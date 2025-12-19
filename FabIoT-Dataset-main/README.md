# FabIoT-Dataset

## Introduction

FabIoT dataset was produces as a result of a Bacholer Thesis at The University of Zurich during the spring semester 2021. It models the internal behavior of a Raspberry Pi 4 while under attack from backdoors. The dataset was created using [this](https://github.com/Fabiooo98/Monitoring-Script) monitoring script. It monitors 72 performance events, the exact usage and specifications are mentioned on the respective GitHub page.

## Content

Six different attack behaviors were defined as follows:

| Name | Description |
| ----------- | ----------- |
| b1 | Execute a simple non-interactive command (”pwd”)and returning its ouput to the attacker |
| b2 | Execute a more complex chain of commands(creating a new directory and cloning a repository) |
| b3 | Deleting a file. |
| b4 | Pulling a file from the target machine. |
| b5 | Resolving a domain name at the target machine. |
| b6 | Changing an access flag for a file on the target machine. |

These attack behaviors were then repeatedly executed while the monitoring script gathered data. Each csv file contains the device behavior under one attack behavior.
The dataset contains 13 files containing infected data and one file containing normal device behavior.

The attacks were conducted using the following three backdoors:

- [http](https://github.com/SkryptKiddie/httpBackdoor)
- [backdoor](https://github.com/jakoritarleite/backdoor)
- [thetick](https://github.com/nccgroup/thetick#usage)

The dataset has the following structure:

```
FabIoT-Dataset
│   README.md
│   LICENSE    
│
└───data
   └───1 - clean
        │   clean.csv
   └───2 - http
        │   http_b1.csv
        │   http_b2.csv
        │   http_b3.csv
   └───3 - backdoor
        │   backdoor_b1.csv
        │   backdoor_b2.csv
        │   backdoor_b3.csv
        │   backdoor_b4.csv
   └───4 - tick
        │   tick_b1.csv
        │   tick_b2.csv
        │   tick_b3.csv
        │   tick_b4.csv
        │   tick_b5.csv
        │   tick_b6.csv
 ```
