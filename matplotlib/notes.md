# Complete Matplotlib Guide (from official docs, matplotlib.org)

This follows the structure of matplotlib's own docs: Quick Start → Anatomy of a Figure → Plot Types → Customization → Subplots → Working with Pandas → Saving → Advanced. Every section has runnable code.

---

## 0. Install & Import

```bash
pip install matplotlib
```

```python
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
```

---

## 1. The Two APIs (understand this FIRST — it prevents 90% of confusion)

Matplotlib has two ways to plot. The official docs recommend the **OO (object-oriented) style** for anything beyond a quick throwaway plot.

**Pyplot style (implicit, state-based — quick and dirty):**
```python
plt.plot([1, 2, 3, 4], [1, 4, 2, 3])
plt.title("Quick plot")
plt.show()
```

**OO style (explicit — recommended, scales to complex figures):**
```python
fig, ax = plt.subplots()          # fig = whole canvas, ax = the plotting area
ax.plot([1, 2, 3, 4], [1, 4, 2, 3])
ax.set_title("OO style plot")
plt.show()
```

Rule of thumb: **use `fig, ax = plt.subplots()` always**, even for a single plot. It makes every later concept (subplots, twin axes, styling) consistent.

---

## 2. Anatomy of a Figure

```
Figure
 └── Axes (the actual plot — NOT the same as "axis"!)
      ├── xaxis, yaxis (the number lines)
      ├── Title
      ├── Legend
      ├── Spines (the box lines)
      └── Artists (lines, text, patches — everything drawn)
```

```python
fig, ax = plt.subplots(figsize=(6, 4))   # figsize in inches
ax.plot([1, 2, 3], [4, 5, 6])
ax.set_xlabel("X label")
ax.set_ylabel("Y label")
ax.set_title("Anatomy demo")
fig.suptitle("Figure-level title")       # note: fig, not ax
plt.show()
```

Key distinction: **Figure** = the whole window/image. **Axes** = one plot inside it (a figure can have many Axes = subplots).

---

## 3. Core Plot Types (with your kind of data)

### Line plot
```python
housing = pd.read_csv("housing.csv")
fig, ax = plt.subplots()
ax.plot(housing["median_income"].sort_values().values)
ax.set_title("Sorted median income")
plt.show()
```

### Scatter plot
```python
fig, ax = plt.subplots()
ax.scatter(housing["median_income"], housing["median_house_value"], alpha=0.3, s=10)
ax.set_xlabel("Median Income")
ax.set_ylabel("Median House Value")
plt.show()
```

### Bar chart
```python
ipl = pd.read_csv("matches.csv")
top_winners = ipl["winner"].value_counts().head(8)

fig, ax = plt.subplots()
ax.bar(top_winners.index, top_winners.values, color="steelblue")
ax.set_xticklabels(top_winners.index, rotation=45, ha="right")
ax.set_title("Most wins by team")
plt.tight_layout()
plt.show()
```

### Horizontal bar
```python
fig, ax = plt.subplots()
ax.barh(top_winners.index, top_winners.values)
plt.show()
```

### Histogram
```python
fig, ax = plt.subplots()
ax.hist(housing["median_house_value"], bins=40, edgecolor="black")
ax.set_title("Distribution of house values")
plt.show()
```

### Box plot
```python
fig, ax = plt.subplots()
ax.boxplot(housing["median_income"].dropna())
plt.show()
```

### Pie chart
```python
fig, ax = plt.subplots()
ax.pie(top_winners.values, labels=top_winners.index, autopct="%1.1f%%")
plt.show()
```

### Heatmap (correlation matrix — very common in your workflow)
```python
corr = housing.select_dtypes("number").corr()
fig, ax = plt.subplots(figsize=(8, 6))
im = ax.imshow(corr, cmap="coolwarm", vmin=-1, vmax=1)
ax.set_xticks(range(len(corr.columns)))
ax.set_xticklabels(corr.columns, rotation=90)
ax.set_yticks(range(len(corr.columns)))
ax.set_yticklabels(corr.columns)
fig.colorbar(im, ax=ax)
plt.tight_layout()
plt.show()
```

---

## 4. Styling & Customization

### Colors, linestyles, markers
```python
fig, ax = plt.subplots()
ax.plot(x, y, color="crimson", linestyle="--", marker="o", linewidth=2, markersize=5, label="series 1")
ax.legend()
plt.show()
```
Shorthand: `ax.plot(x, y, 'ro--')` → red, circles, dashed.

### Labels, title, ticks
```python
ax.set_xlabel("X", fontsize=12)
ax.set_ylabel("Y", fontsize=12)
ax.set_title("Title", fontsize=14, fontweight="bold")
ax.set_xlim(0, 10)
ax.set_ylim(-1, 1)
ax.set_xticks([0, 5, 10])
```

### Grid & spines
```python
ax.grid(True, linestyle=":", alpha=0.6)
ax.spines["top"].set_visible(False)
ax.spines["right"].set_visible(False)
```

### Legends
```python
ax.plot(x, y1, label="income")
ax.plot(x, y2, label="value")
ax.legend(loc="upper right", frameon=True)
```

### Style sheets (instant professional look)
```python
plt.style.use("seaborn-v0_8")      # or "ggplot", "fivethirtyeight", "dark_background"
print(plt.style.available)         # see all options
```

### Colormaps (for scatter/heatmaps)
```python
sc = ax.scatter(x, y, c=values, cmap="viridis")
fig.colorbar(sc, ax=ax)
```

---

## 5. Multiple Plots — Subplots

### Grid of subplots
```python
fig, axes = plt.subplots(2, 2, figsize=(10, 8))
axes[0, 0].plot(x, y)
axes[0, 1].scatter(x, y)
axes[1, 0].hist(y)
axes[1, 1].bar(["a", "b"], [3, 5])
plt.tight_layout()
plt.show()
```

### Iterating axes cleanly
```python
fig, axes = plt.subplots(1, 3, figsize=(15, 4))
for ax, col in zip(axes, ["median_income", "housing_median_age", "population"]):
    ax.hist(housing[col], bins=30)
    ax.set_title(col)
plt.tight_layout()
plt.show()
```

### Uneven layouts — `subplot_mosaic` (modern, recommended over old `add_subplot` gridspec juggling)
```python
fig, axd = plt.subplot_mosaic([["left", "right_top"],
                                ["left", "right_bottom"]], figsize=(8, 6))
axd["left"].plot(x, y)
axd["right_top"].scatter(x, y)
axd["right_bottom"].hist(y)
plt.tight_layout()
plt.show()
```

### Twin axes (two y-scales on one plot)
```python
fig, ax1 = plt.subplots()
ax2 = ax1.twinx()
ax1.plot(x, y1, color="blue")
ax2.plot(x, y2, color="red")
ax1.set_ylabel("y1", color="blue")
ax2.set_ylabel("y2", color="red")
plt.show()
```

---

## 6. Working Directly with Pandas

DataFrames have a built-in `.plot()` (a thin wrapper over matplotlib) — but for real control, pass the `ax`:

```python
fig, ax = plt.subplots()
housing["median_income"].plot(kind="hist", bins=30, ax=ax)   # pandas convenience
ax.set_title("Using pandas .plot() with a matplotlib ax")
plt.show()
```

Groupby + plot (very common for your IPL data):
```python
wins_by_season = ipl.groupby("season")["winner"].count()
fig, ax = plt.subplots()
wins_by_season.plot(kind="line", marker="o", ax=ax)
plt.show()
```

---

## 7. Annotations & Text

```python
fig, ax = plt.subplots()
ax.plot(x, y)
ax.annotate("Peak", xy=(peak_x, peak_y), xytext=(peak_x+1, peak_y+1),
            arrowprops=dict(facecolor="black", shrink=0.05))
ax.text(0.5, 0.9, "Note", transform=ax.transAxes)   # position in axes-fraction coords
plt.show()
```

---

## 8. Saving Figures

```python
fig.savefig("plot.png", dpi=300, bbox_inches="tight")
fig.savefig("plot.svg")   # vector, good for reports
fig.savefig("plot.pdf")
```

---

## 9. Interactivity & Backends (brief)

- In Jupyter: `%matplotlib inline` (static) or `%matplotlib widget` (interactive, needs `ipympl`).
- `plt.show()` is only needed in plain scripts — Jupyter auto-displays the last figure in a cell.

---

## 10. A Realistic End-to-End Example (California housing)

```python
plt.style.use("seaborn-v0_8-whitegrid")
fig, axes = plt.subplots(1, 2, figsize=(12, 5))

axes[0].scatter(housing["longitude"], housing["latitude"],
                c=housing["median_house_value"], cmap="viridis",
                s=housing["population"]/100, alpha=0.4)
axes[0].set_title("Geographic price distribution")
axes[0].set_xlabel("Longitude"); axes[0].set_ylabel("Latitude")

axes[1].hist(housing["median_house_value"], bins=40, color="teal", edgecolor="black")
axes[1].set_title("Price distribution")
axes[1].set_xlabel("Median house value")

plt.tight_layout()
fig.savefig("housing_overview.png", dpi=200)
plt.show()
```

---

## Cheat Sheet Quick Reference

| Task | Code |
|---|---|
| New figure + axes | `fig, ax = plt.subplots()` |
| Grid of axes | `fig, axes = plt.subplots(rows, cols)` |
| Line | `ax.plot(x, y)` |
| Scatter | `ax.scatter(x, y)` |
| Bar | `ax.bar(x, height)` |
| Histogram | `ax.hist(data, bins=n)` |
| Title/labels | `ax.set_title()`, `ax.set_xlabel()`, `ax.set_ylabel()` |
| Legend | `ax.legend()` |
| Save | `fig.savefig("name.png", dpi=300)` |
| Style | `plt.style.use("ggplot")` |
| Layout fix | `plt.tight_layout()` |

---

## Where to go next in the official docs
- Quick start guide: matplotlib.org/stable/users/explain/quick_start.html
- Plot types gallery: matplotlib.org/stable/plot_types/index.html
- Full examples gallery: matplotlib.org/stable/gallery/index.html
- Colors & colormaps: matplotlib.org/stable/users/explain/colors/index.html

**Suggested practice path with your own data:** take your California housing and IPL CSVs and recreate every plot type above using those datasets — you already have both loaded from your recent pandas practice, so this guide maps directly onto notebooks you're already building.