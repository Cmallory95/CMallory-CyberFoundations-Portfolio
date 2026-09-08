# Week 6 Lab 04 — Reading the Blueprints

**Student Name:**

**Date Completed:**

**Module:** 2 — Networking & Cloud Foundations | **Week:** 6  
**Submission Path:** `week-06/labs/lab-04-reading-the-blueprints.md`

---

> ### 🔒 Cloud Heights Security Rule
> Your Bastion link and Cloud Heights password are **private access credentials**. Never paste either into a worksheet, screenshot, GitHub repository, Circle post, or chat message. When taking screenshots, crop out the browser address bar and all login information.

---

## Overview

**This is a SHORT lab — 15 to 20 minutes.** It is deliberately small. You already have the commands; this lab is about matching a drawing to reality.

The **Cloud Heights Network Blueprint** is displayed at the top of this lab page in the portal. Everything you write about the network's architecture comes from that blueprint or from your own machine — never from a guess.

---

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Environment | Cloud Heights — live Ubuntu 22.04 VM, reached through Azure Bastion |
| Source of truth | The Cloud Heights Network Blueprint shown at the top of this lab page |
| Commands used | `ip addr`, `ip route` |
| Known value | Student subnet: **`10.60.6.0/26`** |

---

## Part A — Read the Drawing

### Step 1 — Record the Architecture Values

From the blueprint at the top of this page, record each value **exactly as drawn**. If a value is not shown on the blueprint, write "not shown on blueprint" — do not guess.

| Item | Value from the blueprint |
| --- | --- |
| VNet name | vnet-cf-labs |
| VNet address space | 10.60.6.0/24 |
| Student subnet range | 10.60.6.0/26 |

---

## Part B — Verify Against Your Own Machine

### Step 1 — Confirm Your Address Lives in the Subnet

Run `ip addr` and find your private IPv4 address.

Command and output:

```
10.60.6.27/26
```

Your private IP:

```
127.0.0.1/8
```

Explain how you know your address falls inside `10.60.6.0/26` — what range does that prefix actually cover:

```
I know my address falls inside this range because they are on the same street "10.60.6" my exact door is 27. I believe this range covers all of the ip address 10.60.6.0-10.60.6.27
```

### Step 2 — Confirm Route Behaviour

Run `ip route`.

Command and output:

```
ip route

default via 10.60.6.1 dev eth0 proto dhcp src 10.60.6.27 metric 10
0 
10.60.6.0/26 dev eth0 proto kernel scope link src 10.60.6.27 metri
c 100 
10.60.6.1 dev eth0 proto dhcp scope link src 10.60.6.27 metric 100
 
168.63.129.16 via 10.60.6.1 dev eth0 proto dhcp src 10.60.6.27 met
ric 100 
169.254.169.254 via 10.60.6.1 dev eth0 proto dhcp src 10.60.6.27 m
etric 100 
```

What the default route tells you about traffic that is not destined for your own subnet:

```
This shows that any traffic that is not destined for my own subnet will go through default gateway 10.60.6.1
```

### Step 3 — Capture Your Evidence

**Required filename:** `blueprint-verified.png`

This must be **your own `ip addr` and `ip route` output** — not a re-screenshot of the blueprint. Crop out the address bar and any login information.

---

## Part C — How Traffic Actually Moves

### Step 1 — No Public IP

Your VM has a private address and **no public IP**. Explain what that means for who can reach it directly from the internet:

```
This means that nothing from the internet can contact my machine directly it would have to go through the default gateway of the basion link to my VM. Having a private address and no IP means it is not visible on the open internet. 
```

### Step 2 — Outbound vs. Inbound

Outbound internet traffic from your VM leaves through address **translation (NAT)**. Inbound access for you arrives through **Azure Bastion**, not through a public address on the VM.

Explain both directions in your own words:

```
outbound traffic leaving from the NAT means it has been translated for the outside world aka it will have a public IP like other internet connections such as a website. Inbound access arrives through the Azure station aka the front desk person and is routed where go from there. Since the VM does not have or use public ip addresses, incoming info from a public address would not be able to make it to my VM they would not be able to see or connect to it.
```

### Step 3 — The Guard Post You Do Not Touch Yet

Each student machine sits behind its own **network security group** — a per-student guard post that decides what traffic is allowed in.

**In Week 6 you do not configure it.** Week 7 is when you take control of those rules.

Write one sentence naming what the guard post does and one sentence stating what you are *not* doing with it this week:

```
The guard post sits and decides what outbound traffic is allowed in to reach your VM. This week we are not configuring security groups!
```

---

## Analysis Questions

**Analysis Question 1.** Why would an organization put every student machine in one small subnet instead of giving each machine a public address? *(Minimum 3 sentences.)*

```
Giving a machine a public address means it can be reached by the internet and outbound traffic can flow to it. Having a small subnet keeps those machines on the same network but uses segmentation to break it off from the main network IP. This increases overall security while helping to keep student VM IP addresses organized.
```

**Analysis Question 2.** Segmentation means separating a network into parts that cannot freely reach each other. Give one concrete benefit of segmentation during a security incident. *(Minimum 3 sentences.)*

```
Having segmentation allows you contain the problem in one area before it spreads to another. This is helpful when you are against the clock for malware or a virus that self replicates or spreads quickly in other ways. This also keeps critical systems safe as well, having them segmented makes it harder for the systems to be targeted in the event of a breach. 
```

**Analysis Question 3.** A diagram and a live machine disagree about an address range. Which do you trust, what do you do next, and why? *(Minimum 2 sentences.)*

```
I would look at the diagram and see what the outline was first for the IP address and compare it to the computer. Since the IP addresses are systematically mapped out I would side more with the diagram but would be sure to double check overall. 
```

---

## Submission Checklist

- [ ] VNet name, address space, and subnet range recorded from the blueprint (Part A)

- [ ] `ip addr` run and own private IP confirmed inside `10.60.6.0/26` (Part B, Step 1)

- [ ] `ip route` run and default route behaviour explained (Part B, Step 2)

- [ ] `blueprint-verified.png` captured from your own terminal, cropped, uploaded to `assets/screenshots/week-06/` (Part B, Step 3)

- [ ] Private address / NAT / Bastion explained (Part C, Steps 1–2)

- [ ] Per-student guard post identified — and explicitly not configured this week (Part C, Step 3)

- [ ] All three Analysis Questions answered (minimum sentence counts met)

- [ ] This file is committed to your portfolio repo at `week-06/labs/lab-04-reading-the-blueprints.md`

---

## GitHub Commit Subsection

1. Open **Week 6 → Lab 04: Reading the Blueprints** in the Lab Portal.
2. Fill in the worksheet fields and upload `blueprint-verified.png` to `assets/screenshots/week-06/`.
3. Click **Submit to GitHub** — the Portal commits to `week-06/labs/lab-04-reading-the-blueprints.md`.

---

*CyberVisionaries Institute · Cyber Foundations · Tier I*
