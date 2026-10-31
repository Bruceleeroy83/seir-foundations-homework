# Module 1, Lesson 1: Balerica Legacy Bank on EC2

**Student:** Ben Tucker (Bruceleeroy83)
**Runbook:** [BalericaAI/SEIR-Foundations, module1/lesson1_ec2/API/runbook.md](https://github.com/BalericaAI/SEIR-Foundations/blob/main/module1/lesson1_ec2/API/runbook.md)

Each run is a brand-new t3.small instance in us-east-1, with Steps 0–4 done by hand. The ID line runs right before uvicorn, and every instance is terminated after its run.

## Lab runs

| Run | Date | Time zone | Instance ID | Boot time (UTC) | Public IPv4 DNS | Screenshot | Terminated? |
|---|---|---|---|---|---|---|---|
| 1 | 2026-10-30 | America/Denver | i-007852c4250891a7b | 2026-10-31 01:05:54 | ec2-32-201-83-46.compute-1.amazonaws.com | [lab1_run1.jpg](screenshots/lab1_run1.jpg) | Yes |
| 2 | 2026-10-30 | America/Denver | i-0436e11348ec308a8 | 2026-10-31 01:31:00 | ec2-174-129-155-38.compute-1.amazonaws.com | [lab1_run2.jpg](screenshots/lab1_run2.jpg) | Yes |
| 3 | 2026-10-30 | America/Denver | i-062a1c9de9b6caa3c | 2026-10-31 01:46:36 | ec2-23-22-179-201.compute-1.amazonaws.com | [lab1_run3.jpg](screenshots/lab1_run3.jpg) | Yes |
| 4 | 2026-10-31 | America/Denver | i-06f264cfbfa19000e | 2026-10-31 17:02:21 | ec2-3-80-51-178.compute-1.amazonaws.com | [lab1_run4.jpg](screenshots/lab1_run4.jpg) | Yes |
| 5 | | | | | | | |
| 6 | | | | | | | |
| 7 | | | | | | | |

## COBOL core practice

Done on Run 3 (`i-062a1c9de9b6caa3c`) in `/opt/balerica-bank`. Details are in `lab1_notes.md`.

| Screenshot | What it shows |
|---|---|
| [cobol_rc0.jpg](screenshots/cobol_rc0.jpg) | RC 0: `account_lookup 100001` found Alice Johnson |
| [cobol_rc4.jpg](screenshots/cobol_rc4.jpg) | RC 4: `account_lookup 999999`, ACCOUNT NOT FOUND |
| [cobol_rc8.jpg](screenshots/cobol_rc8.jpg) | RC 8: `payment_post TXN-BT-0004` for 0.00, REJECTED |
| [cobol_rc12.jpg](screenshots/cobol_rc12.jpg) | RC 12: ran from the wrong folder, CANNOT OPEN data/accounts.dat |
| [cobol_ledger_decode.jpg](screenshots/cobol_ledger_decode.jpg) | Alice's balance decoded with `cut -c45-56` |

## Git

[git_clone.jpg](screenshots/git_clone.jpg): cloned this repo onto Run 3 and listed `module1`. Clone only, no push and no token on the server.

## My notes

- I've learned that if you just listen to Theo and do exactly what he tells us to do, we will be fine. I do get it though: one thing wrong and it won't work.
