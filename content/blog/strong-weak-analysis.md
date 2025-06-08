+++
title = 'Post Number 0'
date = 2024-04-24T15:29:14+12:00
draft = false
showToc = true
[params]
  ShareButtons = ["linkedin", "reddit"]
+++

## Strong Weak Analysis - trading the trend

Some time ago I got into [this post](https://forums.babypips.com/t/trading-the-trend-with-strong-weak-analysis/77959/1) from Dennis3450, where he mentions his analysis to define the strong and weak forex pairs, basing it on the difference between one currency and JPY daily close and 4h 200 MA.

Based on how the calculations are made, I decided to create a simple Python script to automate the process. It runs everyday at 9PM UTC and publishes the results automatically to https://sw.gus.nz. This is just an initial test and with time I plan to improve how the data is presented.