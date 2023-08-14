+++
author = "Manuel Levi"
categories = ["python", "Checklist"]
date = 2022-06-19T15:34:23Z
description = ""
draft = false
slug = "python-checklist"
tags = ["python", "Checklist"]
title = "Python Checklist"

+++


## Disable scientific notation.

I wrote a whole [post](__GHOST_URL__/why-i-hate-scientific-notation/) describing why it's a good idea NOT to use scientific notation.

Here's the code for both pandas and numpy.

```
import numpy as np
import pandas

np.set_printoptions(suppress=True)
pd.set_option('display.float_format', lambda x: '%.10f' % x)

```

To enable again:

```
np.set_printoptions(suppress=False)
pandas.reset_option('display.float_format')

```



