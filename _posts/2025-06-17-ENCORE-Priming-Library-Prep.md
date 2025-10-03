---
layout: post
title: Library Prep for The ENCORE Priming Project in Bermuda
date: 2025-06-17
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

I will be doing 14 samples with the modifications below. Steps 1 and 2 on the first 7 samples were done on the 17th but tapestation occurred on the 18th once library prep was completed. Library prep for the other 7 samples were completed on the 18th.

Modifications 

- Used 12 ng RNA input instead of 11 ng
- For the polyA R1 reagent, used 3uL of polyA R1 reagent + 2uL of DNase/RNase free water instead of 5 uL of polyA R1 reagent

Total RNA volume used was rounded to 3 decmal places and used to calulate the DNAse/RNAse Free Water used

| Sample ID | TS RNA (ng/uL) | Strip tube # | RNA (uL) | DNAse/RNAse Free Water (uL) | Total starting volume (ul) | Primer |
| ------ | -------------- | ------------ | -------- | -------------------- | -------------------------- | ------ |
| MD-2-4| 10.5           | 29            | 1.142857143	|3.857142857                | 5.0                        | 53      |
| MD-1-20| 10.35           | 30            | 1.15942029	|3.84057971                 | 5.0                        | 54      |
| MD-2-10| 19.6           | 31            |0.612245	|4.387755                 | 5.0                        | 55      |
| MD-5-5| 11           | 32            | 1.090909	|3.909091                 | 5.0                        | 56      |
| MD-5-6| 15.1           | 33            | 0.7947019868	|4.205298013                | 5.0                        | 57      |
| MD-1-6| 36.5           | 34            | 0.3287671233	|4.671232877               | 5.0                        | 58      |
| MD-1-11 |12.35           | 35            |0.971659919	|4.028340081                 | 5.0                        | 59      |
 MD-2-25| 11.4           | 36            | 1.052631579	|3.947368421                 | 5.0                        | 60      |
| MD-4-6| 10           | 37            | 1.2	|3.8                 | 5.0                        | 61      |
| MD-1-3| 16.4           | 39            |0.7317073171	|4.268292683                 | 5.0                        | 62      |
| MD-4-25| 18.3           |40            | 0.6557377049	|4.344262295                 | 5.0                        | 63      |
| MD-1-15| 15.15           | 41            | 0.7920792079	|4.207920792                | 5.0                        | 64      |
| MD-2-2| 24.4           | 42            | 0.4918032787	|4.508196721               | 5.0                        | 65      |
| MD-4-9 |15.9           | 43            |0.7547169811	|4.245283019                 | 5.0                        | 66      |

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


See the full report [here](https://github.com/flofields/Coral_Priming_Experiments_Summer_2024/blob/5dba240ac13474e911af17f6027ad065f7b6b03c/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF1.pdf). and [here](https://github.com/flofields/Coral_Priming_Experiments_Summer_2024/blob/5dba240ac13474e911af17f6027ad065f7b6b03c/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF2.pdf)


#### Note: All samples except for MD-5-14 and MD-2-23 failed and will need to be redone

![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF1_page-0001.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF1_page-0002.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF1_page-0003.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF1_page-0004.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF1_page-0005.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF1_page-0006.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF1_page-0007.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF1_page-0008.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF1_page-0009.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF1_page-0010.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF1_page-0011.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF1_page-0012.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF2_page-0001.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF2_page-0002.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF2_page-0003.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF2_page-0004.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF2_page-0005.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF2_page-0006.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF2_page-0007.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF2_page-0008.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_17/DNA_ENCORE_2025-18_FF2_page-0009.jpg)