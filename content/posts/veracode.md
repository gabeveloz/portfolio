---
title: "Automating Security Visibility"
date: 2026-04-28T16:13:05-07:00
draft: false
toc: false
images:
tags:
  - security
  - github actions
---
## The Problem

So our team was shipping code constantly, but we had no real way to know if critical vulnerabilities were in it. Scanning was this manual thing that happened randomly—basically whenever someone remembered to do it. You'd find out about issues weeks later, or the worst case, right before you needed to deploy something.

Not ideal.

## How I Built It

I set up three workflows that talk to each other. Here's how it works:

**Saturday:** GitHub Actions runs vulnerability scans on all the repos. Automated, scheduled, happens every week without anyone having to think about it.

**Sunday:** Another workflow wakes up and does the actual filtering. It grabs the scan results from the vulnerability API and keeps only the high and very-high severity findings. The reason I filter is simple—if you show people 50+ findings with a mix of low and critical stuff, they get overwhelmed. You need to show them what actually matters.

**Sunday morning:** Power Automate takes those filtered results and sends them to Teams. So instead of drowning in data, the team sees like 2 to 4 critical alerts.

Here's what happens: every Sunday morning the team sees what they're dealing with. They fix stuff Monday through Wednesday. Friday they deploy knowing what was actually in the code.

## Why It Actually Works

Before, finding vulnerabilities was totally reactive. You'd stumble on them when you least expected it—or too late. Now it's predictable. Every single Sunday the team gets visibility into security issues before they hit production.

That shift from "oh crap we found something critical right before release" to "okay here are the vulnerabilities we need to fix this week" is massive. It changes how teams actually work.

## What Changed

**Consistency:** The pipeline just runs. 52 weeks straight, 52 successful scans. Zero misses because the system doesn't forget.

**Discovery timing:** Instead of randomly finding vulnerabilities whenever someone ran a manual scan, the team now gets critical findings every Sunday morning. Predictable.

**Signal to noise:** Teams gets 2 to 4 actionable alerts per week instead of wading through 50+ findings trying to figure out which ones actually matter.

**How the team works:** Remediation used to be chaos. Now it's scheduled. Review on Sunday, fix during the week, ship on Friday.

**Tech:** GitHub Actions, vulnerability scanning APIs, Power Automate, Microsoft Teams.

The actual win is that your team finds vulnerabilities before they become someone else's problem. That's the whole difference.
