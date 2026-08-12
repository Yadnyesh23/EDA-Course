# NumPy Practice Exercises (EDA Focus)

Try to solve each on your own first. Solutions are at the bottom — no peeking early!

---

## Section A: Array Creation

**A1.** Create a 1D array of numbers from 5 to 50 with a step of 5 using `np.arange`.

**A2.** Create a 3x3 array of all zeros, then a 2x4 array of all ones.

**A3.** Use `np.linspace` to generate 10 evenly spaced values between 0 and 100 (inclusive).

**A4.** Create a 4x4 identity matrix.

**A5.** Create a 1D array of 20 random integers between 1 and 100 using `np.random.randint`. Set a seed of `42` first so your output is reproducible.

---

## Section B: Array Properties (1D / 2D / 3D)

**B1.** Create a 2D array with shape `(4, 3)` containing numbers 1–12 (hint: use `np.arange` + `.reshape()`). Print its `.shape`, `.ndim`, and `.size`.

**B2.** Create a 3D array of shape `(2, 3, 4)` filled with random integers between 0 and 9. Print its shape and number of dimensions.

**B3.** Given `arr = np.arange(1, 25)`, reshape it into a 3D array of shape `(2, 3, 4)`.

---

## Section C: Indexing & Slicing — 1D

Given: `arr = np.array([12, 45, 7, 89, 34, 23, 67, 5, 90, 15])`

**C1.** Get the first 3 elements.

**C2.** Get the last 3 elements.

**C3.** Get every 2nd element starting from index 1.

**C4.** Reverse the array.

**C5.** Get all elements greater than 30 (boolean indexing).

**C6.** Get the elements at indices 0, 3, and 7 (fancy indexing).

---

## Section D: Indexing & Slicing — 2D

Given:
```python
arr2d = np.array([[4, 8, 15, 16],
                   [23, 42, 6, 9],
                   [11, 20, 13, 7],
                   [1, 5, 99, 3]])
```

**D1.** Get the element at row 2, column 3.

**D2.** Get the entire 3rd row (index 2).

**D3.** Get the entire last column.

**D4.** Get the sub-matrix formed by the first 2 rows and last 2 columns.

**D5.** Get all elements greater than 10.

**D6.** Replace every element greater than 50 with `0`.

---

## Section E: EDA-Style Statistics

Given: `data = np.array([23, 45, 12, 67, 34, 89, 21, 56, 78, 90, 15, 33])`

**E1.** Find the mean, median, and standard deviation.

**E2.** Find the 25th, 50th, and 75th percentiles.

**E3.** Compute the z-score for every value: `(x - mean) / std`.

**E4.** Using the z-scores from E3, filter out values whose absolute z-score is greater than 1.5 (these are potential outliers).

**E5.** Normalize `data` to a 0–1 range using min-max scaling: `(x - min) / (max - min)`.

**E6.** Find the number of unique values and their counts using `np.unique`.

---

## Section F: Handling Missing Values

Given: `data = np.array([10, np.nan, 25, 40, np.nan, 55, 30])`

**F1.** Count how many `NaN` values are in the array.

**F2.** Compute the mean while ignoring `NaN` values.

**F3.** Replace all `NaN` values with the mean of the non-NaN values.

---

## Section G: Random Sampling

**G1.** Generate a 3x3 matrix of values sampled from the standard normal distribution using `np.random.randn`.

**G2.** Generate 5 random floats between 0 and 1 using `np.random.random_sample`.

**G3.** Simulate rolling a die 20 times (random integers 1–6) and find how many times each face (1–6) appeared. (hint: `np.unique` with `return_counts=True`)

**G4.** Create a random 5x5 matrix of integers between 1–100, then find its row-wise mean and column-wise mean (hint: use the `axis` parameter in `np.mean`).

---

## Section H: Mini Challenge (Combine Everything)

You're given a simulated dataset of 50 students' exam scores:

```python
np.random.seed(1)
scores = np.random.randint(30, 100, size=50)
```

Do the following:
1. Print the mean, median, and standard deviation of scores.
2. Find how many students scored above 75.
3. Find how many students scored below 40 (potentially at-risk students).
4. Normalize the scores to a 0–1 range.
5. Find the top 5 highest scores using slicing on a sorted array.
6. Bucket the scores into 5 bins and print the frequency count in each bin using `np.histogram`.

---

## Solutions

<details>
<summary>Click to expand solutions</summary>

```python
import numpy as np

# --- Section A ---
# A1
a1 = np.arange(5, 51, 5)

# A2
a2_zeros = np.zeros((3,3))
a2_ones = np.ones((2,4))

# A3
a3 = np.linspace(0, 100, 10)

# A4
a4 = np.eye(4)

# A5
np.random.seed(42)
a5 = np.random.randint(1, 101, size=20)

# --- Section B ---
# B1
b1 = np.arange(1, 13).reshape(4, 3)
print(b1.shape, b1.ndim, b1.size)

# B2
b2 = np.random.randint(0, 10, size=(2,3,4))
print(b2.shape, b2.ndim)

# B3
arr = np.arange(1, 25)
b3 = arr.reshape(2, 3, 4)

# --- Section C ---
arr = np.array([12, 45, 7, 89, 34, 23, 67, 5, 90, 15])
c1 = arr[:3]
c2 = arr[-3:]
c3 = arr[1::2]
c4 = arr[::-1]
c5 = arr[arr > 30]
c6 = arr[[0, 3, 7]]

# --- Section D ---
arr2d = np.array([[4, 8, 15, 16],
                   [23, 42, 6, 9],
                   [11, 20, 13, 7],
                   [1, 5, 99, 3]])
d1 = arr2d[2, 3]
d2 = arr2d[2]
d3 = arr2d[:, -1]
d4 = arr2d[0:2, 2:4]
d5 = arr2d[arr2d > 10]
arr2d_copy = arr2d.copy()
arr2d_copy[arr2d_copy > 50] = 0
d6 = arr2d_copy

# --- Section E ---
data = np.array([23, 45, 12, 67, 34, 89, 21, 56, 78, 90, 15, 33])
e1_mean, e1_median, e1_std = data.mean(), np.median(data), data.std()
e2 = np.percentile(data, [25, 50, 75])
e3 = (data - data.mean()) / data.std()
e4 = data[np.abs(e3) <= 1.5]   # keep non-outliers
e5 = (data - data.min()) / (data.max() - data.min())
e6_values, e6_counts = np.unique(data, return_counts=True)

# --- Section F ---
data_f = np.array([10, np.nan, 25, 40, np.nan, 55, 30])
f1 = np.isnan(data_f).sum()
f2 = np.nanmean(data_f)
data_f_filled = np.where(np.isnan(data_f), np.nanmean(data_f), data_f)

# --- Section G ---
g1 = np.random.randn(3,3)
g2 = np.random.random_sample(5)
g3_rolls = np.random.randint(1, 7, size=20)
g3_faces, g3_counts = np.unique(g3_rolls, return_counts=True)
g4 = np.random.randint(1, 101, size=(5,5))
g4_row_mean = g4.mean(axis=1)
g4_col_mean = g4.mean(axis=0)

# --- Section H ---
np.random.seed(1)
scores = np.random.randint(30, 100, size=50)
h1_mean, h1_median, h1_std = scores.mean(), np.median(scores), scores.std()
h2 = (scores > 75).sum()
h3 = (scores < 40).sum()
h4 = (scores - scores.min()) / (scores.max() - scores.min())
h5 = np.sort(scores)[-5:]
h6_counts, h6_bin_edges = np.histogram(scores, bins=5)
```

</details>
