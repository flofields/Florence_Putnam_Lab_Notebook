---
layout: post
title: SwitchFree library prep
date: '2025-06-09'
categories:Protocols
tags: [DNA, RNA, Library prep, Protocols]
projects: ENCORE Priming 
---
## Library Prep for The ENCORE Priming Project 

We used all of Zymos recommendations for library prep found [here](https://fscucchia-labnotebooks.github.io/FScucchia_Putnam_Lab_Notebook/Troubleshooting-LibraryPrep-ZymoSwitch/)

| Sample ID | TS RNA (ng/uL) | Strip tube # | RNA (uL) | Ultrapure water (uL) | Total starting volume (ul) | Primer |
| ------ | -------------- | ------------ | -------- | -------------------- | -------------------------- | ------ |
| MD-5-3 | 43.5           | 2            | 1.0      | 4.0                  | 5.0                        | 2      |
| MD-2-18| 36.1           | 3            | 1.0      | 4.0                  | 5.0                        | 3      |

Zymo SwitchFree library prep workflow
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/85a6e4a1cfdee2851b568f8d417ef4271da868a6/images/Zymo-Seq_SwitchFree_3'_mRNA_Library_Kit_Workflow.jpg?raw=true)

### Materials 

- [Zymo-Seq SwitchFree 3' mRNA Library Kit](https://www.zymoresearch.com/products/zymo-seq-switchfree-3-mrna-library-kit)
- PCR tubes (4 per sample)
- Thermocycler 
- Mini centrifuge
- Aluminum beads (to keep things on ice)
- Magnetic stand for PCR tubes 
- [Zymo-Seq SwitchFree 3' mRNA Library Kit contents](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/0a97a356397e13642f23f66375e470a1f710121d/images/Zymo%20switch%20free/Zymo%20switch%20free%20kit%20contents.jpg)

### Buffer preperation 

This step only needs to be done once a new kit is opened. 

- Add 300 uL of the Select-a-Size MagBead concentrate to 10 mL of Select-a-Size MagBead Buffer. 
	- These should be prepared at least 3-5 days ahead of library prep. 
- Add 24 mL of 100% ethanol to the DNA Wash Buffer concentrate. Store at room temperature. 

### Best practices 

- Avoid multiple freeze thaws of all components, and aliquot components as necessary
- Remove enzymes from cold storage just before use and return to cold storage immediately after use
- Thaw and maintain all components on ice unless noted otherwise 
- Flick to mix thawed components and briefly centrifuge prior to use 
- After adding each component, mix by pipetting up and down 15-20 times. Briefly centrifuge after 
- Pre-program thermal cycler with lid heating ON set to >100-105°C 
- Turn on thermocycler day-of to preheat 
- Pre-calculate how much to dilute RNA samples

Full report [here](https://github.com/flofields/Coral_Priming_Experiments_Summer_2024/blob/471d7aa8cff1dae3044867ae2c954d952be3852b/images/D5000_ScreenTape/2025_06_09/2025-06-09%20-%20trial.pdf)

![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_09/20250609_1.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_09/20250609_2.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_09/20250609_3.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_09/20250609_4.jpg)

Note: All both samples failed, this could be because of primer dimer or adapter dimers. Next steps is to change the amount of cycles from 19 to 21 during amplification. 