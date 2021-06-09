# Optiplex 3020 Hackintosh

>**Disclamer:** As I am a human being, like you, I make mistakes. I'm not responsible for any negative consequences if you follow this guide, and something goes wrong. If you find any issues, feel free to open a ticket, or send me a message. Use this guide at your own responsibility.

## Introduction

Hackintoshing is a very fun and rewarding side project. The good thing about it is that you don't need very specific hardware to make one. When I got my hands on an OptiPlex 3020, I had no I idea what to do with it. But based on a suggestion of my roommate, I've decided to turn it into a Hackintosh. As I found out, it is a fully capable Hackintosh candidate, with all functions working. 

### About this repository
This guide is made to explain the proccess of turning your OptiPlex 3020 into a Hackintosh. The guide is formatted in a step-by-step manner. Important text is made **bold**. *Italicized text* identifies that the text is optional or not important. During the guide, I will talk about the different hardware configurations and the different steps required for each of them. I will also give suggestions on what kind of hardware is recommended.

Currently this EFI is based on **OpenCore 0.7.0** and supports **macOS Big Sur**.

### About macOS Catalina and the future
This repository first made to support macOS Catalina. When Big Sur came out of public beta, this repository was changed to support it. Now the repo only supports Big Sur. It may still work with Catalina, but I cannot guarantee it.

*In WWDC 2021, Apple announced macOS 12 Monterey. I do not have any plans of updating this guide to support it until it comes out of public beta. Any issues regarding macOS 12 will be closed.*

## Prerequisites

### Requirements

These things are required for this project to succeed:

 - A Dell Optiplex 3020 of any form factor:
   - SFF
   - MT
   - M
   - To check which one you have, check out [this link](https://www.dell.com/support/kbdoc/en-us/000129367/optiplex-3020-visual-guide-to-your-computer).
-   Access to a working Mac/Linux/Windows machine.
- A 16GB pen drive
