# Lesson 2 Notes - Slack Alerts - Ben Tucker

Server: launched from my golden AMI `bank-golden-1031`, t3.small, Instance ID `i-0e24b59a6e350de86` (Sat 11/7/2026).
Workspace: `https://gohansirfoundations.slack.com` · channel `#balerica-alerts` · app `Balerica AI`.
The webhook URL is a secret, so it is only in `.env` on the server and in my private notes, never here.

## Screenshots (in `screenshots/`)
- `lesson2_slack_alerts.png`: `#balerica-alerts` with 7 different homework alerts (HW-002, HW-003, HW-007, HW-004, HW-005, DRILL-9631, DRILL-8071)
- `lesson2_hw7_terminal.png`: ID line + `hw7_morning_report.py`
- `lesson2_experiments.png`: experiments 1-4 in one terminal

## Lab: slack.py test
- `python slack.py --test` → `SUCCESS: Chewbacca approves!` and TEST-001 arrived in `#balerica-alerts`.

## Homework scripts

### hw1_hello_slack.py
- Put my name in `MY_NAME`, ran it → `Delivered!` → HW-001 "Hello from Ben Tucker!" arrived in Slack.

### hw2_severity_tour.py
- Output: INFO, WARNING, ERROR and CRITICAL → all `DELIVERED to Slack`.
- **Which severities arrived, and why?** All 4 arrived because my threshold was INFO, which is the lowest level, so it lets everything through.

### hw3_server_time.py
- Sent HW-003 with the server's time in UTC.
- **How many hours apart are server time and my wall clock?** 7 hours apart, and the server is ahead. I'm in Mountain Time, which is UTC-7 now that daylight saving ended on 11/1 (it was 6 hours during my Lesson 1 runs).

### hw4_disk_watch.py
- My code measured the disk and chose the severity itself: under 75% = INFO, 75%+ = WARNING, 90%+ = ERROR. My disk was about 26% used, so it sent INFO (HW-004). Actual run: `Disk is 25.5% used -> severity INFO.` → `Delivered!`
- **Why is a quiet INFO here good design, not a failure?** A quiet INFO means no false alarms. If the monitor yelled when nothing was wrong, people would start ignoring it, like a smoke alarm that goes off every time you make toast, and then they'd miss a real fire.

### hw5_load_check.py
- Load average = how much work is waiting in line for the CPUs (1, 5 and 15 minute averages). My server has 2 CPUs, so the script only warns if the 5-minute load goes over 2. Mine was near 0, so it sent INFO (HW-005). Actual run: `Load: 0.00 / 0.00 / 0.00 on 2 CPU(s) -> INFO` → `Delivered!`
- **What would make this number spike on the Balerica bank?** Lots of payments at once, like a big burst of SWIFT messages (what `swift/traffic_generator.py` simulates) or many people checking balances at the same time.

### hw6_incident_drill.py
- Ran it a few times. Each run picks a random fake (DRILL) incident and sends it with a DRILL-#### ID. Everything is synthetic, no real customer data. I drew `DRILL-9631: [ERROR] payment_post returned code 8: business rejection rate above 5%` and `DRILL-8071: [WARNING] SWIFT traffic generator queue depth climbing: 42 messages waiting`, both delivered.
- **Which fake incident deserves to wake a human at 3 AM?** The CRITICAL one: `account_lookup` returned code 12, ledger file not reachable. If the ledger is gone, nobody can find the money, so balances and payments stop working. The WARNINGs (a late batch job, a growing SWIFT queue) and the INFO backup can wait until morning.

### hw7_morning_report.py
- Ran it right after the ID line (screenshot `lesson2_hw7_terminal.png`):
  - `ec2-user` · `Sun Nov  8 09:47:50 MST 2026` · `instance-id: i-0e24b59a6e350de86` · boot `2026-11-08 00:20:02` (UTC)
  - Report: time `2026-11-08 16:48:19 UTC`, host `ip-172-31-29-64.ec2.internal (Linux x86_64)`, Python 3.9.25, disk 25.5% used, load (1m) 0.00 → `Delivered!` (HW-007)
- hw7 combines hw3 (time), hw4 (disk) and hw5 (load) into one daily status report, like the morning news for the server.

## Experiments

### Experiment 1: SLACK_ALERT_SEVERITY=CRITICAL, rerun hw2
- **My prediction (before running):** Only CRITICAL will arrive, because it's the loudest, and the threshold only lets in alerts at or above CRITICAL.
- **Result:** My prediction was right. INFO, WARNING and ERROR said `skipped by your threshold`, and only CRITICAL said `DELIVERED to Slack`.
- **Why:** the script checks the level against my `.env` threshold **before** it sends anything. CRITICAL (3) is the only level that is at least as loud as CRITICAL (3). This is how a team keeps a busy channel quiet and only gets the emergencies.

### Experiment 2: SLACK_ALERTS_ENABLED=false, rerun hw1
- **Result:** nothing got through. The mute is on. hw1 printed `Skipped. Check .env: SLACK_ALERTS_ENABLED must be true...` and nothing arrived in Slack.
- **Why a kill switch helps during maintenance:** During planned maintenance the bank goes down on purpose, so muting alerts:
  - prevents alert fatigue (people don't learn to ignore the channel)
  - avoids false incidents for something everyone already planned
  - keeps uptime records clean (planned downtime isn't counted as an outage)
  - stops automated reactions (no bots opening tickets or restarting things while engineers are working)
  - and it's one setting in `.env`, so no code has to change. Just remember to turn it back on after.

### Experiment 3: hw4 WARNING threshold 75 → 20
- Changed line 18 of `hw4_disk_watch.py` from `elif percent_used >= 75:` to `elif percent_used >= 20:` and checked it with `grep -n`.
- **Result:** Yes, the severity changed from INFO to WARNING: `Disk is 25.5% used -> severity WARNING.` → `Delivered!`. The disk didn't change at all, only my rule did. The code decides what counts as a problem, so picking the right threshold matters.
- Screenshot: `lesson2_experiments.png` (all experiments in one terminal).

### Experiment 4: put everything back
- Set `SLACK_ALERTS_ENABLED=true` and `SLACK_ALERT_SEVERITY=INFO`, then checked with `grep -E "ENABLED|SEVERITY" .env`, which showed both lines back to normal.

## Mistakes I made
- I answered the hw4, hw5 and hw6 questions but forgot to actually run those scripts at first. My Slack screenshot showed HW-004, HW-005 and DRILL were missing, so I ran them. Lesson: check the evidence, not just my memory.
- My webhook leaked in chat twice (11/3 and 11/7) from pasting in the wrong spot. Both times I removed it in Slack, made a new one, and checked the old one was dead (404 `no_service`).
- `sed` without a line number changed every `chewbacca` in `slack.py`, including Theo's bite check, so Chewbacca bit my real URL. Fix: fresh copy, then `sed -i '13s/...'` (only line 13).
- Typed `chmod` while nano was still open, so `.env` got `chttps://` and lost 3 lines. `cut -c1-45 .env` showed the problem without showing the secret. Fixed with `sed` and `printf >>`.
- Ghostty's `xterm-ghostty` stopped nano from opening. Fix: `export TERM=xterm-256color`.
