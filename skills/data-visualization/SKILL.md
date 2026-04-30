---
name: data-visualization
description: "Use this skill when the user asks to create charts, graphs, dashboards, data visualizations, or interactive data displays. Triggers for 'create a chart', 'visualize this data', 'make a dashboard', 'plot this', 'bar chart', 'line graph', 'pie chart', 'heatmap', 'scatter plot', 'data dashboard', or 'show me a graph of'. Covers chart type selection, data transformation for visualization, color schemes, accessibility in charts, responsive layouts, and interactive features. Produces standalone HTML artifacts using lightweight charting libraries."
license: Complete terms in LICENSE.txt
---

# Data Visualization

You are creating data visualizations. Choose the right chart type for the data, make it readable, accessible, and visually polished. Default to standalone HTML files that work without a build step.

## Chart Type Selection

| Data Relationship | Chart Type | When to Use |
| ----------------- | ---------- | ----------- |
| Comparison | Bar chart | Compare values across categories |
| Trend over time | Line chart | Show change over continuous time |
| Part of whole | Donut chart | Show proportions (max 6-7 segments) |
| Distribution | Histogram | Show frequency distribution |
| Correlation | Scatter plot | Show relationship between 2 variables |
| Ranking | Horizontal bar | Rank items from top to bottom |
| Geographic | Choropleth map | Data by region/country |
| Flow/process | Sankey diagram | Show flows between categories |
| Matrix comparison | Heatmap | Show intensity across two dimensions |
| Hierarchical | Treemap | Show nested proportions |

### Decision Rules

- **< 5 data points** → Use a table, not a chart
- **Comparing categories** → Bar chart (vertical for < 8, horizontal for more)
- **Time series** → Line chart (area chart if showing cumulative)
- **Proportions** → Donut chart (never pie — donuts are easier to read)
- **Two variables** → Scatter plot (add size for a third dimension)
- **Multiple metrics over time** → Small multiples, not one cluttered chart

## Technology Stack

### Default: Chart.js (via CDN)

Use Chart.js for standard charts. It's lightweight, well-documented, and renders to canvas.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Chart Title]</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js@4"></script>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body {
      font-family: system-ui, -apple-system, sans-serif;
      background: #fafafa;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      padding: 2rem;
    }
    .chart-container {
      background: white;
      border-radius: 12px;
      padding: 2rem;
      box-shadow: 0 1px 3px rgba(0,0,0,0.1);
      width: 100%;
      max-width: 800px;
    }
    h1 {
      font-size: 1.25rem;
      font-weight: 600;
      color: #1a1a1a;
      margin-bottom: 0.5rem;
    }
    .subtitle {
      font-size: 0.875rem;
      color: #666;
      margin-bottom: 1.5rem;
    }
  </style>
</head>
<body>
  <div class="chart-container">
    <h1>[Chart Title]</h1>
    <p class="subtitle">[Data source or description]</p>
    <canvas id="chart"></canvas>
  </div>

  <script>
    const ctx = document.getElementById('chart').getContext('2d');
    new Chart(ctx, {
      type: 'bar', // or 'line', 'doughnut', 'scatter', etc.
      data: {
        labels: ['Category A', 'Category B', 'Category C'],
        datasets: [{
          label: 'Series Name',
          data: [30, 50, 20],
          backgroundColor: ['#2563eb', '#7c3aed', '#059669'],
          borderRadius: 6,
        }]
      },
      options: {
        responsive: true,
        plugins: {
          legend: { display: false },
          tooltip: {
            backgroundColor: '#1a1a1a',
            titleFont: { size: 13 },
            bodyFont: { size: 12 },
            padding: 10,
            cornerRadius: 8,
          }
        },
        scales: {
          y: {
            beginAtZero: true,
            grid: { color: '#f0f0f0' },
            ticks: { font: { size: 12 } }
          },
          x: {
            grid: { display: false },
            ticks: { font: { size: 12 } }
          }
        }
      }
    });
  </script>
</body>
</html>
```

### Advanced: D3.js (for Custom Visualizations)

Use D3.js when Chart.js doesn't support the chart type (sankey, treemap, force graph) or when full customization is needed.

```html
<script src="https://cdn.jsdelivr.net/npm/d3@7"></script>
```

## Color Palettes

### Default Palette (Accessible)

```javascript
const colors = {
  primary:  ['#2563eb', '#7c3aed', '#059669', '#d97706', '#dc2626', '#0891b2'],
  neutral:  ['#1a1a1a', '#525252', '#a3a3a3', '#d4d4d4', '#f5f5f5'],
  positive: '#059669',  // green — growth, success
  negative: '#dc2626',  // red — decline, error
  highlight: '#2563eb', // blue — emphasis
};
```

### Rules

- Max **6 colors** in a single chart — more becomes unreadable
- Use **opacity variants** of one color for related data (e.g., `rgba(37,99,235,0.2)`)
- Sequential data → single-hue gradient (light to dark)
- Diverging data → two-hue gradient (red ← neutral → blue)
- Never use red/green as the only differentiator — color-blind users can't distinguish them
- Always include labels or patterns as a secondary differentiator

### Color-Blind Safe Alternatives

```javascript
const colorBlindSafe = ['#0072B2', '#E69F00', '#009E73', '#CC79A7', '#56B4E9', '#D55E00'];
```

## Typography & Formatting

| Element | Format |
| ------- | ------ |
| Numbers > 999 | Use locale formatting: `1,234` or `1.234` |
| Large numbers | Abbreviate: `1.2M`, `340K` |
| Currency | Include symbol: `$1,234.56` |
| Percentages | One decimal: `42.1%` |
| Dates on axes | Short format: `Jan`, `Feb` or `Q1 '25` |
| Axis labels | Sentence case, include units: `Revenue ($M)` |

```javascript
// Number formatting helper
function formatNumber(n) {
  if (Math.abs(n) >= 1e9) return (n / 1e9).toFixed(1) + 'B';
  if (Math.abs(n) >= 1e6) return (n / 1e6).toFixed(1) + 'M';
  if (Math.abs(n) >= 1e3) return (n / 1e3).toFixed(1) + 'K';
  return n.toLocaleString();
}
```

## Dashboard Layout

For multi-chart dashboards, use CSS grid:

```html
<style>
  .dashboard {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(400px, 1fr));
    gap: 1.5rem;
    padding: 2rem;
    max-width: 1400px;
    margin: 0 auto;
  }
  .card {
    background: white;
    border-radius: 12px;
    padding: 1.5rem;
    box-shadow: 0 1px 3px rgba(0,0,0,0.1);
  }
  .card--full { grid-column: 1 / -1; }
  .kpi-row {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 1rem;
    grid-column: 1 / -1;
  }
  .kpi {
    background: white;
    border-radius: 12px;
    padding: 1.5rem;
    box-shadow: 0 1px 3px rgba(0,0,0,0.1);
  }
  .kpi-value { font-size: 2rem; font-weight: 700; color: #1a1a1a; }
  .kpi-label { font-size: 0.875rem; color: #666; margin-top: 0.25rem; }
  .kpi-change { font-size: 0.8rem; margin-top: 0.5rem; }
  .kpi-change.up { color: #059669; }
  .kpi-change.down { color: #dc2626; }
</style>
```

### KPI Cards

```html
<div class="kpi">
  <div class="kpi-value">$1.2M</div>
  <div class="kpi-label">Total Revenue</div>
  <div class="kpi-change up">↑ 12.3% vs last month</div>
</div>
```

## Accessibility

- Every chart must have a text alternative (title + description or `aria-label`)
- Canvas-based charts need a `<p>` description after them for screen readers
- Use patterns/textures in addition to color when possible
- Ensure contrast ratio of 3:1 minimum for data elements against background
- Include data tables as a toggle for screen reader users

```html
<canvas id="chart" role="img" aria-label="Bar chart showing quarterly revenue: Q1 $1.2M, Q2 $1.5M, Q3 $1.3M, Q4 $1.8M"></canvas>
<details>
  <summary>View data table</summary>
  <table>
    <thead><tr><th>Quarter</th><th>Revenue</th></tr></thead>
    <tbody>
      <tr><td>Q1</td><td>$1.2M</td></tr>
      <tr><td>Q2</td><td>$1.5M</td></tr>
      <tr><td>Q3</td><td>$1.3M</td></tr>
      <tr><td>Q4</td><td>$1.8M</td></tr>
    </tbody>
  </table>
</details>
```

## Rules

- Every chart must have a **title** and **subtitle** explaining what it shows
- Always start Y-axis at 0 for bar charts (never truncate to exaggerate differences)
- Line charts may use a non-zero baseline when the range is narrow
- Remove chart junk: no 3D effects, no unnecessary gridlines, no decorative elements
- Data labels: show on bars when < 8 items, use tooltips when more
- Responsive: charts must resize with the container
- Animation: subtle entrance only (`animation: { duration: 600 }`) — never looping
- Always provide the raw data alongside the chart (in a `<details>` toggle or as comments)
- When processing user data, validate and clean it before plotting

## Integration with Other Skills

- **`web-artifacts-builder`** — For embedding charts in interactive HTML artifacts
- **`xlsx`** — Read data from spreadsheets for visualization
- **`pdf`** — Generate PDF reports containing charts
- **`frontend-design`** — Integrate charts into application UIs
