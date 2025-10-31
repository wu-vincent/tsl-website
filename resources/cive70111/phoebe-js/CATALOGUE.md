# Phoebe-JS Control Library

Shared controls, utilities, and stylesheets for CIVE70111 interactive demos.

## CSS Frameworks

### phoebe.css
**Purpose**: Comprehensive CSS framework with reusable UI patterns
**Usage**: `<link rel="stylesheet" href="../phoebe-js/phoebe.css">`
**Documentation**: See `fancybox-examples.md` for FancyBox component usage

**Patterns included**:
- **Control Panel**: `.controls` - Standard sidebar styling with padding, background, and shadow
- **Feature Box Grid**: `.feature-box-grid`, `.feature-box` - Toggleable boxes in grid layout with active states
- **FancyBox**: `.fancybox`, `.fancybox-panel`, `.fancybox-container` - Interactive selection boxes with SVG/emoji icons and color themes
- **InfoTab**: `.infotab`, `.infotab-header`, `.infotab-btn`, `.infotab-panel` - Tab navigation system for informational content
- **SelectorBox**: `.selectorbox` - Simple toggle boxes using data attributes only
- **Horizontal Scroll Tables**: `.horizontal-scroll-container`, `.horizontal-scroll-table` - Scrollable tables with sticky columns
- **Metric Tracker**: `.metric_tracker_grid`, `.metric_tracker_box` - Progress bars with labels, values, and formulas
- **MetricLabel**: `.metriclabel`, `.metriclabel-{color}` - Metric display boxes with theme colors
- **Plot Containers**: `.plot-container` - Standard chart container styling
- **Slider Controls**: `.slider-container`, `.value-display` - Range inputs with value displays
- **Button Styles**: `.train-btn`, `.stop-btn`, `.reset-btn` - Common action buttons
- **Layout Helpers**: Flex-based layouts, responsive breakpoints at 768px

See file header comments for detailed usage patterns.

---

## JavaScript Controls

### infotab.js
**Purpose**: Info tab navigation system
**Usage**: `<script src="../phoebe-js/infotab.js"></script>`

**Features**:
- Automatic tab initialization from `data-tab-title` attributes
- Active tab state management
- Click event handling

**HTML Structure**:
```html
<div class="infotab">
    <div class="infotab-header">
        <!-- Auto-generated tab buttons -->
    </div>
    <div class="infotab-content">
        <div class="infotab-panel" data-tab-title="Introduction">...</div>
        <div class="infotab-panel" data-tab-title="Instructions">...</div>
    </div>
</div>
```

---

### metriclabel.js
**Purpose**: Utility for displaying metrics with MathJax rendering
**Usage**: `<script src="../phoebe-js/metriclabel.js"></script>`
**Documentation**: See `metriclabel-examples.md`

**Key Classes**:
- `MetricLabel` - Manages metric box content with MathJax rendering
- `MetricLabelManager` - Manages multiple metric boxes

**Example**:
```javascript
const metricBox = new MetricLabel('equation-element-id', {
    label: 'Loss',
    labelPosition: 'bottom-right'
});
metricBox.update('MSE = 0.234');
```

---

### paramslider.js
**Purpose**: Enhanced parameter slider controls with value displays and fine-tuning
**Usage**: `<script src="../phoebe-js/paramslider.js"></script>`
**Documentation**: See `paramslider-examples.md`

**Key Classes**:
- `ParamSlider` - Individual slider with value display
- `ParamSliderManager` - Manages multiple sliders

**Example**:
```javascript
const slider = new ParamSlider('alpha', {
    min: -5,
    max: 5,
    step: 0.1,
    value: 0.1,
    onChange: (value) => console.log(value)
});
```

---

## Utility Libraries

### canvashelper.js
**Purpose**: Canvas helper functions
**Usage**: `<script src="../phoebe-js/canvashelper.js"></script>`
**Documentation**: See `canvashelper-examples.md`

**Features**:
- High-DPI canvas setup
- Chart.js overlay alignment
- Coordinate transformations (data ↔ pixel)
- Error square visualization

---

### datamanager.js
**Purpose**: Unified data management - CSV parsing, file loading, and transformations
**Usage**: `<script src="../phoebe-js/datamanager.js"></script>`
**Documentation**: See `datamanager-examples.md`

**Key Classes**:
- `DataManager` - All-in-one data management utility

**Features**:
- **CSV Parsing** (sync): `parseCSV()`, `toCSV()`, `extractColumns()`, `extractXY()`
- **File Loading** (async): `loadCSV()`, `loadJSON()`, `loadFull()`, `loadMultiple()`
- **Data Analysis**: `filter()`, `sort()`, `columnStats()`
- **Transformations**: `normalize()`, `standardize()`, `trainTestSplit()`, `calculateBounds()`

**Example**:
```javascript
// Load CSV and extract x, y columns
const data = await DataManager.loadCSV('data.csv', 'temp', 'demand');

// Calculate bounds
const bounds = DataManager.calculateBounds(data, 10);
```

**Note**: Replaces the previous separate `csv-parser.js` and `dataset-loader.js` files

---

### mathhelper.js
**Purpose**: Unified MathJax configuration and utilities
**Usage**: `<script src="../phoebe-js/mathhelper.js"></script>`

**Features**:
- MathJax configuration (inline/display math, TeX settings, CHTML output)
- `MathHelper` utility class with static methods:
  - `waitForReady()` - Wait for MathJax initialization
  - `renderElements()` - Render specific elements
  - `renderAll()` - Render entire document
  - `tex2html()` - Convert TeX to HTML
  - `polynomialToTex()` - Generate polynomial equations
  - `formatNumber()` - Scientific notation formatting

---

### metrics.js
**Purpose**: Classification and regression metrics
**Usage**: `<script src="../phoebe-js/metrics.js"></script>`

**Features**:
- MSE, RMSE, MAE calculations
- Classification metrics (accuracy, precision, recall, F1)
- Confusion matrix generation

---

### linalg.js
**Purpose**: Lightweight linear algebra helpers for demo-sized datasets
**Usage**: `<script src="../phoebe-js/linalg.js"></script>`

**API**:
- `LinAlg.multiply(A, B)` – Matrix multiplication (m×n by n×p)
- `LinAlg.vectorMultiply(A, v)` – Matrix–vector product
- `LinAlg.transpose(A)` – Matrix transpose
- `LinAlg.gaussianElimination(A, b, tolerance)` – Solve square systems with partial pivoting
- `LinAlg.addBiasColumn(X)` – Prepend bias term to feature matrix
- `LinAlg.predict(X, θ)` – Convenience wrapper for design-matrix predictions

**Performance Notes**:
- Implementations favour clarity over asymptotic speed (O(n³) Gaussian elimination). Suitable for the ≤12×12 systems used in the polynomial regression demos.
- Partial pivoting guards against near-singular matrices, but large λ values in ridge regression can further stabilise solutions when extending to high-degree models.
- Future factorisation upgrades (e.g. Cholesky for symmetric positive definite matrices) can drop into the same API surface without changing demo code.

---

## Usage Pattern

**Typical demo includes**:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Demo Title</title>

    <!-- MathJax -->
    <script src="../phoebe-js/mathhelper.js"></script>
    <script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>

    <!-- Chart.js (if needed) -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.js"></script>

    <!-- Shared Styles -->
    <link rel="stylesheet" href="../styles.css">
    <link rel="stylesheet" href="../phoebe-js/phoebe.css">
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <!-- Demo content -->

    <!-- Shared Scripts -->
    <script src="../phoebe-js/infotab.js"></script>
    <script src="../phoebe-js/metriclabel.js"></script>
    <script src="script.js"></script>
</body>
</html>
```

---

## Directory Structure

```
phoebe-js/
├── CATALOGUE.md                    # This file
├── phoebe.css                      # Main CSS framework (FancyBox, InfoTab, SelectorBox, MetricLabel, etc.)
├── infotab.js                      # Info tab navigation JS
├── metriclabel.js                  # Metric display utility
├── metriclabel-examples.md         # Metric display documentation
├── paramslider.js                  # Parameter slider utility
├── paramslider-examples.md         # Parameter slider documentation
├── fancybox-examples.md            # FancyBox component documentation
├── canvashelper.js                 # Canvas helpers
├── canvashelper-examples.md        # CanvasHelper documentation
├── datamanager.js                  # CSV parsing, loading, transformations
├── datamanager-examples.md         # DataManager documentation
├── mathhelper.js                   # MathJax config & utilities
└── metrics.js                      # Metrics calculations
```

---

## Naming Conventions

- **CSS classes**: Use underscore for component names (e.g., `.metric_tracker_box`)
- **JS classes**: Use PascalCase (e.g., `MetricLabel`)
- **Files**: Use kebab-case (e.g., `metriclabel.js`)

---

## Adding New Controls

When adding new shared controls:

1. Add the file to `phoebe-js/` directory
2. Update this CATALOGUE.md with documentation
3. Follow existing naming conventions
4. Include usage examples
5. Add header comments in the file
6. Update relevant demos to use the new control

---

## TODO: Future Considerations

### Demo-Tab Control Unification
**Status**: Under consideration
**Context**: Currently, some demos use local "demo-tab" classes (`.demo-tab-header`, `.demo-tab-button`, `.demo-tab-content`) for secondary tab controls within the demo content (separate from the top-level InfoTab navigation).

**Current implementations**:
- `lec03d_gradient-descent-with-regularisation`: L2/L1 tabs with custom `switchDemoTab()` function
- `lec04c_applying-neural-networks`: Architecture/Evaluate/Predict tabs with inline switching logic

**Question**: Should these be centralized into a reusable `DemoTabController` (similar to `InfoTab`)?

**Considerations**:
- Both implementations are similar but have slight variations
- Names suggest they serve different purposes (InfoTab = informational, DemoTab = functional content)
- May not need centralization if use cases diverge significantly
- If more demos need similar controls, centralization becomes more valuable

**Decision needed**: Evaluate whether the implementations are truly similar enough to warrant a shared control, or if they represent different interaction patterns that should remain demo-specific.
