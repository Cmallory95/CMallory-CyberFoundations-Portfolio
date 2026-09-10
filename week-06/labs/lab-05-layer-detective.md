# Week 6 Lab 05 — Layer Detective

**Student Name:** Chantel Mallory

**Date Completed:** 9/9/26

**Module:** 2 — Networking & Cloud Foundations | **Week:** 6  
**Submission Path:** `week-06/labs/lab-05-layer-detective.md`

---

## Overview

**This is a SHORT lab — 20 to 30 minutes — and it needs no VM.** No Cloud Heights session, no simulator, no screenshot. This is a thinking lab: you take the evidence you have already collected in Weeks 5 and 6 and sort it into layers.

This is an **independent** lab.

---

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Environment | This worksheet only — nothing to start, nothing to connect to |
| Prerequisite | Week 5 labs and Week 6 Labs 01–04 |
| Screenshot | None required |

---

## Part A — The Seven-Row Table

Fill in every row. For the last column, name one **real thing you personally saw** in Weeks 5–6 that belongs at that layer.

| # | Layer name | One-line job | Real thing from Weeks 5–6 |
| --- | --- | --- | --- |
| 7 | Application | The actual application you interact with | Outlook email |
| 6 | Presentation | Formatting and decoding info so the other side can read it | Encryption  |
| 5 | Session | Starting, holding and ending a convo | Packet inspector |
| 4 | Transport | The conversation in between machines | TCP handshake. TCP v UDP |
| 3 | Network | Communication between unfamiliar networks | Default gatway |
| 2 | Data Link | Delivers local messages across one hop | MAC address  |
| 1 | Physical | Actual energy being used | Energy running through fiber cabling |

---

## Part B — Case Files

For each case, name the layer where the problem lives, and name the evidence proving the layers **below** it were already working.

### Case File 1 — The Name That Went Nowhere

A hostname lookup fails, but pinging the machine's IP address directly succeeds.

Layer:

```
Layer 2: Data link
```

Evidence that the layers below were working:

```
pinging the machine's IP hostname and address are still local to the host network. Getting a successful ping to the IP address means that data is successfully going locally across one hop.
```

### Case File 2 — Permission Denied

`ssh` to a host returns `Permission denied` after a password prompt.

Layer:

```
Layer 4: Transport and Layer 7: Application

```

Evidence that the layers below were working:

```
The issue can be tied to 4 if there is an issue during the TCP handshake or if data packets are lost during transmission. 

The actual permission denied prompt will occur on the presentation layer itself in the app on the login screen
```

### Case File 3 — The Cable Story

A machine reports no link on its interface and has no address at all.

Layer:

```
Layer 1: Physical
```

Evidence and reasoning:

```
If it is showing no link and has no address at all the problem is probably a cable. If a cable is loose or or bad especially one linked to the router it can cause network issues to the machine which can cause an IP to not show up.
```

### Case File 4 — Ping Works, The Page Does Not

`ping` to a server succeeds, but `curl http://<that server>` returns nothing useful.

Layer:

```
Layer 7: Application
```

Evidence that the layers below were working:

```
If ping succeeds but the curl command does nothing useful the issue is probably with DNS. The domain may not have resolved correctly. 
```

### Case File 5 — Wrong Neighbourhood

A machine has an address, but its default route points somewhere that cannot forward its traffic.

Layer:

```
Layer 3: Network
```

Evidence and reasoning:

```
Layer 3 has to do with communicating across disparate networks. There maybe an issue with the endpoint that the host is trying to communicate with. 
```

---

## Part C — The Silent Gateway Case

In Lab 03 the Azure default gateway did not answer your ping. However, your VM had a valid default route configured, and your local communication with the Grid Beacon — the ping replies, the HTTP banner, and `TRACE ID: CF-NET-0604` — succeeded.

A failed gateway ping is one piece of evidence — not automatically proof of a gateway or network failure. But the evidence you weigh against it has to be the right kind of evidence.

The Grid Beacon at `10.60.6.4` sits on the same local subnet as your VM (`10.60.6.0/26`). Reaching it proves **local-subnet connectivity** — that traffic never crosses the default gateway, so beacon success alone cannot prove the gateway forwarded anything. Your `ip route` output proves a **default route is configured** — your VM knows where it intends to send non-local traffic — but it does not prove the gateway forwarded that traffic. The evidence that demonstrates the **default path is functioning** is successful communication with a destination outside `10.60.6.0/26`, such as the outbound internet access through NAT that you examined in Lab 04.

### Step 1 — Rule on the Case

Is the failed gateway ping enough evidence to declare a network-layer failure? Explain your answer using the other evidence you collected. In your response, distinguish between:

- evidence that proves **local-subnet connectivity**
- evidence that proves a **default route is configured**
- evidence that supports **successful off-subnet connectivity**

```
Local subnet connectivity is proven by pinging the Beacon grid, is on a local subnet of my machine so reaching it helps proves you have local connectivity. Conducting ip route and seeing your default gateway proves a route is configured for non local traffic. Seeing the default path functioning means that you have successful off-subnet connectivity because that default is to send and receive non local info.
```

### Step 2 — Name the Correct Conclusion

For each of these four results, state what it actually proves: the Grid Beacon at `10.60.6.4` answering, the default route shown by `ip route`, a successful connection to a destination outside your local subnet, and the gateway's failed ping. Then state the rule you would give a junior colleague about the difference between an observation ("the gateway did not answer my ICMP probe") and a diagnosis ("the gateway is broken"):

```
The Grid Beacon answering proves it is reachable, while `ip route` proves a default route is configured. A successful connection outside the local subnet proves traffic can leave the local network and reach that destination. A failed gateway ping only proves the gateway did not respond to the probe, not that it is broken. For a JR I would distinguish observations from diagnoses—“the gateway did not answer” is an observation; “the gateway is broken” requires more evidence.
```

---

## Part D — Two Models, One Job

The OSI model has seven layers. The practical TCP/IP model most engineers speak day to day has four or five.

### Step 1 — Map Them

Briefly show how the seven OSI layers collapse into the practical model:

```
The practical TCP/IP model

- Application:** OSI Layers 5–7 → Application
- Transport:** OSI Layer 4 → Transport
- Internet:** OSI Layer 3 → Internet
- Network Access:** OSI Layers 1–2 → Network Access

```

### Step 2 — When Each Is Useful

Explain when the seven-layer vocabulary helps and when the practical model is the better tool:

```
The 7-layer OSI model is helpful when you need precise troubleshooting or want to identify exactly where a problem occurs. The practical TCP/IP model is better for everyday networking because it reflects how modern networks actually operate and is simpler to use.

```

---

## Analysis Questions

**Analysis Question 1.** Explain the Ladder Rule using layer language. What does "test the near thing first" mean when the rungs are layers? *(Minimum 3 sentences.)*

```
The Ladder Rule means troubleshoot from the lowest rung (most simplest troubleshooting),re moving higher and getting more advance. Don’t jump to an Application-layer diagnosis when a lower-layer problem hasn’t been ruled out. You can save yourself time and avoid causing a bigger issue. 
```

**Analysis Question 2.** Why is "which layer is this?" a faster question than "what is broken?" when you are under pressure? *(Minimum 3 sentences.)*

```
“Which layer is this?” is faster because it narrows the problem before you start guessing at causes. Instead of asking “what is broken?” and considering dozens of possibilities, you identify the layer involved and focus your tests there. This gives you a **structured troubleshooting path** and prevents jumping to conclusions.

```

**Analysis Question 3.** Pick one case file from Part B and describe the very next command you would run to confirm your ruling, and what result would change your mind. *(Minimum 2 sentences.)*

```
Case file 3: The first prompt I would start with is a ping to check the local connection. If I got no response at all or if the screen was blank my first instant would be to check the cabling. I would ensure everything is plugged in correctly both on the computer/CPU and the router. The command that would make me change my mind is if I ran ping and got anything other than 100% packet loss.
```

---

## Submission Checklist

- [ ] All seven rows of the OSI table completed with a real Week 5–6 anchor each (Part A)

- [ ] All five case files given a layer and supporting evidence (Part B)

- [ ] Silent gateway case ruled on correctly (Part C)

- [ ] OSI vs. practical TCP/IP model compared (Part D)

- [ ] All three Analysis Questions answered (minimum sentence counts met)

- [ ] No screenshot required for this lab

- [ ] This file is committed to your portfolio repo at `week-06/labs/lab-05-layer-detective.md`

---

## GitHub Commit Subsection

1. Open **Week 6 → Lab 05: Layer Detective** in the Lab Portal.
2. Fill in the worksheet fields.
3. Click **Submit to GitHub** — the Portal commits to `week-06/labs/lab-05-layer-detective.md`.

---

*CyberVisionaries Institute · Cyber Foundations · Tier I*
