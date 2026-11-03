# Lab 1 Notes: Balerica Legacy Bank on EC2

**Student:** Ben Tucker (Bruceleeroy83)
Every run: brand-new t3.small in us-east-1 from my launch template `seir-foundations-lab0`, SSH from my Mac, Steps 0–4 by hand, ID line right before uvicorn, then terminate.

## Run 1: i-007852c4250891a7b
Run 1 was my first homework run after a week of practice reps, on Fri 10/30. My first ID-line screenshot cut off `whoami` at the top, so I retook it with the whole output showing. I posted my first payment with my own initials, `TXN-BT-0001`, and it came back POSTED. I learned that Theo grades the screenshot by the ID line, so every piece of it has to be visible.

Exact output only this run had:
```
INFO:     Started server process [35348]
```

## Run 2: i-0436e11348ec308a8
Run 2 was the second run on Fri 10/30, and it went clean with nothing covering the screenshot. The boot time from `uptime -s` (01:31:00 UTC) matched the launch time in the AWS console, which proved the screenshot came from a fresh server. I learned that `uptime -s` shows boot time in UTC, which is 6 hours ahead of Denver, so the date can look like "tomorrow."

Exact output only this run had:
```
INFO:     Started server process [34950]
```

## Run 3: i-062a1c9de9b6caa3c
Run 3 was the third run on Fri 10/30, so that was my limit for the day. I did all my COBOL practice on this server (see the COBOL practice section below) and cloned my own homework repo to prove Git works (clone only, no token, no push). I learned that the COBOL program only works from inside `/opt/balerica-bank`, because it looks for `data/accounts.dat` from the folder I'm standing in.

Exact output only this run had:
```
INFO:     Started server process [35050]
```

## Run 4: i-06f264cfbfa19000e
Run 4 was Sat 10/31 morning and took about 33 minutes. I typed `<paste-the-IP>` literally into my ssh command, and zsh gave me a parse error; then I typed the example IP by mistake before using my real one. In `/ask` I typed the key twice (`"question": "question": …`), got a **422 JSON decode error "Expecting ',' delimiter"**, fixed it, and got a 200. I learned that SWIFT payments go through the API and change the database balance (Alice went from 5000.00 to 4147.69), but COBOL `payment_post` only writes the journal.

Exact output only this run had:
```
INFO:     Started server process [35235]
```

## Run 5: i-0e7af006c53f26971
Run 5 was done live in Theo's Saturday class on 10/31 and posted `TXN-BT-0006`. My first ID-line shot was cut off on the left and I ran `uptime` without `-s`, so I retook it. I pressed Ctrl+C in Tab 1 to retake it, and then Tab 2's curl said "Could not connect to server" until I restarted uvicorn, because Ctrl+C closes the bank. SWIFT gave 7 POSTED / 3 REJECTED / 0 FAILED, and Alice's balance was 5000 − 86.15 − 135.87 = 4777.98, which matched `GET /accounts/100001`. From Theo I learned that COBOL accepts a duplicate TXN ID and stores money in cents (`12550` = 125.50).

Exact output only this run had:
```
INFO:     Started server process [36518]
```

## Run 6: i-0d97cc1cfb2b765cb
Run 6 was Sat night 10/31, my third run that day. My ssh command split into two lines, so ssh printed its `usage:` help and zsh said `command not found: ec2-user@54.159.63.55`. The same thing happened to `payment_post`: COBOL got only 2 of its 5 fields and answered `STATUS=REJECTED` / `REASON=MALFORMED REQUEST`, and `payments.dat` stayed empty, so I retyped it on one line and it POSTED. I ran the good payment twice and saw two `TXN-BT-0007` lines in the journal, which proved what Theo showed: COBOL does not block duplicate IDs. SWIFT gave 10 POSTED / 0 REJECTED / 0 FAILED for the first time, and Alice went to 5000 − 287.96 − 321.39 = 4390.65.

Exact output only this run had:
```
INFO:     Started server process [35588]
```

## Run 7: i-0408dca3d830ebca6
Run 7 was Mon 11/2 at night, my last run and my third different day (10/30, 10/31, 11/2). This time I typed the long `payment_post` command myself on one line, ran it only once, and got exactly one journal line for `TXN-BT-0008`. I used my new Ghostty terminal, and `clear` failed with `'xterm-ghostty': unknown terminal type` because the server didn't know Ghostty's terminal name, but the ID line still ran right before uvicorn. I learned that the time zone changed from MDT to MST on 11/1, so Denver is now 7 hours behind UTC instead of 6.

Exact output only this run had:
```
INFO:     Started server process [35222]
```

## COBOL practice
Done on **Run 3, i-062a1c9de9b6caa3c**, in `/opt/balerica-bank`.

| Return code | Command | Exact output |
|---|---|---|
| 0 success | `./cobol/bin/account_lookup 100001` | `NAME=Alice Johnson`, `BALANCE=5000.00`, `echo $?` → `0` |
| 4 not found | `./cobol/bin/account_lookup 999999` | `ERROR=ACCOUNT NOT FOUND`, `echo $?` → `4` |
| 8 business rejection | `./cobol/bin/payment_post TXN-BT-0004 100001 0.00 USD INV-1004` | `STATUS=REJECTED`, `echo $?` → `8` |
| 12 environment failure | ran it from `~` instead of `/opt/balerica-bank` | `CANNOT OPEN data/accounts.dat` (FILE-STATUS=35), `echo $?` → `12` |

**Alice's balance decoded by hand:** `cut -c45-56 data/accounts.dat` printed `000000500000`. The last two digits are cents, so that is **$5,000.00**.

**My journal lines:**
```
TXN-BT-0003         100001000000012550USDPOSTED  INV-1003
TXN-BT-0004 ... REJECTED INV-1004
```

**Did the balance change?** No. After posting `TXN-BT-0003` for 125.50, the ledger still said `000000500000`. `payment_post` writes the payment to the journal (`payments.dat`), but it does not update the ledger (`accounts.dat`).

## Mistakes I made
- Typed the placeholder `<paste-the-IP>` literally, then typed the example IP instead of my real one.
- Long commands split into two lines (ssh and `payment_post`) because pasting added a hidden Enter.
- Typed a JSON key twice in `/ask` → 422 error.
- Pressed Ctrl+C in Tab 1, which stops the bank.
- Ran `uptime` without `-s`, and let the left edge of my screenshot get cut off.
- Ran the same payment twice by accident (duplicate TXN ID).
- Typed the screenshot filename `lab1_run7.jpg` as the instance Name tag instead of `lab1-run7`.
- In practice: ran `curl 127.0.0.1` on my Mac and in Chrome. It only works inside the server.

## Commands I now know by heart
```
ssh -i ~/.ssh/seir-lab0-key.pem ec2-user@<server-ip>
whoami
pwd
sudo dnf install -y git
git clone https://github.com/BalericaAI/SEIR-Foundations.git
cd /opt/balerica-bank
./cobol/bin/account_lookup 100001
echo $?
cat data/payments.dat
source venv/bin/activate
whoami && TZ='America/Denver' date && ec2-metadata -i && uptime -s
uvicorn app.main:app --host 0.0.0.0 --port 8000
curl http://127.0.0.1:8000/status
python3 swift/traffic_generator.py --count 10
```
