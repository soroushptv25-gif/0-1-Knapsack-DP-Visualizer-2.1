# 🎒 0/1 Knapsack — Interactive Teaching Tool

A single-file, zero-dependency interactive visualizer for the **0/1 Knapsack algorithm**, built with vanilla HTML, CSS, and JavaScript. Designed to teach the algorithm from multiple angles: dynamic programming, brute force enumeration, greedy comparison, and complexity analysis — all animated step by step.

---

## 🚀 Quick Start

```bash
# Clone the repo
git clone https://github.com/your-username/knapsack-visualizer.git
cd knapsack-visualizer

# Open in browser — no build step, no server needed
open index.html
```

Or just download `index.html` and open it directly in any modern browser.

---

## 🧠 What Is the 0/1 Knapsack Problem?

You have a knapsack with a maximum weight capacity **W**, and **n** items each with a **value** and a **weight**. You want to maximize the total value of items you pack, with two rules:

- Each item can only be taken **once** (hence "0/1" — you either take it or you don't).
- The total weight of chosen items must not exceed **W**.

**Example:**

| Item | Value | Weight |
|------|-------|--------|
| #1   | 3     | 2 kg   |
| #2   | 4     | 3 kg   |
| #3   | 5     | 4 kg   |
| #4   | 6     | 5 kg   |

With capacity W = 5, the optimal choice is Item #1 + Item #2 → **Value 7, Weight 5**.

---

## ⚙️ How the Algorithm Works

### Dynamic Programming (DP) — O(n·W)

The DP approach builds a table `K[i][w]` meaning *"the best value achievable using the first `i` items with capacity `w`"*.

**Recurrence relation:**

```
If weight[i] > w:
    K[i][w] = K[i-1][w]          ← item too heavy, skip it

Else:
    K[i][w] = max(
        K[i-1][w],                ← exclude item i
        value[i] + K[i-1][w - weight[i]]  ← include item i
    )
```

**Base cases:** `K[0][w] = 0` for all w (no items), `K[i][0] = 0` for all i (no capacity).

After filling the entire table, the answer is `K[n][W]`. Selected items are found by **tracing back** through the table.

**Why is it O(n·W)?** There are `n × W` cells in the table, and each takes O(1) to compute.

### Brute Force — O(2ⁿ)

Try every possible **subset** of items (there are 2ⁿ of them), check if the total weight fits in the knapsack, and keep track of the best valid value found. Simple but exponentially slow — 10 items means 1,024 subsets; 30 items means over 1 billion.

### Why Not Greedy?

Sorting items by **value/weight ratio** and taking the best ones first works for the *fractional* knapsack (where you can take partial items), but **fails for 0/1** because items are discrete. Example:

| Item | Value | Weight | v/w Ratio |
|------|-------|--------|-----------|
| #1   | 6     | 1 kg   | 6.0 ← greedy picks first |
| #2   | 10    | 2 kg   | 5.0 ← greedy picks second |
| #3   | 12    | 3 kg   | 4.0 |

With W = 4: Greedy picks #1 + #2 = **value 16**. DP finds #1 + #3 = **value 18**. Greedy loses by 2.

---

## 🖥️ Visualizer Guide

### Layout

The interface is split into two panels:

- **Left panel** — Input controls, playback, step explanation, pseudocode, sorting intuition, greedy comparison.
- **Right panel** — Items display, DP table, results, brute force explorer, complexity chart, step history.

Responsive: collapses to a single column on screens under 980px.

---

### Left Panel Sections

#### Input
- Enter custom **Values**, **Weights**, and **Capacity** as comma-separated numbers.
- Click **▶ Build Steps** to generate all animation steps for the current input.
- Use **↺ Reset** to clear everything and start fresh.
- **Presets:** Four built-in examples to quickly load interesting cases.

| Preset | Purpose |
|--------|---------|
| Small Example | Simple 4-item walkthrough |
| Greedy Fails | Counterexample where greedy gives a suboptimal result |
| Max Capacity | Larger problem to see the table fill up |
| ⚠ Worst Case | 8 items, reveals the exponential gap between DP and brute force |

#### Playback Controls
| Button | Action |
|--------|--------|
| **‹ Prev** | Step backward one state |
| **Next ›** | Step forward one state |
| **▶ Play** | Auto-play through all steps |
| **⏸ Pause** | Pause auto-play |
| **⇥ End** | Jump to the final state |
| **Speed slider** | Control playback speed from 0.25× to 4× |

The progress bar and step counter (`Step X / Y`) update in real time.

#### Step Explanation Box
Shows a plain-English description of exactly what is happening at each step — which item is being considered, what the weight check result is, and whether the item was included or excluded.

#### Pseudocode Panel
Displays the full DP algorithm with line numbers. The **currently executing line is highlighted in blue** as you step through. Hover over any line to see a tooltip explaining what that line does.

#### Sorting Intuition
A bar chart showing each item sorted by its **value/weight ratio**, with greedy selections highlighted in amber. Illustrates why greedy seems appealing — and why it isn't always correct.

#### Greedy vs DP Counterexample
A fixed side-by-side comparison showing a concrete case where greedy fails, with the DP optimal result clearly winning.

---

### Right Panel Sections

#### Items Strip
Visual boxes for each item showing index, value, and weight. Items highlight in **cyan** when they are being evaluated, and turn **green** when they are selected as part of the optimal solution.

#### DP Table
The main visualization. Rows = items (0 to n), columns = capacity (0 to W).

| Cell color | Meaning |
|------------|---------|
| Dark/empty | Not yet computed |
| Dim white | Computed value |
| Cyan border | Currently being computed (active cell) |
| Green | Part of the optimal traceback path |

Cells animate with a brief pop effect when first written.

#### DP Result Banner
Appears after all steps complete. Shows the optimal value, which items were selected, total weight used, and value efficiency.

---

### Brute Force Explorer

Click **⚡ Build BF Steps** (after building DP steps) to generate all 2ⁿ subset evaluations for the same input.

**Current Subset Display** — shows all items color-coded:
- **Amber** = currently evaluating
- **Green** = fits within capacity (valid)
- **Red** = over capacity (rejected)
- **White glow** = current best solution found so far

**Subset Log** — a scrollable table showing every evaluated subset as a binary bitmask, the items included, total value, total weight, and a pass/fail/best status. The current row is highlighted. Auto-scrolls as the animation plays.

**Live BF Counters** — track Subsets Checked, Weight Checks, Theory 2ⁿ, and Best Value in real time.

**DP vs BF Comparison** — appears when BF finishes. Side-by-side card showing:
- Operations used by each approach
- Theoretical vs actual counts
- Efficiency ratio (BF ops ÷ DP ops)
- Confirmation that both found the same optimal answer

---

#### Complexity Growth Chart

An SVG chart (no libraries) plotting four series simultaneously:

| Series | Color | Description |
|--------|-------|-------------|
| O(n·W) curve | Cyan | Theoretical DP growth |
| O(2ⁿ) curve | Amber dashed | Theoretical brute force growth |
| DP actual | Purple dot | Real ops from your current DP run |
| BF actual | Red dot | Real ops from your current BF run |

Run both DP and BF on the **Worst Case** preset to see the gap visually plotted on the same axes.

#### Step History Table
Logs the last 20 steps as you animate. Click any row to jump the animation directly to that state — useful for revisiting a specific decision.

---

## 📐 Technical Details

- **Single file** — everything in one `index.html`, no external dependencies except Google Fonts.
- **Vanilla JS** — no frameworks, no build tools.
- **SVG complexity chart** — drawn programmatically, no charting libraries.
- **Brute force limit** — capped at 20 items to keep the browser responsive (20 items = 1,048,576 subsets).
- **DP table limit** — capped at 10 items and capacity 25 for readable visualization.
- **Responsive** — single-column layout below 980px.

---

## 📁 File Structure

```
knapsack-visualizer/
└── index.html    ← the entire application
└── README.md     ← this file
```

---

## 📄 License

MIT — free to use, modify, and share.
