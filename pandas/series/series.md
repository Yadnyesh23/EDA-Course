# Pandas — Series Notes

## 1. What is Pandas?

**Pandas** is a Python library built on top of NumPy for working with structured/tabular data. It gives you two main data structures:

- **Series** — a 1D labeled array (covered in this document)
- **DataFrame** — a 2D labeled table (not covered here)

Pandas is the go-to tool for **data loading, cleaning, exploration, and analysis** — it's what you use right after (or often instead of) raw NumPy when your data has labels, mixed types, or comes from files like CSVs.

```python
import pandas as pd
```

---

## 2. What is a Series?

A **Pandas Series** is like a single **column in a table**. It is a **1D array** that can hold data of **any type** (integers, floats, strings, objects, etc.), and — unlike a NumPy array — every value has an associated **label**, called the **index**.

```python
a = pd.Series([1, 2, 3, 4, 5], index=['a', 'b', 'c', 'd', 'e'])
print(a)
```
```
a    1
b    2
c    3
d    4
e    5
dtype: int64
```

Think of a Series as: **values + labels**, i.e., a NumPy array with an index attached.

If you don't specify an index, Pandas auto-assigns a default integer index (0, 1, 2, ...):
```python
b = pd.Series([10, 20, 30])
# index will be 0, 1, 2 automatically
```

---

## 3. Creating a Series

### 3.1 Series from a list

```python
marks = pd.Series([85, 90, 78, 92])
# default index: 0, 1, 2, 3

marks_labeled = pd.Series([85, 90, 78, 92], index=['Math', 'Sci', 'Eng', 'Hist'])
```

### 3.2 Series from a dictionary

When creating a Series from a dictionary, the **keys automatically become the index** and the **values become the data**.

```python
d = {'a': 100, 'b': 200, 'c': 300}
s = pd.Series(d)
print(s)
```
```
a    100
b    200
c    300
dtype: int64
```

You can also pass an explicit `index` to select/reorder only specific keys:
```python
s2 = pd.Series(d, index=['b', 'c', 'x'])
# 'x' doesn't exist in the dict -> value becomes NaN
```

---

## 4. Series Attributes

| Attribute | What it tells you |
|---|---|
| `.size` | Total number of elements (including NaNs) |
| `.dtype` | Data type of the elements stored in the Series |
| `.name` | Name of the Series (useful when it will become a DataFrame column) |
| `.is_unique` | `True`/`False` — whether all values in the Series are unique |
| `.index` | The index (labels) of the Series |
| `.values` | The underlying data as a NumPy array |

```python
a = pd.Series([1, 2, 2, 4], index=['w','x','y','z'], name='numbers')

a.size         # 4
a.dtype        # dtype('int64')
a.name         # 'numbers'
a.is_unique    # False (2 appears twice)
a.index        # Index(['w', 'x', 'y', 'z'], dtype='object')
a.values        # array([1, 2, 2, 4])
```

### Difference between `Index` and `RangeIndex`

- **`RangeIndex`** is the **default** index type Pandas assigns when you don't provide custom labels. It's memory-efficient because it's just a defined range (like `range(0, n)`) — it doesn't actually store every individual label in memory.
  ```python
  s = pd.Series([1, 2, 3])
  print(s.index)   # RangeIndex(start=0, stop=3, step=1)
  ```
- **`Index`** (a general `Index` object) is used when you provide **custom labels** (strings, dates, non-sequential numbers, etc.). It actually stores every label explicitly.
  ```python
  s = pd.Series([1, 2, 3], index=['a', 'b', 'c'])
  print(s.index)   # Index(['a', 'b', 'c'], dtype='object')
  ```

**In short:** `RangeIndex` = auto-generated, lightweight, sequential integers. `Index` = explicitly stored, custom labels of any type.

---

## 5. Series using `read_csv`

You can create a Series directly by reading a single-column CSV file.

```python
s = pd.read_csv('data.csv', index_col=0, squeeze=True)
```

- **`index_col`** — tells Pandas which column of the CSV to use as the index (row labels) instead of creating a new default index. `index_col=0` means "use the first column as the index."
- **`squeeze`** — if the CSV has only **one data column** (after the index column is set aside), `squeeze=True` converts the resulting single-column DataFrame into a **Series** instead of a DataFrame.

  > **Note:** In modern Pandas versions, `squeeze` was deprecated/removed. The current recommended way is:
  > ```python
  > df = pd.read_csv('data.csv', index_col=0)
  > s = df.squeeze("columns")   # or df.iloc[:, 0] to grab it as a Series
  > ```

---

## 6. Series Methods

| Method | What it does |
|---|---|
| `.head(n)` | Returns the first `n` elements (default 5) |
| `.tail(n)` | Returns the last `n` elements (default 5) |
| `.sample(n)` | Returns `n` random elements from the Series |
| `.value_counts()` | Returns counts of each unique value, sorted descending by frequency |
| `.sort_values()` | Sorts the Series by its **values** |
| `.sort_index()` | Sorts the Series by its **index labels** |

```python
s = pd.Series([50, 20, 90, 20, 40], index=['a','b','c','d','e'])

s.head(2)          # first 2 values
s.tail(2)          # last 2 values
s.sample(2)         # 2 random values
s.value_counts()    # 20 -> 2, 50 -> 1, 90 -> 1, 40 -> 1
s.sort_values()      # sorted ascending by value
s.sort_index()       # sorted by index label (a,b,c,d,e)
```

---

## 7. Series Mathematical Methods

| Method | What it does |
|---|---|
| `.count()` | Number of **non-null** values |
| `.sum()` | Sum of all values |
| `.product()` | Product of all values |
| `.mean()` | Average |
| `.median()` | Middle value |
| `.mode()` | Most frequently occurring value(s) |
| `.std()` | Standard deviation |
| `.var()` | Variance |
| `.max()` / `.min()` | Maximum / minimum value |
| `.describe()` | Summary of all key stats at once (count, mean, std, min, 25%, 50%, 75%, max) |

### Difference between `.size` and `.count()`

- **`.size`** — total number of elements **including `NaN`/missing values**.
- **`.count()`** — number of **non-null (non-NaN)** elements only.

```python
s = pd.Series([10, 20, None, 40])
s.size    # 4  (counts the None/NaN too)
s.count() # 3  (ignores the NaN)
```

```python
s.describe()
```
```
count     3.0
mean     23.33
std      15.28
min      10.0
25%      15.0
50%      20.0
75%      30.0
max      40.0
dtype: float64
```

---

## 8. Series Indexing

You can access elements using either the **label-based index** or the **default positional index**.

```python
s = pd.Series([100, 200, 300], index=['x', 'y', 'z'])

s['x']     # 100  -> label-based access
s[0]       # 100  -> positional access (still works but discouraged; use .iloc)
s.loc['y'] # 200  -> explicit label-based access
s.iloc[1]  # 200  -> explicit position-based access
```

### Negative indexing does not work in Series (by label)

Unlike Python lists, you **cannot** do `s[-1]` to get the last element of a labeled Series — because `-1` is interpreted as a **label**, and if `-1` isn't an actual index label, it raises a `KeyError`.

```python
s = pd.Series([10, 20, 30], index=['a', 'b', 'c'])
s[-1]        # ❌ KeyError (unless -1 happens to literally be a label)
```

To get the last element, use `.iloc[-1]` (position-based), which **does** support negative indexing:
```python
s.iloc[-1]   # ✅ 30
```

### Slicing

Slicing works, but behaves **differently** depending on whether you slice by **label** or by **position**:

```python
s = pd.Series([10, 20, 30, 40, 50], index=['a', 'b', 'c', 'd', 'e'])

s['b':'d']    # label-based slicing -> INCLUSIVE of the end label 'd'
              # returns b, c, d

s.iloc[1:4]   # position-based slicing -> EXCLUSIVE of end position (like Python lists)
              # returns index 1,2,3 -> b, c, d
```

**Key point:** Label-based slicing (`s['b':'d']`) includes the stop label, while position-based slicing (`s.iloc[1:4]`) excludes the stop position — this is a common source of confusion.

---

## 9. Editing in a Series

### Using index

You can update the value at a given index label directly:
```python
s = pd.Series([10, 20, 30], index=['a', 'b', 'c'])
s['b'] = 99
# s -> a:10, b:99, c:30
```

### If an index does not exist

Assigning to an index label that **doesn't already exist** simply **adds a new entry** to the Series (Pandas Series can grow dynamically):
```python
s['d'] = 40
# a new element with index 'd' and value 40 is appended
```

---

## 10. Series with Python Functionalities

Pandas Series play nicely with built-in Python functions since a Series is iterable and array-like.

### `len` / `type` / `dir` / `sorted` / `max` / `min`

```python
s = pd.Series([30, 10, 20])

len(s)      # 3            -> number of elements
type(s)     # pandas.core.series.Series
dir(s)      # lists all attributes/methods available on s
sorted(s)   # [10, 20, 30] -> returns a sorted Python list of the values
max(s)      # 30
min(s)      # 10
```

### Type conversion

You can convert a Series to native Python data structures:
```python
list(s)      # [30, 10, 20]
dict(s)      # {0: 30, 1: 10, 2: 20}  (index -> value pairs)
```

### Membership operator (`in`)

By default, the `in` operator checks against the **index labels**, not the values:
```python
s = pd.Series([10, 20, 30], index=['a', 'b', 'c'])

'a' in s     # True  -> checks index labels
10 in s      # False -> 10 is a value, not an index label

10 in s.values   # True -> check against actual values instead
```

### Loop

Looping over a Series by default iterates over the **values** (not the index):
```python
for val in s:
    print(val)      # 10, 20, 30

for idx, val in s.items():
    print(idx, val)  # a 10, b 20, c 30  -> use .items() to get both
```

---

## 11. Plotting Graphs

Since a Series wraps a NumPy array with labels, it has a built-in `.plot()` method (powered by Matplotlib) for quick, one-line visualizations — very handy during EDA.

```python
import matplotlib.pyplot as plt

s = pd.Series([10, 25, 15, 40, 30], index=['Mon','Tue','Wed','Thu','Fri'])

s.plot()            # default line plot
s.plot(kind='bar')   # bar chart
s.plot(kind='pie')   # pie chart
s.plot(kind='hist')  # histogram of the values
s.value_counts().plot(kind='bar')  # frequency bar chart

plt.show()
```

Common `kind` options: `'line'` (default), `'bar'`, `'barh'`, `'hist'`, `'box'`, `'kde'`, `'pie'`.

---

## Quick Reference Cheat-Sheet

```python
import pandas as pd

# Creation
s1 = pd.Series([1,2,3], index=['a','b','c'])
s2 = pd.Series({'a':1, 'b':2, 'c':3})

# Attributes
s1.size; s1.dtype; s1.name; s1.is_unique; s1.index; s1.values

# Methods
s1.head(); s1.tail(); s1.sample(2)
s1.value_counts(); s1.sort_values(); s1.sort_index()

# Math methods
s1.count(); s1.sum(); s1.mean(); s1.median(); s1.mode()
s1.std(); s1.var(); s1.max(); s1.min(); s1.describe()

# Indexing
s1['a']; s1.loc['a']; s1.iloc[0]; s1.iloc[-1]
s1['a':'b']    # label slice - inclusive
s1.iloc[0:2]   # position slice - exclusive

# Editing
s1['a'] = 100     # update existing
s1['d'] = 999     # add new

# Python interplay
len(s1); max(s1); min(s1); sorted(s1)
list(s1); dict(s1)
'a' in s1          # checks index
for idx, val in s1.items(): pass

# Plotting
s1.plot(kind='bar')
```