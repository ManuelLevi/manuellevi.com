+++
author = "Manuel Levi"
date = 2023-02-14T17:56:52Z
description = ""
draft = true
slug = "pyautogui-problems"
title = "Pyautogui: Common problems and fixes (Python RPA)"

+++


## `locateOnScreen` is not working, or is returning None all the time (Mac OS/OS X)



#### Step 1. Verify that you're able to take a screenshot

To do this, run the code

`pyautogui.screenshot('screenshot.png')`

Check the screenshot. Can you see all your windows, or just the background?

If all you can see is the background, then you need to update the "Security & Privacy" settings for the application that is running your code. This can be `Terminal`, or `Visual Studio Code`, or `PyCharm`.

If you have already done this, close the application and reopen it. I had to completely logout, since my application was running as a deamon, that was the best way to make sure it was restarted.

After you've done this, test it again. It should work.



#### Step 2. Verify the images 

This function needs the images to match perfectly, so make sure you have good screenshot of the images. Consider saving them in PNG format because of the way it compresses images without losing much information.



#### Step 3. Reduce the matching confidence


You can install `opencv` to get access to the additional `confidence` argument.


If you haven't installed `opencv` yet, you can do it by running `pip install opencv-python`. 


After that you can start using the confidence argument like so:
`pos = pyautogui.locateOnScreen('btn.png',grayscale=True, confidence=0.9)`



## `typewrite` doesn't work for symbols or inputs wrong characters



`PyAutoGUI` describes itself as a "cross-platform GUI automation Python module for human beings.", Maybe instead the description should say "module for usonian human beings".

This bug with "PyAutoGui" is due to the fact that it does not know about keyboard maps, except the USA one.

The solution is simple, using the `keyboard` library and the `keyboard.write` function, instead of the `pyautogui.typewrite` function.

There are other workarounds like:
* changing the keyboard layout just for your automation (yuck 🤮);
* storing the string in the clipboard and copy-pasting (yuck 🤮);
* storing a character map and replacing the characters on each string you want to send (yuck 🤮).

`pyautogui` supports thirdparty libraries like `opencv` for image matching, so it could do the same for keyboard input. Hopefuly this will happen soon.

## `typewrite` doesn't work with unicode strings


This is a very similar problem as the one above with probably the same root cause. Simply use one of the workarounds described above, or just replace the `pyautogui.typewrite` function with the `keyboard.write` function, and that's it! Everything should work nicely.

## ImportError: No module named pyautogui

Managing python environments can be a pain in the ass. Yes, you can use virtual environments and install all the libraries necessary every-single-damn-time, but that's boring AF and feels like too much when you just want to test something fast.

Typically, you would install `pyautogui` with a simple `pip3 install pyautogui`, however this doesn't always work when outside a virtual environment (looking at you, Anaconda.).

Instead, try the command `python3 -m pip install pyautogui`, this gives you much better chances that the library is going to be installed in the same place that your `python3` alias is looking at.

## Can't hold key down for X seconds

If you're automating something that requires you to hold a key down for sometime, like the movement of a game character, you'll realize that `pyautogui.keyDown` doesn't cause key repeats. 

The best way to do this would be by using the `hold() context manager`, like so:

```
with pyautogui.hold(key):
    pyautogui.sleep(hold)
```


## Mouse movements don't count as activity

Sometimes, you want to move the mouse using `pyautogui` to simulate activity (preventing screenlocks, showing as active on a messenger, 

Mouse movements aren't always counter for activity. Instead, consider using the keyboard events. Something like pressing the volume up and volume down keys can work wonders:
```
import pyautogui
import time

while True:
    pyautogui.press('volumedown')
    time.sleep(0.5)
    pyautogui.press('volumeup')
    time.sleep(60)
```

## Pyautogui is not working in a particular application

`Pyautogui` can easily be identified by specific software, like games and anti-cheating software. 

A lot of times, pyautogui isn't trying to hide. For example, the library uses Window's API and adds the `LLMHF_INJECTED` flag to its events.

There are at least ways around this:
* Write your own driver (yuck 🤮);
* Use a library that simulates direct input (like PyDirectInput - for windows only)
* Run it in a VMWare machine;

I don't like any of these to be honest. It would be better if pyautogui had a flag that would allow you to either flag your input or not.

**Note:** If you need something really untraceable, you can always use an Arduino to simulate key strokes as an actualy keyboard and mouse.

## The Pillow package is required to use this function.

The solution is simple, you install the `Pillow` package. 

However, it should have been installed automatically, when you installed `pyautogui` as it is a dependency of a dependency ;)

So I would recommend simply installing everything again with
`pip install --upgrade --force-reinstall pyautogui`

## Work with me

Connect with me through [LinkedIn](https://www.linkedin.com/in/manuellevi/), or through my company [Enlightenment.ai](https://Enlightenment.ai).



