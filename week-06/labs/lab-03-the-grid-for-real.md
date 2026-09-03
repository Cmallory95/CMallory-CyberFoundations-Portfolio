# Week 6 Lab 03 — The Grid, For Real

**Student Name:**

**Date Completed:**

**Module:** 2 — Networking & Cloud Foundations | **Week:** 6  
**Submission Path:** `week-06/labs/lab-03-the-grid-for-real.md`

---

> ### 🔒 Cloud Heights Security Rule
> Your Bastion link and Cloud Heights password are **private access credentials**. Never paste either into a worksheet, screenshot, GitHub repository, Circle post, or chat message. When taking screenshots, crop out the browser address bar and all login information.

---

## Overview

In Week 5 you ran `ip addr`, `ip route`, `ping`, and `traceroute` in a simulator that always behaved. Today you run the same toolkit against real cloud infrastructure that does **not** always behave the way the textbook implies — and you learn to tell "broken" apart from "normal."

This is an **independent** lab. It tells you what to accomplish; you choose the commands. Expect about 40 minutes.

---

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Environment | Cloud Heights — live Ubuntu 22.04 VM, reached through Azure Bastion |
| Commands used | `ip addr`, `ip route`, `ping`, `traceroute`, `curl` |
| Known-good target | **Grid Beacon — `10.60.6.4`** |
| Prerequisite | Week 6 Labs 01–02 |

---

## Part A — Where You Actually Are

### Step 1 — Read Your Own Address

Run the command that lists your interfaces and addresses.

Command and output:

```
ip addr

Last login: Wed Sep  2 20:31:49 2026 from 127.0.0.1
analyst@cf-student-08:~$ ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOW
N group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state
 UP group default qlen 1000
    link/ether 60:45:bd:48:a7:f7 brd ff:ff:ff:ff:ff:ff
    inet 10.60.6.27/26 metric 100 brd 10.60.6.63 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::6245:bdff:fe48:a7f7/64 scope link 
       valid_lft forever preferred_lft forever
3: enP63503s1: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP> mtu 1500 qd
isc mq master eth0 state UP group default qlen 1000
    link/ether 60:45:bd:48:a7:f7 brd ff:ff:ff:ff:ff:ff
    altname enP63503p0s2
```

Your private IPv4 address and prefix length:

```
10.60.6.27/26 
```

### Step 2 — Read Your Route

Run the command that shows the routing table.

Command and output:

```
ip route

analyst@cf-student-08:~$ ip route
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

Your default gateway:

```
10.60.6.1
```

### Step 3 — Compare to Week 5

Compare this live Ubuntu output to what the CLI Simulator produced in Week 5. What looks the same, what looks different, and what surprised you:

```
The simulator just gave a simple line per input. The boh share the default gateway and your IP. Ubuntu gives specifics to the VM itself and other additional info. 
```

---

## Part B — The Gateway That Does Not Answer

### Step 1 — Ping the Gateway

Ping the default gateway address you recorded. Let it run a few seconds, then stop it.

Command and output:

```
ping 10.60.6.1
```

### Step 2 — Interpret It Correctly

You almost certainly got **no replies**. In Azure, the platform gateway commonly does not answer ICMP. This is **expected platform behaviour** and by itself proves nothing about whether your machine or network is broken.

Explain why "the gateway did not answer ping" is weak evidence:

```
It is weak evidence because Microsoft's configurations make it so that a ping default gateway does not answer pings. A ping is just a signal that you send out to see if it can reach something when leaving your gateway (neighborhood). With the default gateway being your own way out, it defeats the purpose to ping it, it's still technically a part of your own network (neighborhood).
```

---

## Part C — The Known-Good Target

The **Grid Beacon** at `10.60.6.4` is a machine that is known to be up and known to answer. When your first probe fails, you test against something known-good before you conclude anything.

### Step 1 — Ping the Beacon

```
ping 10.60.6.4
```
Output:

```
PING 10.60.6.4 (10.60.6.4) 56(84) bytes of data.
64 bytes from 10.60.6.4: icmp_seq=1 ttl=64 time=1.14 ms
64 bytes from 10.60.6.4: icmp_seq=2 ttl=64 time=1.09 ms
64 bytes from 10.60.6.4: icmp_seq=3 ttl=64 time=1.10 ms
64 bytes from 10.60.6.4: icmp_seq=4 ttl=64 time=1.11 ms
64 bytes from 10.60.6.4: icmp_seq=5 ttl=64 time=1.08 ms
64 bytes from 10.60.6.4: icmp_seq=6 ttl=64 time=1.11 ms
64 bytes from 10.60.6.4: icmp_seq=7 ttl=64 time=1.10 ms
64 bytes from 10.60.6.4: icmp_seq=8 ttl=64 time=1.20 ms
64 bytes from 10.60.6.4: icmp_seq=9 ttl=64 time=1.21 ms
^C
--- 10.60.6.4 ping statistics ---
9 packets transmitted, 9 received, 0% packet loss, time 8010ms
rtt min/avg/max/mdev = 1.082/1.127/1.211/0.043 ms
```

### Step 2 — Trace the Path

```
traceroute 10.60.6.4
```
Output:

```
 1  grid-beacon.internal.cloudapp.net (10.60.6.4)  2.216 ms * *
analyst@cf-student-08:~$ ^C
```

### Step 3 — Ask the Application

```
curl http://10.60.6.4
```
Output:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-sca
le=1.0">
    <title>GRID BEACON | CVI CyberFoundations</title>
    <style>
        body {
            background: #071426;
            color: #d9f7ef;
            font-family: monospace;
            max-width: 850px;
            margin: 80px auto;
            padding: 30px;
        }
        .beacon {
            border: 1px solid #31d6a6;
            padding: 35px;
        }
        h1 { color: #31d6a6; }
        .label { color: #8ca8ff; }
        .status { color: #31d6a6; }
        .classified {
            margin-top: 30px;
            border-top: 1px solid #31445e;
            padding-top: 20px;
        }
    </style>
</head>
<body>
<div class="beacon">
```

> ### ⚠️ Grid Beacon not responding?
> The Grid Beacon is shared course infrastructure and should normally be available. First, confirm your Cloud Heights VM shows **Running** and that you completed the preceding network checks. Then retry the command once after a minute or two.
>
> If the Grid Beacon still does not respond, **stop this part of the lab and contact your instructor.** Record that the shared service was unavailable; do not treat the result as evidence that your VM or your work is incorrect.
>
> Do not change networking, NSGs, firewall rules, routes, DNS, or any Azure settings to try to reach the beacon.
>
> *Instructor note: a confirmed Grid Beacon outage is an environment issue, not a student error. Affected students may complete this portion of Lab 03 after the service is restored, with no penalty.*

### Step 4 — Record the Application Evidence

The beacon returns a banner and a trace ID. Record exactly what you received:

```
Not sure what part is the Banner:
<h1>GRID BEACON</h1>

    <p><span class="label">NODE:</span> grid-beacon</p>
    <p><span class="label">NETWORK:</span> CVI Training Grid</p>
    <p><span class="label">STATUS:</span>
       <span class="status">ONLINE</span></p>

    <p>
        Network beacon established.<br>
        If you reached this node, your route is operational.
    </p>

    <div class="classified">
        <p>INVESTIGATION CHECKPOINT</p>

        <p>
            Observe the path that brought you here.
            The destination is only part of the story.
        </p>

       Trace ID: <p>TRACE ID: CF-NET-0604</p>
  
```

Explain the difference between what the `ping` proved and what the `curl` proved:

```
Ping checks for connectivity. It makes sure that you will able to connect with whatever you will try to connect with outside of your network. The curl ensures that the network/ service etc you are trying to connect with is accessible and able to be used.
```

### Step 5 — Capture Your Evidence

Two screenshots, both cropped to the terminal only:

**Required filename:** `vm-toolkit-live.png` — your `ip addr` and `ip route` output

**Required filename:** `beacon-reply.png` — your beacon ping/traceroute/curl evidence

---

## Part D — Rewrite the Ladder Rule

Week 5 taught the Ladder Rule: test the near thing before the far thing. Real infrastructure adds a wrinkle — a silent rung is not automatically a broken rung.

Rewrite the Ladder Rule in your own words so that it survives real cloud infrastructure. Your version must include both **route/path evidence** and **a known-good target**:

```
To apply the ladder I would first ping myself at ping 10.60.6.1. Rung two would be to check outside my neighborhood I would ping the grid beacon at traceroute 10.60.6.4 and that being a known good source that I know is up and running would confirm that who I am, where, and who I want to connect with while confirming I have the ability to reach others by pinging a known good target.
```

---

## Analysis Questions

**Analysis Question 1.** Your ping to the gateway failed and your ping to the beacon succeeded. What does that pair of results, taken together, prove about your machine's networking? *(Minimum 3 sentences.)*

```
Ping to the beacon as a known good source confirms my network can connect with other machines outside their network, pinging to the gateway will fail because it is configured that way. The gateway is a door that leads out of your neighborhood not an actual place to go or connect to it transports.
```

**Analysis Question 2.** Why is `traceroute` useful even when `ping` already answered? What extra thing does it show you? *(Minimum 2 sentences.)*

```
Ping more so test the destination to ensure you can get to it and connect. Traceroute gives you more insight into the actual network path the packets took. This can help in the event all of your packets/ info do not make it to the host you are communicating with. You can see exactly where the fail happened.
```

**Analysis Question 3.** A service is unreachable and ping to it succeeds. Where would you look next, and why is "the network is fine" an incomplete answer? *(Minimum 3 sentences.)*

```
If a a service is unreachable but the ping works I would traceroute. Ping would show me that I can technically send info to that host and get a communication back. However if the service comes back successful I would do a traceroute to identify where the failure happened.
```

**Analysis Question 4.** Something already controls what is allowed to reach your machine in Cloud Heights. If you could decide those rules, what would you want to allow, what would you want to block, and who in an organization should get to make that decision? *(Minimum 3 sentences.)*

```
I would have an ACL for any known machines/hosts to be able to reach my machine in cloud heights. I would block any unknown/ unauthorized or unsecure (port 80) connections until authentication was successful. Who would make that decision I would say a SR network admin or myself if I am able to.
```

---

## Submission Checklist

- [ ] `ip addr` output recorded and own private IP/prefix identified (Part A)

- [ ] `ip route` output recorded and default gateway identified (Part A)

- [ ] Live output compared to the Week 5 simulator (Part A, Step 3)

- [ ] Gateway pinged and the silent result interpreted correctly (Part B)

- [ ] Beacon `ping`, `traceroute`, and `curl` all run and recorded (Part C)

- [ ] Beacon banner and TRACE ID recorded (Part C, Step 4)

- [ ] `vm-toolkit-live.png` and `beacon-reply.png` captured, cropped, uploaded to `assets/screenshots/week-06/` (Part C, Step 5)

- [ ] Ladder Rule rewritten with route evidence + known-good target (Part D)

- [ ] All four Analysis Questions answered (minimum sentence counts met)

- [ ] This file is committed to your portfolio repo at `week-06/labs/lab-03-the-grid-for-real.md`

---

## GitHub Commit Subsection

1. Open **Week 6 → Lab 03: The Grid, For Real** in the Lab Portal.
2. Fill in the worksheet fields and upload both screenshots to `assets/screenshots/week-06/`.
3. Click **Submit to GitHub** — the Portal commits to `week-06/labs/lab-03-the-grid-for-real.md`.

---

*CyberVisionaries Institute · Cyber Foundations · Tier I*
