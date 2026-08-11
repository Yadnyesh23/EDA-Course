# NumPy — Complete Notes (with EDA focus)

## 1. What is NumPy?

**NumPy** (Numerical Python) is the core Python library for numerical and scientific computing. It provides:

- The **`ndarray`** (n-dimensional array) object — a fast, memory-efficient, multi-dimensional array structure.
- A huge collection of **mathematical, statistical, linear algebra, and random-number functions** that operate on these arrays.
- The foundation on which **Pandas, Scikit-learn, TensorFlow, PyTorch, SciPy, and Matplotlib** are all built. Pandas `DataFrame`/`Series` actually store their data internally as NumPy arrays.

```python
import numpy as np
```

---

## 2. Why is NumPy used?

| Reason | Explanation |
|---|---|
| **Speed** | NumPy arrays are stored in contiguous memory blocks and operations are implemented in C, so they are 10–100x faster than native Python lists for numerical work. |
| **Vectorization** | You can apply operations to an entire array at once (`arr * 2`) instead of writing explicit `for` loops. This is both faster and more readable. |
| **Memory efficiency** | A NumPy array stores elements of a single, fixed data type (`dtype`), unlike a Python list which stores pointers to objects — this drastically reduces memory overhead. |
| **Broadcasting** | Lets you perform arithmetic between arrays of different (but compatible) shapes without manually reshaping them. |
| **Rich functionality** | Built-in support for linear algebra, Fourier transforms, random sampling, statistics, and more. |
| **Interoperability** | Almost every data science / ML library expects or returns NumPy arrays, making it the common "data currency" of the Python data ecosystem. |

**In short:** Python lists are flexible but slow for numeric computation; NumPy arrays are fast, compact, and purpose-built for numerical work.

---

## 3. Where is NumPy used?

- **Data Analysis / EDA** — cleaning, transforming, and summarizing numeric data (often underneath Pandas).
- **Machine Learning / Deep Learning** — feature matrices, weight matrices, tensors (PyTorch/TensorFlow tensors are conceptually NumPy arrays with GPU support).
- **Scientific computing** — physics simulations, signal processing, image processing (images are just 2D/3D NumPy arrays of pixel values).
- **Statistics & Mathematics** — computing mean, std, variance, correlation, matrix operations, eigenvalues, etc.
- **Finance** — time-series computations, portfolio simulations, Monte Carlo methods.
- **Computer Vision** — an image is literally a NumPy array: `(height, width, channels)`.

---

## 4. How is NumPy used in EDA (Exploratory Data Analysis)?

EDA is about understanding the shape, distribution, and quality of your data before modeling. NumPy supports this at almost every step:

1. **Loading/converting data** — Pandas columns convert to NumPy arrays (`df['col'].values` or `df.to_numpy()`) for fast numeric operations.
2. **Descriptive statistics** — `np.mean()`, `np.median()`, `np.std()`, `np.var()`, `np.percentile()`, `np.min()`, `np.max()` to understand central tendency and spread.
3. **Handling missing / invalid values** — `np.isnan()`, `np.nan`, `np.nanmean()`, `np.nansum()` to detect and safely compute around missing data.
4. **Outlier detection** — using z-scores (`(x - mean) / std`) or IQR (`np.percentile(arr, 75) - np.percentile(arr, 25)`) computed via NumPy.
5. **Correlation & covariance** — `np.corrcoef()`, `np.cov()` to see relationships between numeric features.
6. **Reshaping & filtering data** — boolean masking (`arr[arr > 50]`) to quickly filter rows/values matching a condition.
7. **Generating synthetic/test data** — `np.random` functions to simulate distributions, test hypotheses, or create dummy datasets.
8. **Feeding visualization libraries** — Matplotlib/Seaborn take NumPy arrays directly for histograms, scatter plots, etc.
9. **Binning & histograms** — `np.histogram()`, `np.digitize()` for frequency distribution analysis.
10. **Normalization/Scaling** — min-max scaling or z-score standardization done via simple vectorized NumPy math before feeding into ML models.

---

## 5. NumPy Arrays: 1D, 2D, 3D

### 1D Array (Vector)
A simple sequence of numbers — like a single column or a list.

```python
arr1d = np.array([10, 20, 30, 40, 50])
print(arr1d.shape)   # (5,)
print(arr1d.ndim)    # 1
```
Used for: a single feature/column, a time series, a simple list of measurements.

### 2D Array (Matrix)
Rows and columns — like a spreadsheet or a table (this is what a Pandas DataFrame is internally).

```python
arr2d = np.array([[1, 2, 3],
                   [4, 5, 6],
                   [7, 8, 9]])
print(arr2d.shape)   # (3, 3)  -> (rows, columns)
print(arr2d.ndim)    # 2
```
Used for: tabular datasets, grayscale images (height × width), correlation matrices.

### 3D Array (Tensor)
An array of matrices — think of it as multiple 2D tables stacked together.

```python
arr3d = np.array([[[1, 2], [3, 4]],
                   [[5, 6], [7, 8]],
                   [[9, 10], [11, 12]]])
print(arr3d.shape)   # (3, 2, 2)  -> (depth, rows, columns)
print(arr3d.ndim)    # 3
```
Used for: color images (height × width × channels), a stack/batch of matrices, video frames, time-series of multiple features across multiple entities.

**Rule of thumb:** `ndim` tells you the number of dimensions, `shape` tells you the size along each dimension.

---

## 6. NumPy Indexing and Slicing

### 6.1 In 1D Arrays

```python
arr = np.array([10, 20, 30, 40, 50])

arr[0]        # 10          -> first element
arr[-1]       # 50          -> last element
arr[1:4]      # [20 30 40]  -> slice: index 1 up to (not including) 4
arr[:3]       # [10 20 30]  -> from start to index 3
arr[2:]       # [30 40 50]  -> from index 2 to end
arr[::2]      # [10 30 50]  -> every 2nd element
arr[::-1]     # [50 40 30 20 10] -> reversed array
```

**Boolean indexing (very common in EDA):**
```python
arr[arr > 25]     # array([30, 40, 50]) -> filter values greater than 25
```

**Fancy indexing:**
```python
arr[[0, 2, 4]]    # array([10, 30, 50]) -> select specific indices
```

### 6.2 In 2D Arrays

```python
arr2d = np.array([[1, 2, 3],
                   [4, 5, 6],
                   [7, 8, 9]])

arr2d[0, 0]       # 1        -> row 0, col 0
arr2d[1, 2]       # 6        -> row 1, col 2
arr2d[1]          # [4 5 6]  -> entire row 1
arr2d[:, 0]       # [1 4 7]  -> entire column 0
arr2d[0:2, 1:3]   # [[2 3] [5 6]] -> sub-matrix (rows 0-1, cols 1-2)
arr2d[:, :2]      # all rows, first 2 columns
arr2d[-1, :]      # last row, all columns
```

**Boolean masking in 2D (common for filtering EDA data):**
```python
arr2d[arr2d > 5]        # flattened array of values > 5: [6 7 8 9]
```

**Key syntax pattern:** `array[row_selector, column_selector]`

---

## 7. EDA Point of View — Practical Use Cases

| Task | NumPy Code |
|---|---|
| Get summary stats | `np.mean(arr)`, `np.median(arr)`, `np.std(arr)` |
| Find missing values | `np.isnan(arr).sum()` |
| Filter outliers (z-score) | `z = (arr - arr.mean()) / arr.std(); arr[np.abs(z) < 3]` |
| Quick correlation check | `np.corrcoef(x, y)` |
| Bucket data into bins | `np.histogram(arr, bins=5)` |
| Normalize a column | `(arr - arr.min()) / (arr.max() - arr.min())` |
| Standardize a column | `(arr - arr.mean()) / arr.std()` |
| Sort data | `np.sort(arr)` |
| Get unique values + counts | `np.unique(arr, return_counts=True)` |

---

## 8. Mechanisms to Generate Arrays

### 8.1 `np.arange(start, stop, step)`
Generates evenly spaced values within a given range (like Python's `range()`, but returns an array and supports floats).

```python
np.arange(1, 20, 2)
# array([ 1,  3,  5,  7,  9, 11, 13, 15, 17, 19])
```
- `start` — inclusive (default 0)
- `stop` — exclusive
- `step` — spacing between values (default 1)

Other common generators:
```python
np.zeros((2, 3))          # array of zeros, shape (2,3)
np.ones((3, 3))           # array of ones
np.full((2, 2), 7)        # array filled with 7
np.eye(3)                 # 3x3 identity matrix
np.linspace(0, 1, 5)      # 5 evenly spaced values between 0 and 1 (inclusive)
```

### 8.2 Random Number Generation — `np.random`

| Function | What it does | Example |
|---|---|---|
| `np.random.randint(low, high, size)` | Random **integers** from `low` (inclusive) to `high` (exclusive) | `np.random.randint(1, 100, size=5)` → 5 random ints between 1–99 |
| `np.random.randn(d0, d1, ...)` | Random samples from the **standard normal distribution** (mean=0, std=1) | `np.random.randn(3, 3)` → 3x3 matrix of normally distributed values |
| `np.random.random_sample(size)` (or `np.random.random(size)`) | Random floats uniformly distributed in **[0.0, 1.0)** | `np.random.random_sample(5)` → 5 floats between 0 and 1 |
| `np.random.rand(d0, d1, ...)` | Similar to `random_sample` but takes dimensions directly, uniform [0,1) | `np.random.rand(2, 3)` |
| `np.random.choice(arr, size)` | Randomly picks elements from an existing array | `np.random.choice([1,2,3,4], size=2)` |
| `np.random.seed(n)` | Sets a fixed seed so random results are **reproducible** | `np.random.seed(42)` |

**Examples:**
```python
np.random.randint(1, 50, size=(3, 3))
# 3x3 matrix of random integers between 1 and 49

np.random.randn(5)
# array of 5 values sampled from N(0, 1) — can be negative

np.random.random_sample((2, 2))
# 2x2 matrix of random floats in [0.0, 1.0)
```

**Why this matters in EDA:**
- `randint` / `random_sample` — great for generating **dummy/test datasets** to prototype an analysis pipeline before real data arrives.
- `randn` — useful for simulating **normally distributed data**, testing statistical assumptions, or adding synthetic noise.
- `seed()` — critical for **reproducibility** so your analysis/results can be shared and re-verified exactly.

---

## 9. Quick Reference Cheat-Sheet

```python
import numpy as np

# Creation
a = np.array([1, 2, 3])              # 1D
b = np.array([[1,2],[3,4]])          # 2D
c = np.arange(1, 20, 2)              # ranged
d = np.zeros((2,3)); np.ones((2,3))  # filled
e = np.linspace(0, 1, 5)             # evenly spaced

# Inspecting
a.shape, a.ndim, a.dtype, a.size

# Indexing/Slicing
a[1:3]; b[0,1]; b[:,0]; b[b > 2]

# Random
np.random.seed(0)
np.random.randint(0, 10, 5)
np.random.randn(3, 3)
np.random.random_sample(4)

# EDA stats
np.mean(a); np.median(a); np.std(a); np.var(a)
np.percentile(a, [25, 50, 75])
np.corrcoef(x, y)
np.unique(a, return_counts=True)
```