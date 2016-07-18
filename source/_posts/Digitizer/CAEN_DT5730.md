---
layout: post
title: "CAEN DT5730"
date: 2016-07-17 11:01:07 +0800
comments: true
tags: [CAEN,Digitizer]
---
<!-- CAEN_DT5730.md --- 
;; 
;; Description: 
;; Author: Hongyi Wu(吴鸿毅)
;; Email: wuhongyi@qq.com 
;; Created: 日 7月 17 11:01:07 2016 (+0800)
;; Last-Updated: 一 7月 18 15:44:19 2016 (+0800)
;;           By: Hongyi Wu(吴鸿毅)
;;     Update #: 14
;; URL: http://wuhongyi.github.io --> 

# DT5730

<!-- more -->

输入增益只有两档：

- 1：input range is 2 Vpp
- 4：input range is 0.5 Vpp


## Trigger Management

According to the standard firmware operating, all the channels in a board share the same trigger (board common trigger), that is to say they acquire an event simultaneously and in the same way (a determined number of samples and position with respect to the trigger).

The generation of a common acquisition trigger is based on different trigger sources (configurable through the Trigger Source Enable Mask register):

- Software trigger
- External trigger
- Self-trigger
- Coincidence

### Software Trigger

Software triggers are internally produced via a software command (write access in the Software Trigger register) through USB or Optical Link.

### External Trigger
x
A TTL or NIM external signal can be provided in the front panel TRG-IN connector (configurable through the Front Panel I/O Control register). If the external trigger is not synchronized with the internal clock, a 1-clock period jitter occurs.

### Self-Trigger
Each channel is able to generate a self-trigger signal when the digitized input pulse exceeds a configurable threshold(Channel n Threshold register). The self-triggers of each couple of adjacent channels are then processed to provide out a single trigger request. The trigger requests are propagated to the central trigger logic where they are ORed to produce the board common trigger, which is finally distributed back to all channels causing the event acquisition. Fig. 9.5 schematizes the self-trigger and trigger request logic having the channel 0 and channel 1 couple as an example.

![Self Trigger and Trigger Request logic for Ch0 and Ch1 couple. A single trigger request signal is generated.](/img/Digitizer/CAENDT5730_SELFTRIGGRT_95.png)


……

### Trigger Coincidence Level
Operating with the standard firmware, the acquisition trigger is a board common trigger. This common trigger allows the coincidence acquisition mode to be performed through the Majority operation.

Enabling the coincidences is possible by writing in the Trigger Source Enable Mask register (address 0x810C):

- Bits[3:0] enable the trigger request signals to participate in the coincidence;
- Bits[23:20] set the coincidence window ($T_{TVAW}$);
- Bits[26:24] set the Majority (i.e. Coincidence) level; the coincidence takes place when: **Number of enabled trigger requests > Majority level**

Supposing bits[3:0] = FF (i.e. all the 4 trigger request are enabled) and bits[26:24] = 01 (i.e. Majority level = 1), a board common trigger is issued whenever at least two of the enabled trigger requests are in coincidence within the programmed $T_{TVAW}$ .

The Majority level must be smaller than the number of trigger requests enabled via bits[3:0] mask. By default, bits[26:24] = 00 (i.e. Majority level = 0), which means the coincidence acquisition mode is disabled and the $T_{TVAW}$ is meaningless. In this case, the board common trigger is simple OR of the signals from the enabled channels pairs.

Fig. 9.8 and Fig. 9.9 show the trigger management in case the coincidences are disabled:

![Trigger request management at mezzanine level with Majority level = 0](/img/Digitizer/CAENDT5730_MajorityLevel0_98.png)
![Trigger request management at motherboard level with Majority level = 0](/img/Digitizer/CAENDT5730_MajorityLevel0_99.png)

Fig. 9.10 and shows the trigger management in case the coincidences are enabled with Majority level = 1 and $T_{TVAW}$ is a value different from 0. In order to simplify the description, CH1 and CH3 channels are considered disabled, so that the relevant trigger requests are the over-threshold signals from CH0 and CH1.

![Fig. 9.10: Trigger request relationship with Majority level = 1 and T TVAW ≠ 0](/img/Digitizer/CAENDT5730_MajorityLevel1_910.png)

*Note: with respect to the position where the common trigger is generated, the portion of input signal stored depends on the programmed length of the acquisition window and on the post trigger setting.*

Fig. 9.11 shows the trigger management in case the coincidences are enabled with Majority level = 1 and T TVAW = 0 (i.e. 1 clock cycle). In order to simplify the description, CH1 and CH3 channels are considered disabled, so that the relevant trigger requests are the over-threshold signals from CH0 and CH1.

![Trigger request relationship with Majority level = 1 and T TVAW = 0](/img/Digitizer/CAENDT5730_MajorityLevel1_911.png)

### Trigger Distribution
In the default configuration, the OR of all the enabled trigger sources is synchronized with the internal clock, then becomes the common trigger of the board and is fed in parallel to all channels, which consequently provokes the capture of an event.

A Trigger Out signal is also generated on the relevant front panel GPO connector (NIM or TTL), and allows to extend the trigger signal to other boards: set bits[17:16] = 00 in Front Panel Control register and enable the desired trigger sources in Front Panel Trigger Out Enable Mask register.

For example, in order to start the acquisition on all the channels of the boards, as soon as one of the channels crosses its threshold, the relevant local trigger must be enabled as Trigger Out signal. The Trigger Out must then be fed to a Fan Out unit; the obtained signal has to be fed to the external trigger input TRG-IN of all the boards in the chain(including the board which generated the Trigger Out signal).




















<!-- CAEN_DT5730.md ends here -->
