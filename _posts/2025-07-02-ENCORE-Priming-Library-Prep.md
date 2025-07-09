---
layout: post
title: Library Prep for The ENCORE Priming Project in Bermuda
date: 2025-07-02
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

I will be doing 7 samples with the modifications below

Modifications 

- Used 12 ng RNA input instead of 11 ng
- For the polyA R1 reagent, used 3uL of polyA R1 reagent + 2uL of DNase/RNase free water instead of 5 uL of polyA R1 reagent

Total RNA volume used was rounded to 3 decmal places and used to calulate the DNAse/RNAse Free Water used

| Sample ID | TS RNA (ng/uL) | Strip tube # | RNA (uL) | DNAse/RNAse Free Water (uL) | Total starting volume (ul) | Primer |
| ------ | -------------- | ------------ | -------- | -------------------- | -------------------------- | ------ |
| MD-1-24| 19.9           | 1            | 0.6030150754	|4.396984925                 | 5.0                        | 8      |
| MD-4-26| 11.4           | 2            | 1.052631579	|3.947368421                 | 5.0                        | 9      |
| MD-4-2| 15.2           | 3            |0.7894736842	|4.210526316                 | 5.0                        | 10      |
| MD-4-13| 16.55           | 4            | 0.7250755287	|4.274924471                 | 5.0                        | 11      |
| MD-2-18| 36.1           | 5            | 0.3324099723	|4.667590028                | 5.0                        | 12      |
| MD-1-26| 9.23           | 6            | 1.300108342	|3.699891658               | 5.0                        | 13      |
| MD-1-19 |6.27           | 7            |0.5940594059	4.405940594                 | 5.0                        | 14      |


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
| ------ | -------------- | ------------ | -------- | ---
|Standard #1|50.83| | |
|Standard #2|23857.17| | |
| MD-1-24| 2.58           | 2.56            | 2.57	|
| MD-4-26| 3.20           | 3.20            | 3.20	|
| MD-4-2| 4.74           | 4.72            |4.73	|
| MD-4-13| 4.16           | 4.16            | 4.16	|
| MD-2-18| 2.42           | 2.42            | 2.42	|
| MD-1-26| 2.26           | 2.26            | 2.26	|
| MD-1-19 |Out of Range           |             |


See the full report [here](https://github.com/flofields/Coral_Priming_Experiments_Summer_2024/blob/5771205c8c2d10c10f647c1cff36553ce4280780/images/D5000_ScreenTape/2025_07_02/DNA_ENCORE_20250702_FF.pdf).


#### Note: Samples MD-4-24 had many peaks and needs to be redone
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_02/DNA_ENCORE_20250702_FF_page-0001.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_02/DNA_ENCORE_20250702_FF_page-0002.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_02/DNA_ENCORE_20250702_FF_page-0003.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_02/DNA_ENCORE_20250702_FF_page-0004.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_02/DNA_ENCORE_20250702_FF_page-0005.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_02/DNA_ENCORE_20250702_FF_page-0006.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_02/DNA_ENCORE_20250702_FF_page-0007.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_02/DNA_ENCORE_20250702_FF_page-0008.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_02/DNA_ENCORE_20250702_FF_page-0009.jpg)
