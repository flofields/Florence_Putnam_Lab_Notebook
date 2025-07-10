---
layout: post
title: Library Prep for The ENCORE Priming Project in Bermuda
date: 2025-07-08
categories: Protocols
tags: [DNA, RNA, Library prep, Madracis decactis]
---

## Library Prep for The ENCORE Priming Project in Bermuda 

### Protocols used 

- [Zymo-Seq SwitchFree 3′ mRNA Library Kit protocol](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/5e1bd6a1daa8db6ce40285a804eace80a1039421/protocols/Zymo_seq_switchfree_3_mrna_library_kit.pdf)
- [TapeStation protocol DNA D5000](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/6e9046967846d31b2658908bf7ece3a7d2a67ed0/_posts/2025-06-09-DNA-Tapestation.md)

This post details the info about the SwitchFree library prep steps to produce adequate libraries for RNA sequencing with Madracis decactis. I'm using the [Zymo-Seq SwitchFree 3' mRNA Library Kit](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/5e1bd6a1daa8db6ce40285a804eace80a1039421/protocols/Zymo_seq_switchfree_3_mrna_library_kit.pdf). I followed Dr. Jill Ashey's post [here](https://github.com/JillAshey/JillAshey_Putnam_Lab_Notebook/blob/master/_posts/2024-03-29-Zymo-SwitchFree.md) and Zymo's protocol [here](https://files.zymoresearch.com/protocols/_r3008_r3009__zymo_seq_switchfree_3_mrna_library_kit.pdf). 

I will be prepping samples from the [ENCORE priming experiment conducted in Bermuda 2024](https://github.com/flofields/Coral_Priming_Experiments_Summer_2024).

The kit needs a minimum of 10 ng of total RNA or a maximum of 500 ng of total RNA, which is a large range. We will be using 12 ng of total RNA as input. Here's a breakdown of input RNA volumes for each sample:

I will be doing 4 samples with the modifications below

Modifications 

- Used 12 ng RNA input instead of 11 ng
- For the polyA R1 reagent, used 3uL of polyA R1 reagent + 2uL of DNase/RNase free water instead of 5 uL of polyA R1 reagent

Total RNA volume used was rounded to 3 decmal places and used to calulate the DNAse/RNAse Free Water used

| Sample ID | TS RNA (ng/uL) | Strip tube # | RNA (uL) | DNAse/RNAse Free Water (uL) | Total starting volume (ul) | Primer |
| ------ | -------------- | ------------ | -------- | -------------------- | -------------------------- | ------ |
| MD-1-19| 6.27           | 1            | 1.913875598	|3.086124402                 | 5.0                        | 23      |
| MD-1-15| 15.15           | 2            | 0.7920792079	|4.207920792                 | 5.0                        | 24      |
| MD-5-19| 12           | 3            |1	|4                 | 5.0                        | 25      |
| MD-4-24| 18.3           | 4            | 0.6557377049	|4.344262295                 | 5.0                        | 26      |

Zymo SwitchFree library prep workflow
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/85a6e4a1cfdee2851b568f8d417ef4271da868a6/images/Zymo-Seq_SwitchFree_3'_mRNA_Library_Kit_Workflow.jpg?raw=true)
	 
Here I’m using UDI primers from the 96 well plate (CAT. D3096, LOT. 255118) provided with the kit.


### Materials 

- [Zymo-Seq SwitchFree 3' mRNA Library Kit](https://www.zymoresearch.com/products/zymo-seq-switchfree-3-mrna-library-kit)
- PCR tubes 
- Thermocycler 
- Mini centrifuge
- Aluminum beads (to keep things on ice)
- Magnetic stand for PCR tubes 
- Kit contents 

![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/fd3b63aea71aa8571e6614d968c75bc7a751b25a/images/Zymo%20switch%20free/Zymo%20switch%20free%20kit%20contents.jpg?raw=true)

#### Qbit dsDNA HS result
| Sample ID | ug/uL #1 | ug/uL #2 | avg ug/uL  | 
| ------ | -------------- | ------------ | -------- | --- |
|Standard #1|47.85| | |
|Standard #2|22623.95| | |
| MD-1-19| 4.54           | 4.52            | 4.53	|
| MD-1-15| 6.50           | 6.10            | 6.30	|
| MD-5-19| 4.62           | 4.62            |4.62	|


See the full report [here](https://github.com/flofields/Coral_Priming_Experiments_Summer_2024/blob/099ae070f0e217b4c8217a9d672c209a4eb9ca68/images/D5000_ScreenTape/2025_07_08/DNA_ENCORE_20250708_FF2.pdf).


#### Note: Samples MD-4-24 had many peaks and needs to be redone
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_08/DNA_ENCORE_20250708_FF2_page-0001.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_08/DNA_ENCORE_20250708_FF2_page-0002.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_08/DNA_ENCORE_20250708_FF2_page-0003.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_08/DNA_ENCORE_20250708_FF2_page-0004.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_08/DNA_ENCORE_20250708_FF2_page-0005.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_08/DNA_ENCORE_20250708_FF2_page-0006.jpg)

MD-4-24 failed however it was done previously on [2025-06-30](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/a8a8cae85446da5ec7f49bc8c2bf0045e0ff406d/_posts/2025-06-30-ENCORE-Priming-Library-Prep.md), after consulting Dr. Jill Ashey I decided it was good enough to use therefore i do not need to redo the sample. 