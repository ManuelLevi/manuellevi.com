+++
author = "Manuel Levi"
date = 2023-03-20T01:13:20Z
description = ""
draft = true
slug = "common-errors-with-pandas-sklearn-and"
title = "Common errors with Pandas, Sklearn and Tensorflow/Keras"

+++




This is a live document that I'll update as I find more and more of these.

## Pandas

### InvalidIndexError: Reindexing only valid with uniquely valued Index objects" Error

Most likely you are using `concat` to concatenate two dataframes with **different indexes**.

For example, your DF1 might have a `datetime` index and your DF2 might have a numerical index like `1,2,3...n`.

```python
# you can use the index from the first df to create df2
df2 = pandas.DataFrame(data, columns=['max_drop', 'max_jump'],index=df.index)
```

## Tensorflow/Keras

### ValueError: Failed to find data adapter that can handle input: , ( containing values of types {""})

Awful feedback, isn't it? One of those unnecessary Google visits.

Just convert your inputs into numpy arrays, you probably forgot it. If your `y` or `y_train` data is a list, you need to convert it to a numpy array.

TF people could simply either convert the list into a numpy array, or give the programmer the suggestion that maybe it's what they forgot.

```py
y = np.asarray(y)

# or if you rather do it after splitting
train_y = np.asarray(train_y)
test_y = np.asarray(test_y)
```

### ValueError: Failed to convert a NumPy array to a Tensor (Unsupported object type float)

Another cryptic message. This can be caused by so many reasons, it's hard to list everything.

Usually this problem is solved by converting the data to an numpy array, you can add the value type to reduce the probability of error:

```python
x = np.asarray(x).astype('float32')
```

However, there are other problems that can cause this error, for example empty values in a string column in a pandas dataframe. In that case, filling NA values with empty strings might help.

```python
df.fillna(value='', inplace=True)
```



