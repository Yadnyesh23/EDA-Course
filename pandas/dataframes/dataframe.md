# Pandas — DataFrame Notes

## 1. What is a DataFrame in Pandas?

A **DataFrame** is Pandas' core 2D data structure — think of it as a **table**, like a spreadsheet or a SQL table. It is a **2D, size-mutable, labeled data structure** with columns that can each hold a **different data type** (int, float, string, bool, etc.).

- Rows are labeled by an **index**.
- Columns are labeled by **column names**.
- Internally, each column is essentially a **Series**, and a DataFrame is a **dict-like collection of Series objects sharing the same index**.

```python
import pandas as pd

df = pd.DataFrame({'col1': [1,2,3,4,5], 'col2': [1,2,3,4,5]}, index=['a','b','c','d','e'])
```

A DataFrame is the structure you'll use for **almost all real-world tabular data analysis** — it's what you get when you load a CSV, an Excel file, or a SQL query result into Pandas.

---

## 2. Creating a DataFrame

### 2.1 From a list

```python
# List of lists (each inner list = a row)
data = [[1, 'Amit', 85], [2, 'Priya', 90], [3, 'Ravi', 78]]
df = pd.DataFrame(data, columns=['ID', 'Name', 'Marks'])

# List of dicts (each dict = a row)
data2 = [{'ID':1,'Name':'Amit'}, {'ID':2,'Name':'Priya'}]
df2 = pd.DataFrame(data2)
```

### 2.2 From a dictionary

The most common approach — **keys become column names**, and each value (a list/array) becomes that column's data.

```python
data = {
    'Name': ['Amit', 'Priya', 'Ravi'],
    'Marks': [85, 90, 78]
}
df = pd.DataFrame(data)
```

You can also build a DataFrame from a **dict of Series**, and a **dict of dicts** (outer keys become columns, inner keys become the index):
```python
d = {'col1': pd.Series([1,2,3], index=['a','b','c']),
     'col2': pd.Series([4,5,6], index=['a','b','c'])}
df = pd.DataFrame(d)
```

### 2.3 From `read_csv`

```python
df = pd.read_csv('data.csv')
```

Common useful parameters of `read_csv`:

| Parameter | Purpose |
|---|---|
| `index_col` | Which column to use as the row index |
| `usecols` | Load only specific columns |
| `nrows` | Load only the first `n` rows |
| `header` | Which row to use as column names (`header=0` by default) |
| `names` | Manually specify column names |
| `dtype` | Force specific dtypes for columns |
| `na_values` | Extra strings to treat as `NaN` |
| `parse_dates` | Parse given columns as datetime |
| `sep` / `delimiter` | Custom delimiter (e.g., `sep=';'` for semicolon-separated files) |

Other common constructors: `pd.read_excel()`, `pd.read_json()`, `pd.read_sql()`, `pd.DataFrame.from_dict()`, `pd.DataFrame.from_records()`.

---

## 3. DataFrame Attributes and Methods

| Attribute/Method | What it tells you |
|---|---|
| `.shape` | Tuple `(rows, columns)` |
| `.dtypes` | Data type of each column (note: plural, unlike Series' `.dtype`) |
| `.index` | Row labels |
| `.columns` | Column labels |
| `.values` | Underlying data as a 2D NumPy array |
| `.head(n)` | First `n` rows (default 5) |
| `.tail(n)` | Last `n` rows (default 5) |
| `.sample(n)` | `n` random rows |
| `.info()` | Summary: column names, non-null counts, dtypes, memory usage |
| `.describe()` | Summary statistics for numeric columns (count, mean, std, min, quartiles, max) |
| `.isnull()` / `.isna()` | Boolean mask of missing values, same shape as df |
| `.duplicated()` | Boolean Series flagging duplicate rows |
| `.rename()` | Rename row index labels or column names |

```python
df.shape        # (5, 2)
df.dtypes       # col1: int64, col2: int64
df.index        # RangeIndex or custom Index
df.columns      # Index(['col1', 'col2'])
df.values       # 2D numpy array

df.head(3)
df.tail(2)
df.sample(2)

df.info()
# <class 'pandas.core.frame.DataFrame'>
# RangeIndex: 5 entries
# Data columns (total 2 columns): ...

df.describe()
# count, mean, std, min, 25%, 50%, 75%, max for each numeric column
df.describe(include='object')   # summary stats for categorical/string columns
df.describe(include='all')      # both numeric and non-numeric

df.isnull()          # DataFrame of True/False
df.isnull().sum()    # count of nulls per column (very common in EDA)

df.duplicated()          # True/False per row
df.duplicated().sum()    # total number of duplicate rows
df.drop_duplicates()     # remove duplicate rows

df.rename(columns={'col1':'ColumnOne'}, inplace=True)
df.rename(index={'a':'row1'}, inplace=True)
```

**Other very useful attributes/methods (from official docs, good to know):**

| Method | What it does |
|---|---|
| `.ndim` | Number of dimensions (always 2 for a DataFrame) |
| `.size` | Total number of elements (rows × columns) |
| `.memory_usage()` | Memory used by each column |
| `.columns.tolist()` | Convert column names to a plain Python list |
| `.nunique()` | Number of unique values per column |
| `.T` | Transpose the DataFrame (rows ↔ columns) |
| `.copy()` | Create an independent copy (avoids `SettingWithCopyWarning` issues) |
| `.dropna()` | Drop rows/columns with missing values |
| `.fillna(value)` | Fill missing values |
| `.replace(old, new)` | Replace specific values |
| `.sort_values(by=...)` | Sort rows by one or more column values |
| `.sort_index()` | Sort rows by index label |
| `.reset_index()` | Reset index back to default integers |
| `.set_index('col')` | Set a column as the new index |
| `.apply(func)` | Apply a function along an axis (rows or columns) |
| `.applymap(func)` | Apply a function element-wise (deprecated in favor of `.map()` in newer versions) |
| `.groupby('col')` | Group rows by column value(s) for aggregate analysis |
| `.merge()` / `.join()` / `.concat()` | Combine multiple DataFrames |
| `.pivot_table()` | Create a spreadsheet-style pivot table |
| `.corr()` | Correlation matrix between numeric columns |
| `.value_counts()` (on a column) | Frequency count of unique values in a Series/column |

---

## 4. Math Methods

Just like Series, DataFrames support aggregate math methods — but now you must think about **direction (axis)**:

- `axis=0` (default) → operate **down each column** (column-wise, i.e., across rows)
- `axis=1` → operate **across each row** (row-wise, i.e., across columns)

| Method | What it computes |
|---|---|
| `.sum()` | Sum of values |
| `.min()` / `.max()` | Minimum / maximum |
| `.mean()` | Average |
| `.median()` | Median |
| `.mode()` | Most frequent value(s) |
| `.std()` | Standard deviation |
| `.var()` | Variance |
| `.count()` | Count of non-null values |

```python
df.sum()          # column-wise sum (default axis=0) -> one value per column
df.sum(axis=1)    # row-wise sum -> one value per row

df.mean()          # column-wise mean
df.mean(axis=1)    # row-wise mean

df.min(); df.max()
df.median(); df.mode()
df.std(); df.var()
```

**Memory tip:** `axis=0` = "walk down the rows" (result is per-column). `axis=1` = "walk across the columns" (result is per-row). This trips up almost everyone at first — the axis number refers to the axis being *collapsed*, not the axis you're summarizing "along" in plain English.

Other handy aggregate options:
```python
df['col1'].sum()          # sum of a single column
df[['col1','col2']].sum() # sum of selected columns
df.agg(['sum', 'mean'])   # apply multiple aggregations at once
df.corr()                 # pairwise correlation between numeric columns
df.cov()                  # pairwise covariance
```

---

## 5. Selecting Columns from a DataFrame

```python
df['col1']          # single column -> returns a Series
df[['col1','col2']] # multiple columns -> returns a DataFrame (note the double brackets)
df.col1              # attribute-style access (works only if column name has no spaces/special chars)
```

---

## 6. Selecting Rows from a DataFrame — `loc` vs `iloc`

| | `.loc` | `.iloc` |
|---|---|---|
| Based on | **Labels** (index names, column names) | **Integer positions** (0-based) |
| Slicing end | **Inclusive** of the end label | **Exclusive** of the end position |
| Use when | You know the row/column **names/labels** | You know the row/column **positions** |

```python
df = pd.DataFrame({'Name':['Amit','Priya','Ravi'], 'Marks':[85,90,78]}, index=['a','b','c'])

df.loc['a']            # row with index label 'a'
df.loc['a':'b']         # rows 'a' through 'b' -> INCLUSIVE
df.loc[['a','c']]       # specific rows by label

df.iloc[0]              # row at position 0
df.iloc[0:2]             # rows at position 0,1 -> EXCLUSIVE of 2
df.iloc[[0,2]]           # specific rows by position
df.iloc[-1]              # last row (negative indexing works with iloc!)
```

**When to use which:**
- Use **`.loc`** when you're working with **meaningful labels** (e.g., dates, IDs, names) — especially when the index isn't just 0,1,2,...
- Use **`.iloc`** when you want **pure positional access** (e.g., "give me the first 5 rows regardless of what their labels are"), or when you need **negative indexing**.

---

## 7. Selecting Both Rows and Columns

Syntax pattern: `df.loc[row_selector, column_selector]` or `df.iloc[row_selector, column_selector]`

```python
df.loc['a', 'Name']              # single value: row 'a', column 'Name'
df.loc['a':'b', ['Name','Marks']] # rows a-b, specific columns
df.loc[:, 'Marks']                # all rows, one column

df.iloc[0, 1]              # single value: row 0, col 1
df.iloc[0:2, 0:2]           # sub-table by position
df.iloc[:, 0]               # all rows, first column
df.iloc[[0,2], [0,1]]        # specific rows & cols by position
```

`.at[]` and `.iat[]` are faster alternatives to `.loc`/`.iloc` when accessing a **single scalar value**:
```python
df.at['a', 'Name']   # fast label-based scalar access
df.iat[0, 1]          # fast position-based scalar access
```

---

## 8. Filtering a DataFrame

Filtering uses **boolean masks** — conditions that return `True`/`False` for each row, used to select matching rows.

```python
df[df['Marks'] > 80]                          # rows where Marks > 80
df[(df['Marks'] > 80) & (df['Name'] == 'Amit')]  # multiple conditions with & (AND)
df[(df['Marks'] < 60) | (df['Marks'] > 90)]      # | (OR)
df[~(df['Marks'] > 80)]                         # ~ (NOT)

df[df['Name'].isin(['Amit','Ravi'])]            # filter using a list of allowed values
df[df['Name'].str.startswith('A')]              # string-based filtering
df.query('Marks > 80')                          # alternative query-string syntax
```

**Important:** Always wrap each condition in parentheses when combining with `&`, `|`, `~` — Python's operator precedence will otherwise cause errors.

---

## 9. Adding New Columns

### Adding a brand-new column
```python
df['Grade'] = ['A', 'A', 'B']              # from a list (must match row count)
df['Passed'] = True                         # broadcasts a single value to all rows
```

### Adding a column derived from existing column(s)
```python
df['MarksPlus10'] = df['Marks'] + 10
df['Percentage'] = (df['Marks'] / 100) * 100
df['Grade'] = df['Marks'].apply(lambda x: 'A' if x >= 80 else 'B')
df['FullInfo'] = df['Name'] + ' - ' + df['Marks'].astype(str)
```

Other ways to add/insert columns:
```python
df.insert(1, 'Rank', [1,2,3])   # insert at a specific column position
df = df.assign(NewCol = df['Marks'] * 2)  # returns a new DataFrame with the column added
```

To remove a column:
```python
df.drop('Grade', axis=1, inplace=True)   # axis=1 -> column
# or
del df['Grade']
```

---

## 10. `astype` — Changing Column Data Types

`.astype()` converts a column (or the whole DataFrame) to a specified data type. Very common in EDA/data cleaning after loading data where numbers get read as strings, or IDs get read as floats.

```python
df['Marks'] = df['Marks'].astype(float)      # int -> float
df['ID'] = df['ID'].astype(str)               # int -> string
df['Passed'] = df['Passed'].astype(bool)      # -> boolean
df['Category'] = df['Category'].astype('category')  # memory-efficient categorical type

# Convert multiple columns at once
df = df.astype({'Marks': float, 'ID': str})

# errors parameter: what to do if conversion fails
df['col'] = pd.to_numeric(df['col'], errors='coerce')  # invalid parsing -> NaN instead of error
```

Related conversion functions from official docs:
```python
pd.to_numeric(df['col'])     # convert to numeric, safely
pd.to_datetime(df['col'])     # convert to datetime
pd.to_timedelta(df['col'])    # convert to timedelta
```

---

## Quick Reference Cheat-Sheet

```python
import pandas as pd

# Creation
df = pd.DataFrame({'col1':[1,2,3], 'col2':[4,5,6]})
df = pd.read_csv('file.csv', index_col=0)

# Attributes
df.shape; df.dtypes; df.index; df.columns; df.values
df.ndim; df.size

# Overview
df.head(); df.tail(); df.sample(3)
df.info(); df.describe()
df.isnull().sum(); df.duplicated().sum()
df.nunique()

# Rename / cleanup
df.rename(columns={'col1':'A'}, inplace=True)
df.drop_duplicates(inplace=True)
df.dropna(); df.fillna(0)

# Math (axis=0 -> per column, axis=1 -> per row)
df.sum(); df.sum(axis=1)
df.mean(); df.median(); df.mode()
df.std(); df.var(); df.min(); df.max()
df.corr()

# Selecting columns
df['col1']; df[['col1','col2']]

# Selecting rows
df.loc['a']; df.loc['a':'b']       # label-based, inclusive
df.iloc[0]; df.iloc[0:2]           # position-based, exclusive

# Selecting rows + columns
df.loc['a':'b', ['col1','col2']]
df.iloc[0:2, 0:2]
df.at['a','col1']; df.iat[0,1]     # fast scalar access

# Filtering
df[df['col1'] > 2]
df[(df['col1'] > 2) & (df['col2'] < 10)]
df[df['col1'].isin([1,2])]
df.query('col1 > 2')

# Adding columns
df['new'] = df['col1'] + df['col2']
df['flag'] = df['col1'].apply(lambda x: 'high' if x > 2 else 'low')
df.insert(1, 'rank', [1,2,3])
df.drop('new', axis=1, inplace=True)

# astype
df['col1'] = df['col1'].astype(float)
df = df.astype({'col1': str, 'col2': int})
pd.to_numeric(df['col1'], errors='coerce')
```