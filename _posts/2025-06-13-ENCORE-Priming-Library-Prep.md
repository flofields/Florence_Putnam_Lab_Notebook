---
layout: post
title: Library Prep for The ENCORE Priming Project in Bermuda
date: 2025-06-13
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

I will be doing 8 samples with the modifications below

Modifications 

- Used 12 ng RNA input instead of 11 ng
- For the polyA R1 reagent, used 3uL of polyA R1 reagent + 2uL of DNase/RNase free water instead of 5 uL of polyA R1 reagent

Total RNA volume used was rounded to 3 decmal places and used to calulate the DNAse/RNAse Free Water used

| Sample ID | TS RNA (ng/uL) | Strip tube # | RNA (uL) | DNAse/RNAse Free Water (uL) | Total starting volume (ul) | Primer |
| ------ | -------------- | ------------ | -------- | -------------------- | -------------------------- | ------ |
| MD-1-18 | 16.9           | 13            | 0.7100591716	|4.29                  | 5.0                        | 21      |
| MD-2-5| 17.6           | 14            | 0.6818181818	|4.318                 | 5.0                        | 22      |
| MD-1-13| 13.6           | 15            | 0.8823529412	|4.118                  | 5.0                        | 23      |
| MD-2-14| 33.1           | 16            | 0.3625377644	|4.637                  | 5.0                        | 24      |
| MD-5-15| 10.2           | 17            | 1.176470588	|3.824                  | 5.0                        | 25      |
| MD-4-18| 33.7           | 18            | 0.3560830861	|4.644      | 4.0                  | 5.0                        | 26      |
| MD-2 |10.4           | 19            | 1.153846154	|3.846                  | 5.0                        | 27      |
| MD-5-1| 17.6           | 20            | 0.6818181818	|4.318                  | 5.0                        | 28      |

Samples found below were went through cDNA synthesis today but stored in -20 until needed to complete the other 2 steps.

Sample ID | TS RNA (ng/uL) | Strip tube # | RNA (uL) | DNAse/RNAse Free Water (uL) | Total starting volume (ul) | Primer |
| ------ | -------------- | ------------ | -------- | -------------------- | -------------------------- | ------ |
| MD-4-21 | 15.2           | 21            | 0.7894736842	|4.211                  | 5.0                        | 29      |
| MD-5-17| 14.7           | 22            | 0.8163265306	|4.184                 | 5.0                        | 30      |
| MD-4-23| 13.2           | 23            | 0.9090909091	|4.091                  | 5.0                        | 31      |
| MD-1-2| 19.9           | 24            | 0.6030150754	|4.397                  | 5.0                        | 32      |
| MD-4-5| 41.7           | 25            | 0.2877697842	|4.712                  | 5.0                        | 33      |
| MD-1-22| 28           | 26            | 0.4285714286	|4.571                | 5.0                        | 34      |
| MD-2-23 |10.15           | 27            |1.18226601	|3.818                 | 5.0                        | 35      |
| MD-5-14| 42.8           | 28            | 0.2803738318	|4.720                  | 5.0                        | 36      |

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

I will run the tapestation on all libraries on 6/16/25. See the full report [here](https://github.com/flofields/Coral_Priming_Experiments_Summer_2024/blob/8850088ca053d188d94361c2b6aef8333102437b/images/D5000_ScreenTape/2025_06_13/DNA_ENCORE_2025-16_FF1.pdf) and [here](https://github.com/flofields/Coral_Priming_Experiments_Summer_2024/blob/9c45cc8fd4673525b4c0113eac039a04791ceb06/images/D5000_ScreenTape/2025_06_13/DNA_ENCORE_2025-06-13-FF2.pdf)

![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_13/20250613_1.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_13/20250613_2.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_13/20250613_3.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_13/20250613_4.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_13/20250613_5.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_13/20250613_6.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_13/20250613_7.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_13/20250613_8.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_13/20250613_9.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_16/20250613_12.jpg)

![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_13/20250613_11.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_13/20250613_13.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_13/20250613_14.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_13/20250613_15.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_13/20250613_16.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_13/20250613_17.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_13/20250613_18.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_06_13/20250613_19.jpg)

#### Note: two peaks were seen for samples MD-1-18, MD-2-5, however bp size was so small and they were close togetheir that the sample does not need to be redone.
#### Note: Two peaks were seen for samples MD-4-5 and will be redone 20250619 by Dr. Jill Ashey(jill), MD-1-22, 2-23, 5-14 however bp size was so small and they were close togethier that the sample does not need to be redone.

