# Phoebe-JS Quick Reference

Quick lookup for common patterns and controls.

## Standard Demo Template

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Demo Title</title>
    <script src="../phoebe-js/mathhelper.js"></script>
    <script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.js"></script>
    <link rel="stylesheet" href="../styles.css">
    <link rel="stylesheet" href="../phoebe-js/phoebe.css">
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="iframe-content">
        <div class="container">
            <!-- Demo content -->
        </div>
    </div>
    <script src="../phoebe-js/infotab.js"></script>
    <script src="../phoebe-js/metriclabel.js"></script>
    <script src="script.js"></script>
</body>
</html>
```

---

## Layout Patterns

### 70/30 Split Layout
```html
<div class="demo-area">
    <div class="left-column-wrapper">
        <!-- Chart and controls (70%) -->
    </div>
    <div class="controls">
        <!-- Sidebar controls (30%) -->
    </div>
</div>
```

```css
.demo-area {
    display: flex;
    gap: 20px;
    align-items: stretch;
}

.left-column-wrapper {
    flex: 0 0 calc((100% - 20px) * 0.70);
    box-sizing: border-box;
}

.controls {
    flex: 0 0 calc((100% - 20px) * 0.30);
    background: #f5f7fa;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    border: 1px solid #dee2e6;
    box-sizing: border-box;
}
```

### 65/35 Split Layout
Same as above, just change ratios to `0.65` and `0.35`.

---

## Common Components

### Info Tab Navigation
```html
<div class="infotab">
    <div class="infotab-header">
        <!-- Auto-generated -->
    </div>
    <div class="infotab-content">
        <div class="infotab-panel" data-tab-title="Introduction">...</div>
        <div class="infotab-panel" data-tab-title="Instructions">...</div>
    </div>
</div>
```

### ParamSlider (HTML Structure)
```html
<div class="control-group">
    <label for="alpha">$\alpha$ (Slope)</label>
    <div class="slider-container">
        <input type="range" id="alpha" min="-5" max="5" step="0.1" value="0.1">
        <div class="value-display" id="alpha-value">0.1</div>
    </div>
</div>
```

### Metric Tracker Grid
```html
<div class="metric_tracker_grid">
    <div class="metric_tracker_box metric-sensitivity">
        <div class="metric_tracker_label">Sensitivity</div>
        <div class="metric_tracker_value" id="metric-sensitivity">0.00</div>
        <div class="metric_tracker_bar">
            <div class="metric_tracker_bar_fill" id="bar-sensitivity"></div>
        </div>
        <div class="metric_tracker_formula">TP / (TP + FN)</div>
    </div>
</div>
```

Add custom colors in demo CSS:
```css
.metric-sensitivity .metric_tracker_value {
    color: #27ae60;
}
.metric-sensitivity .metric_tracker_bar_fill {
    background: linear-gradient(90deg, #27ae60, #2ecc71);
}
```

### Feature Box Grid
```html
<div class="feature-box-grid">
    <div class="feature-box active" data-type="linear">
        <div class="feature-icon">📏</div>
        <div class="feature-label">Linear</div>
    </div>
    <div class="feature-box" data-type="quadratic">
        <div class="feature-icon">〰️</div>
        <div class="feature-label">Quadratic</div>
    </div>
</div>
```

### Horizontal Scroll Table
```html
<div class="horizontal-scroll-container">
    <div class="horizontal-scroll-title">Predictions Table</div>
    <div class="horizontal-scroll">
        <table class="horizontal-scroll-table">
            <tbody>
                <tr><th>ID</th><td>1</td><td>2</td></tr>
                <tr><th>Value</th><td>0.5</td><td>0.8</td></tr>
            </tbody>
        </table>
    </div>
</div>
```

---

## Button Styles

### Standard Buttons
```html
<button class="train-btn">Train Model</button>
<button class="stop-btn">Stop Training</button>
<button class="reset-btn">Reset</button>
```

### Data Control Buttons
```html
<div class="data-buttons-panel">
    <button class="data-btn" id="generate-data-btn">Generate New Data</button>
    <button class="data-btn" id="clear-data-btn">Clear All Data</button>
    <button class="data-btn solution-btn" id="solution-btn">Find Optimal Solution</button>
</div>
```

Add custom colors:
```css
#generate-data-btn {
    background: #27ae60;
}
#generate-data-btn:hover {
    background: #229954;
}
```

---

## JavaScript Patterns

### Metric Box Display
```javascript
const metricBox = new MetricLabel('element-id', {
    label: 'Loss',
    labelPosition: 'bottom-right'
});
metricBox.update('MSE = 0.234');
```

### ParamSlider (JavaScript)
```javascript
const slider = new ParamSlider('alpha', {
    min: -5,
    max: 5,
    step: 0.1,
    value: 0.1,
    onChange: (value) => {
        console.log('Slider changed:', value);
    }
});
```

### Update Metric Tracker
```javascript
function updateMetrics(sensitivity) {
    const valueElement = document.getElementById('metric-sensitivity');
    const barElement = document.getElementById('bar-sensitivity');

    valueElement.textContent = (sensitivity * 100).toFixed(1) + '%';
    barElement.style.width = (sensitivity * 100) + '%';
}
```

---

## Responsive Design

### Standard Breakpoint
```css
@media (max-width: 768px) {
    .demo-area {
        flex-direction: column;
    }

    .left-column-wrapper,
    .controls {
        flex: 1 1 100%;
        width: 100%;
    }
}
```

---

## Color Palette

### Standard Colors
```css
/* Primary Actions */
.train-btn: #3498db (blue)
.stop-btn: #e74c3c (red)
.reset-btn: #95a5a6 (grey)

/* Data Actions */
#generate-data-btn: #27ae60 (green)
#clear-data-btn: #e74c3c (red)
#solution-btn: #9b59b6 (purple)

/* Backgrounds */
.controls: #f5f7fa
.plot-container: white

/* Borders */
border: #ddd or #dee2e6

/* Metrics */
Sensitivity: #27ae60 (green)
Specificity: #2980b9 (blue)
Precision: #8e44ad (purple)
Accuracy: #7f8c8d (grey)
F1-Score: #d35400 (orange)
Balanced: #16a085 (teal)
```

---

## Width Calculations

### With Gap
```css
/* For 70/30 split with 20px gap */
.left-part {
    flex: 0 0 calc((100% - 20px) * 0.70);
}
.right-part {
    flex: 0 0 calc((100% - 20px) * 0.30);
}
```

Always use:
- `box-sizing: border-box;` on flex items
- `calc((100% - gap) * ratio)` for width calculations
- `gap: 20px;` on flex container

---

## Chart.js Responsive Setup

### Fixed Height Container
```html
<div style="position: relative; height: 450px;">
    <canvas id="chart"></canvas>
</div>
```

```javascript
options: {
    responsive: true,
    maintainAspectRatio: false,
    // ... other options
}
```

---

## Common CSS Classes Reference

### Layout
- `.demo-area` - Main demo container
- `.left-column-wrapper` - Left column wrapper
- `.controls` - Right sidebar controls panel
- `.plot-container` - Chart container
- `.control-row` - Horizontal control row

### Controls
- `.control-group` - Individual control wrapper
- `.slider-container` - Slider with value display
- `.value-display` - Slider value display
- `.label-with-buttons` - Label with fine-tune buttons

### Buttons
- `.train-btn` - Primary action button
- `.stop-btn` - Stop action button
- `.reset-btn` - Reset button
- `.data-btn` - Data control button

### Feature Boxes
- `.feature-box-grid` - Grid container for feature boxes
- `.feature-box` - Individual feature box
- `.feature-box.active` - Active state

### Metric Tracker
- `.metric_tracker_grid` - Grid container for metrics
- `.metric_tracker_box` - Individual metric box
- `.metric_tracker_label` - Metric label
- `.metric_tracker_value` - Metric value
- `.metric_tracker_bar` - Progress bar container
- `.metric_tracker_bar_fill` - Progress bar fill
- `.metric_tracker_formula` - Formula text

### Tables
- `.horizontal-scroll-container` - Scrollable table wrapper
- `.horizontal-scroll-title` - Table title
- `.horizontal-scroll` - Scroll container
- `.horizontal-scroll-table` - Table element
