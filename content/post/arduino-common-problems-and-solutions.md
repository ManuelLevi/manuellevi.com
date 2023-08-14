+++
author = "Manuel Levi"
categories = ["Checklist"]
date = 2022-05-24T23:23:40Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2022/05/IMG_20220525_003936.jpg"
slug = "arduino-common-problems-and-solutions"
tags = ["Checklist"]
title = "Arduino Checklist"

+++


I'll keep adding as I find new common problems or bugs, and their solutions.

This will serve as my own reference, but it might be useful for someone else!

## Make sure the right board is selected.

This is one of the most common issues, and probably the easiest to solve.

If you're getting the error message: **"a fatal error occurred: Timed out waiting for packet header"** when you're programming a ESP32 (e.g. M5Stick, M5Stack, Wemos D1, etc), the most likely reason behind it is having the wrong board selected.

Change it to the correct one and it should work!

