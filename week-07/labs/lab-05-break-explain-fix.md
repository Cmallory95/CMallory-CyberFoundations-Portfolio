# Week 7 Lab 05 — Break It, Explain It, Fix It

**Student Name:**

**Date Completed:**

**Module:** 2 — Networking & Cloud Foundations | **Week:** 7  
**Submission Path:** `week-07/labs/lab-05-break-explain-fix.md`

> ## Cloud Heights Protected-Rules Safety Rule
> Four baseline rules are protected: **100** (`allow-ssh-from-bastion`), **110** (`allow-icmp-intra-vnet`), **120** (`deny-ssh-student-subnet`), and **1000** (`deny-tcp8080-student-subnet` — Inbound Deny TCP from `10.60.6.0/26` to port `8080`). **You never modify, delete, replace, or use a protected rule as a troubleshooting target.** Create or edit student rules only in priorities **200–999**. The priority **1000** fallback deny sits after your band on purpose, so a narrower Allow you create in 200–999 is evaluated first. A mistake in your student range is recoverable and is not a grading penalty when you diagnose it honestly.

> **Evidence safety:** Never include a Cloud Heights password or Bastion shareable URL. Crop browser address bars and login information before committing screenshots.

---

## Mission

Create a controlled priority failure inside your student range, diagnose it from evidence, remove the problem, and retest. The goal is method: UNDERSTAND → PREDICT → CHANGE → TEST → VERIFY, then FORECAST → EXECUTE → VERIFY → REMEDIATE → RETEST.

## What You Already Know

A network security rule is a decision about traffic. Rules are evaluated from the lowest priority number to the highest, and the first matching rule wins. Inbound and outbound traffic use separate ledgers. A configured service, a security rule, a test result, and an evidence screenshot answer different questions.

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Required setup | Lab 03 narrow Allow present; listener running |
| Safe failure | Student-created Deny only; all four protected baselines (100, 110, 120, 1000) untouched |
| Recommended temporary rule | Priority 250 Deny TCP 8080 from `10.60.6.4` |
| Time | 45–55 minutes |

- [ ] I am using my assigned `cf-student-XX` VM through the CyberFoundations Lab Portal.

- [ ] The VM shows **Running**.

- [ ] I can identify the four protected baseline rules at priorities 100, 110, 120, and 1000.

- [ ] I understand that my editable priority range is 200–999.

### Cloud Heights Idle Stop

Cloud Heights may warn you that the VM is idle. Return to the Lab Portal and choose **I'm still working** if you are active. If the VM is stopped or deallocated, it was not deleted: restart it from **My Lab Environment**. Your disk files and saved configuration remain.

## UNDERSTAND

Your working Allow is expected at priority 300 or another student value. A new matching Deny with a lower number is evaluated first and makes the later Allow unreachable for that traffic.

## Predict First — FORECAST

Predict Grid Beacon's verdict after adding a priority 250 inbound Deny for TCP 8080 from `10.60.6.4`, while leaving the working Allow in place.

```text
Grid Beacon 10.60.6.4 was denied after adding the 250 inbound deny for TCP 8080 from 10.60.6.4
```

## CHANGE / EXECUTE

### Step 1 — Create the Temporary Fault

Create a student rule named `deny-grid-beacon-8080-test`:

- priority `250`
- Inbound / Deny / TCP
- source `10.60.6.4`; source port Any
- destination your assigned VM/default; destination port `8080`
- description: intentional Week 7 troubleshooting fault

Do not edit or delete the Lab 03 Allow. Do not touch the protected priorities 100, 110, 120, or 1000 — the priority 1000 `deny-tcp8080-student-subnet` fallback stays exactly as it is throughout this lab.

### Step 2 — Capture the Broken Ledger

Capture both the priority 250 Deny and the later Allow in the same ordered rule view.

## TEST / VERIFY

Wait at least 10 seconds, select Grid Beacon, and run **Test My Rule**. Expected verdict: `DENIED`.

```text
Result was denied and yes it matched my prediciton.
```

### Stop & Check — Diagnose Before Fixing

Confirm these healthy facts before remediation:

- VM is Running.
- Python listener is still active on 8080.
- The original Allow is still present and correctly scoped.
- The temporary Deny is evaluated first.

```text
I went to my lab environment and the screen confirmed the VM was running and how long it had left before it auto paused. I tested the connection using beacon grid, ran python3 -m http.server 8080 and saw that the VM is active and listening and 8080 is still on.
```

## REMEDIATE

Delete only the temporary `deny-grid-beacon-8080-test` rule you created. Removing the controlled fault returns the ledger to the known-good least-privilege state.

## RETEST

1. Wait at least 10 seconds and retest Grid Beacon: expected `ALLOWED`.
2. Wait at least 10 seconds and retest Other Test Source: expected `DENIED`.

```text
Grid beacon returned back and allowed result and green box
Other source was denied as the same result in Lab 03
```

## Capture Evidence

Your sequence must show broken rules, observed denial, repaired rules, and final retest results.

## Explain — Incident Note

```text
Problem:
Evidence:
Healthy conditions ruled out:
Root cause:
Remediation:
Retest:
Prevention:
```

## Required Evidence

Save screenshots in `assets/screenshots/week-07/`:

- `week07-lab05-broken-rules.png`
- `week07-lab05-observed-denial.png`
- `week07-lab05-fixed-rules.png`
- `week07-lab05-retest-results.png`

Open each image at full size before submission. Confirm that no password, Bastion shareable URL, browser address bar, or unrelated private information is visible.

## Analysis Questions

**Analysis Question 1.** Why did the correct Allow stop working even though it was never edited? (Minimum 4 sentences.)

```text
It stopped working because we added a new rule. Thew new rule we added had a higher priority number. This means the system will use this as an answer before the original 300 priority rule we had set. With 1st match the lowest number wins and the 250 priority rule I set to deny student traffic out weighted the 300 allow rule I made. 
```

**Analysis Question 2.** Why is diagnosing from evaluation order better than changing rules by trial and error? (Minimum 4 sentences.)

```text
Evaluating allows you to take a step back and avoid making mistakes. Mindlessly changing by trial and error can have you make careless mistakes such as leaving a port open or changing it to any and not switching back. Evaluation allows you to get down to the problem and also see why its happening. This increases understanding and helps avoid mistakes.
```

**Analysis Question 3.** What made this failure safe and recoverable in the course environment? (Minimum 3 sentences.)

```text
We always checked before changing anything. Even though this is a course we practiced real skills where you have to be careful to check before and check after a change. Also we did not change any rules we just added and deleted, no alterations. 
```

## Submission Checklist

- [ ] Forecast written before the change

- [ ] Temporary fault stayed in priorities 200–999

- [ ] Broken ledger and DENIED result captured

- [ ] VM, listener, and original Allow checked before remediation

- [ ] Only the temporary Deny removed

- [ ] Grid Beacon retested `ALLOWED` and Other Test Source retested `DENIED`

- [ ] Incident note completed

- [ ] Protected priorities 100, 110, 120, and 1000 were not changed.

- [ ] Every rule I created or edited used priority 200–999.

- [ ] No password, Bastion URL, or browser address bar appears in my files.

- [ ] This worksheet is committed to `week-07/labs/lab-05-break-explain-fix.md`.

## GitHub / Lab Portal Submission

1. Open **Week 7 → Lab 05: Break It, Explain It, Fix It** in the CyberFoundations Lab Portal.
2. Complete every worksheet field and confirm the listed evidence filenames.
3. Upload screenshots to `assets/screenshots/week-07/`.
4. Confirm your portfolio repository is connected, then choose **Submit to GitHub**.
5. Open the committed worksheet and each image on GitHub to verify formatting, legibility, and redaction.

*CyberVisionaries Institute · CyberFoundations · Tier I*
