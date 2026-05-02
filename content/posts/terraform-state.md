---
title: "Monolithic Terraform Is a Trap"
date: 2026-05-01T17:09:00-07:00
draft: false 
toc: false
images:
tags:
  - Terraform
  - State Management
  - Github Actions 
---
## The Problem
 
One state file. Four environments. Hundreds of AWS resources all crammed together. And for a while, it worked fine. Until it didn't.
 
The problem with monolithic state isn't that it breaks immediately. It quietly accumulates risk. Every `terraform plan` refreshes your entire infrastructure across every environment at once. Every `terraform apply` is one misread output away from touching production when you only meant to change dev. There's no blast radius control. No safety net. Just one big trust fall with your cloud account.
 
Not ideal.
 
## How I Fixed It
 
We split it. Four environments, four independent state files, each in its own S3 key. Dev, staging, preview, and production all isolated from each other. Production even lives in a completely separate AWS account so the isolation is physical, not just logical.
 
The migration was where things got interesting. Real infrastructure was already running in all four environments so nothing could be created or destroyed. The target was a clean plan: X imports, 0 adds, 0 changes, 0 destroys. We used import blocks in code instead of running imperative commands manually, which made the whole thing reviewable and version controlled.
 
At one point I ran a plan against the wrong state because I forgot to re-init after switching environments. The result was a plan showing 35 resources to add and 93 to destroy. Hit Ctrl+C fast enough, no damage done. But that feeling of almost wrecking a real environment because of one missed command stuck with me.
 
We wired everything into a CI workflow after that. Re-init happens automatically now. Plan is open to the team. Apply is locked down to one person.
 
## Why It Actually Works
 
Before the split, every Terraform operation was implicitly a platform-wide operation. You couldn't change dev without Terraform being aware of prod. That's not isolation, that's just hoping nothing goes wrong.
 
Now each environment is its own contract. A plan against staging only knows about staging. An apply against dev cannot touch prod. That boundary is what makes it safe to actually move fast.
 
## What Changed
 
Blast radius: Changes are scoped to one environment. A mistake in dev stays in dev.
 
Confidence: The team can run plans freely without worrying about accidentally refreshing production state.
 
Safety: The wrong-init footgun is automated away. The pipeline handles re-init so no one has to remember.
 
Clarity: Smaller state files are easier to reason about. You only see what belongs to that environment.
 
The real win is that Terraform state is a contract and splitting it means each environment gets its own. That's the whole difference.
 
