# Module 1, Lesson 2: Slack Alerts from EC2

**Student:** Ben Tucker (Bruceleeroy83)
**Homework:** [BalericaAI/SEIR-Foundations, module1/lesson2_slack/homework](https://github.com/BalericaAI/SEIR-Foundations/tree/main/module1/lesson2_slack/homework)

The server was launched from my golden AMI `bank-golden-1031` (t3.small, us-east-1, `i-0e24b59a6e350de86`). I put Theo's `slack.py` in `/opt/balerica-bank`, set my workspace URL, kept the webhook only in `.env` (`chmod 600`, in `.gitignore`), and got `SUCCESS: Chewbacca approves!`. Then I ran all 7 homework scripts with `SLACK_ALERT_SEVERITY=INFO` and did the 4 experiments. The webhook URL is never in this repo or in any screenshot.

## Evidence

| Screenshot | What it shows |
|---|---|
| [lesson2_slack_alerts.png](screenshots/lesson2_slack_alerts.png) | `#balerica-alerts` with 7 different homework alerts: HW-002, HW-003, HW-007, HW-004, HW-005, DRILL-9631, DRILL-8071 |
| [lesson2_hw7_terminal.png](screenshots/lesson2_hw7_terminal.png) | ID line (`ec2-user`, `Sun Nov 8 09:47:50 MST 2026`, `i-0e24b59a6e350de86`, boot `2026-11-08 00:20:02`) + `hw7_morning_report.py` → `Delivered!` |
| [lesson2_experiments.png](screenshots/lesson2_experiments.png) | Experiments 1–4: CRITICAL threshold, kill switch, hw4 threshold 75 → 20, settings put back |

## Scripts

| # | Script | Result |
|---|---|---|
| 1 | `hw1_hello_slack.py` | "Hello from Ben Tucker!" delivered (HW-001) |
| 2 | `hw2_severity_tour.py` | all 4 severities delivered at INFO (HW-002) |
| 3 | `hw3_server_time.py` | server time in UTC delivered (HW-003) |
| 4 | `hw4_disk_watch.py` | 25.5% used → INFO (HW-004) |
| 5 | `hw5_load_check.py` | 0.00 / 0.00 / 0.00 on 2 CPUs → INFO (HW-005) |
| 6 | `hw6_incident_drill.py` | DRILL-9631 [ERROR] and DRILL-8071 [WARNING] |
| 7 | `hw7_morning_report.py` | morning report delivered (HW-007) |

## My notes

Answers to each script's "In your notes" question, experiment 1 prediction vs. result, and my mistakes are in [lesson2_notes.md](lesson2_notes.md).

After the homework I made the `bank-with-slack` AMI (private) and terminated the instance.
