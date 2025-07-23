---
layout: post
title: Library Prep for The ENCORE Priming Project in Bermuda
date: 2025-07-06
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
| MD-1-18| 16           | 1            | 0.7100591716	|4.289940828                 | 5.0                        | 15      |
| MD-5-7| 19.2           | 2            | 0.5729166667	|4.427083333                 | 5.0                        | 16      |
| MD-1-16| 10.4           | 3            |1.153846154	|3.846153846                 | 5.0                        | 17      |
| MD-5-14| 42.8           | 4            | 0.2803738318	|4.719626168                 | 5.0                        | 18      |
| MD-1-11| 12.35           | 5            | 0.971659919	|4.028340081                | 5.0                        | 19      |
| MD-4-9| 15.9           | 6            | 0.7547169811	|4.245283019               | 5.0                        | 20      |
| MD-1-21 |30.2           | 7            |0.3973509934	|4.602649007                 | 5.0                        | 21      |
| MD-2-12 |17.7           | 8            |0.6779661017	|4.322033898                 | 5.0                        | 22      |


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
See [here](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/b7725656ec24e76646ef842d6fc135e1e046528c/_posts/2025-06-29-dsDNA-and-RNA-Qubit-Quantification-Protocol.md) for protocol.


| Sample ID | ug/uL #1 | ug/uL #2 | avg ug/uL  | 
| ------ | -------------- | ------------ | -------- | --- |
|Standard #1|53.15| | |
|Standard #2|24156.17| | |
| MD-1-18| 5.34           | 5.30            | 5.32	|
| MD-5-7| 6.10           | 6.08            | 6.09	|
| MD-1-16| 3.34           | 3.34            |3.34	|
| MD-5-14| 7.44           | 7.42            | 7.43	|
| MD-1-11| 6.14           | 6.12            | 6.13	|
| MD-4-9| 3.64           | 3.62            | 3.63	|
| MD-1-21 |5.58          | 5.56            | 5.57|
| MD-2-12 |6.50           | 6.48            |6.49|

See the full report [here](https://github.com/flofields/Coral_Priming_Experiments_Summer_2024/blob/e09cbc3a668bb4e7138ebb08abb2ce54ee6a6a93/images/D5000_ScreenTape/2025_07_06/DNA_ENCORE_20250707_FF.pdf) and [here](https://github.com/flofields/Coral_Priming_Experiments_Summer_2024/blob/cb8d9307b23d527ec5f9855e90e69198036f1fe0/images/D5000_ScreenTape/2025_07_06/DNA_ENCORE_20250707_FF2.pdf).


#### Note: Samples MD-4-24 had many peaks and needs to be redone
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_06/DNA_ENCORE_20250707_FF_page-0001.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_06/DNA_ENCORE_20250707_FF_page-0002.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_06/DNA_ENCORE_20250707_FF_page-0003.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_06/DNA_ENCORE_20250707_FF_page-0004.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_06/DNA_ENCORE_20250707_FF_page-0005.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_06/DNA_ENCORE_20250707_FF_page-0006.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_06/DNA_ENCORE_20250707_FF_page-0007.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_06/DNA_ENCORE_20250707_FF_page-0008.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_06/DNA_ENCORE_20250707_FF_page-0009.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_06/DNA_ENCORE_20250707_FF_page-0010.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_06/DNA_ENCORE_20250707_FF2_page-0001.jpg)
![](https://raw.githubusercontent.com/flofields/Coral_Priming_Experiments_Summer_2024/refs/heads/main/images/D5000_ScreenTape/2025_07_06/DNA_ENCORE_20250707_FF2_page-0003.jpg)
