---
title: "Monolithic Terraform State Is a Trap"
date: 2026-05-01T17:09:00-07:00
draft: false 
toc: false
images:
tags:
  - Terraform
  - State Management
  - Github Actions 
---
For a while, our backend ran on a single Terraform state file. One file, four environments, hundreds of AWS resources all crammed together. And honestly? It worked. Until it didn't.
 
The problem with monolithic state isn't that it breaks immediately. It's that it quietly accumulates risk. Every `terraform plan` refreshes your entire infrastructure across every environment at once. Every `terraform apply` is one misread output away from touching production when you meant to change dev. There's no blast radius control. There's no safety net. It's just one big trust fall with your cloud account.
 
So we split it. Four environments, four independent state files, each in its own S3 key. Dev, staging, preview, and production all isolated from each other. Production even lives in a completely separate AWS account, so the isolation isn't just logical, it's physical.
 
The migration was the interesting part. Real infrastructure was already running in all four environments, so nothing could be created or destroyed. The target was a clean plan: X imports, 0 adds, 0 changes, 0 destroys. We used import blocks in code instead of running imperative commands manually, which made the whole thing reviewable and version-controlled.
 
We also wired everything into a CI workflow. The wrong-init footgun, planning against the wrong state because you forgot to re-init, is now impossible to hit because the pipeline handles it automatically. Plan is open to the team. Apply is locked down.
 
The biggest thing I took from this: Terraform state is a contract. Treat it like one.
 
