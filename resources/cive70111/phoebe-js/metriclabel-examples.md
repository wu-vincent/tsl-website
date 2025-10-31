# MetricLabel - Usage Examples

The `metriclabel.js` utility provides a standardized, reusable component for metric display boxes with MathJax rendering, theme-based colors, and optional labels.

## Basic Setup

### 1. Include the script in your HTML

```html
<script src="../phoebe-js/metriclabel.js"></script>
<script src="script.js"></script>
```

### 2. HTML Structure

Simple `.metriclabel` boxes with just IDs (no custom CSS classes needed):

```html
<!-- Basic MSE box -->
<div class="metriclabel" id="mse">
    MSE = 0.0
</div>

<!-- Training set MSE -->
<div class="metriclabel" id="train-mse">
    MSE = 0.0
</div>

<!-- Test set MSE -->
<div class="metriclabel" id="test-mse">
    MSE = 0.0
</div>

<!-- MAE box -->
<div class="metriclabel" id="mae">
    MAE = 0.0
</div>
```

### 3. No Inline Styles or Demo-Specific CSS!

**Recommended approach**: Use color theme presets from `phoebe.css`. Colors are applied via CSS classes - no inline styles generated!

**Legacy approach**: You can still pass individual color properties for backward compatibility, but this creates inline styles.

## Usage Examples

### Example 1: Single MSE Box (Recommended - Theme-based)

```javascript
// Initialize with purple theme and label (clean, no inline styles)
const mseDisplay = new MetricLabel('mse', 'MSE', {
    theme: 'purple',
    label: 'Mean Squared Error',
    decimals: 2
});

// Update the display with a numeric value
function updateMetrics() {
    const mse = calculateMSE(yTrue, yPred);
    mseDisplay.update(mse);
}

// Show placeholder when no data available
mseDisplay.update(null);  // Shows "MSE = ?"
// or
mseDisplay.update('?');   // Also shows "MSE = ?"
```

### Example 2: Dual MSE Boxes (Recommended - Theme-based)

```javascript
// Initialize displays with theme colors
const displays = {
    trainMse: new MetricLabel('train-mse', 'MSE', {
        theme: 'blue',
        label: 'Training Set',
        decimals: 1
    }),
    testMse: new MetricLabel('test-mse', 'MSE', {
        theme: 'orange',
        label: 'Test Set',
        decimals: 1
    })
};

// Batch update (more efficient)
function updateMetrics() {
    const trainMSE = calculateMSE(trainY, trainPred);
    const testMSE = calculateMSE(testY, testPred);

    MetricLabel.batchUpdate([
        { display: displays.trainMse, value: trainMSE },
        { display: displays.testMse, value: testMSE }
    ]);
}
```

### Example 3: Multiple Metrics with Different Themes

```javascript
const displays = {
    mse: new MetricLabel('mse', 'MSE', {
        theme: 'purple',
        label: 'Mean Squared Error',
        decimals: 2
    }),
    mae: new MetricLabel('mae', 'MAE', {
        theme: 'green',
        label: 'Mean Absolute Error',
        decimals: 2
    }),
    r2: new MetricLabel('r2', 'R²', {
        theme: 'blue',
        decimals: 3
    })
};

function updateMetrics() {
    const mse = calculateMSE(yTrue, yPred);
    const mae = calculateMAE(yTrue, yPred);
    const r2 = calculateR2(yTrue, yPred);

    MetricLabel.batchUpdate([
        { display: displays.mse, value: mse },
        { display: displays.mae, value: mae },
        { display: displays.r2, value: r2 }
    ]);
}
```

### Example 4: Custom Metric Names

```javascript
// Loss display
const lossDisplay = new MetricLabel('loss', 'Loss', {
    theme: 'green',
    label: 'Cross-Entropy',
    decimals: 4
});

// Accuracy display (no label)
const accDisplay = new MetricLabel('accuracy', 'Accuracy', {
    theme: 'purple',
    decimals: 1,
    unit: '%'
});

// Update
lossDisplay.update(0.1234);
accDisplay.update(95.6);
```

### Example 5: Legacy Custom Colors (Backward Compatibility)

If you need custom colors not available in the theme palette, you can still use individual color properties. This will generate inline styles:

```javascript
// Custom grey theme (not in preset)
const customDisplay = new MetricLabel('custom', 'Custom', {
    background: '#f8f9fa',
    color: '#2c3e50',
    border: '#dee2e6',
    borderLeft: '#6c757d',
    label: 'Custom Metric',
    decimals: 2
});

customDisplay.update(123.45);
```

### Example 6: Coordinated Update with Equation

When updating metrics alongside MathJax equations:

```javascript
function updateMetrics() {
    const trainMSE = calculateMSE(trainY, trainPred);
    const testMSE = calculateMSE(testY, testPred);

    // Update equation
    const equationEl = document.getElementById('equation');
    equationEl.innerHTML = '$y = \\beta_0 + \\beta_1 x$';

    // Update displays without triggering MathJax individually
    displays.trainMse.update(trainMSE, false);
    displays.testMse.update(testMSE, false);

    // Single MathJax render for all elements
    if (window.MathJax) {
        MathJax.typesetPromise([
            equationEl,
            displays.trainMse.element,
            displays.testMse.element
        ]).catch((err) => console.log('MathJax error:', err));
    }
}
```

## Available Color Themes

MetricLabel provides 7 preset color themes defined in `phoebe.css`. Just pass the theme name - no hex codes needed!

| Theme    | Usage Example | Common Use Cases |
|----------|--------------|------------------|
| `blue`   | `theme: 'blue'` | Training data, R², general metrics |
| `orange` | `theme: 'orange'` | Test data, warnings, counts |
| `green`  | `theme: 'green'` | MAE, success metrics, loss |
| `purple` | `theme: 'purple'` | MSE, accuracy, primary metrics |
| `red`    | `theme: 'red'` | Errors, poor performance |
| `teal`   | `theme: 'teal'` | Alternative color for variety |
| `indigo` | `theme: 'indigo'` | Alternative color for variety |

**Why use themes instead of custom colors?**
- ✅ No inline styles - cleaner HTML
- ✅ Consistent color palette across all demos
- ✅ Easy to maintain - change colors globally in CSS
- ✅ Less code - just pass theme name
- ✅ Better performance - CSS classes vs inline styles

**Example:**
```javascript
// Recommended: Theme-based (3 lines)
const mseDisplay = new MetricLabel('mse', 'MSE', {
    theme: 'purple',
    decimals: 2
});

// Legacy: Custom colors (8 lines + inline styles)
const mseDisplay = new MetricLabel('mse', 'MSE', {
    background: '#f3e5f5',
    color: '#7b1fa2',
    border: '#e1bee7',
    borderLeft: '#9c27b0',
    decimals: 2
});
```

## API Reference

### Constructor

```javascript
new MetricLabel(elementId, metricName, options)
```

**Parameters:**
- `elementId` (string): ID of the HTML element to update
- `metricName` (string): Name of the metric (e.g., 'MSE', 'MAE', 'R²')
- `options` (object):
  - `theme` (string): **Recommended** - Color theme name ('blue', 'orange', 'green', 'purple', 'red', 'teal', 'indigo')
  - `background` (string): **Legacy** - Background color (generates inline style)
  - `color` (string): **Legacy** - Text color (generates inline style)
  - `border` (string): **Legacy** - Border color (generates inline style)
  - `borderLeft` (string): **Legacy** - Left border color (generates inline style)
  - `label` (string|null): Optional label for bottom-right corner
  - `decimals` (number): Number of decimal places (default: 2)
  - `separator` (string): Separator between metric name and value ('=' or ':', default: '=')
  - `unit` (string): Unit to display after value (e.g., '%', 'k', default: '')
  - `thousandsSeparator` (boolean): Whether to add comma separators (default: false)

### Instance Methods

#### `update(value, triggerMathJax = true)`

Update the display with a new value.

**Parameters:**
- `value` (number|string|null): The metric value to display
  - **number**: Formatted with specified decimal places (e.g., `123.45`)
  - **null/undefined**: Shows placeholder "?" (e.g., `MSE = ?`)
  - **string**: Displays as-is (e.g., `'?'` shows `MSE = ?`)
- `triggerMathJax` (boolean): Whether to trigger MathJax rendering (default: true)

**Examples:**
```javascript
mseDisplay.update(123.456);  // Shows "MSE = 123.46"
mseDisplay.update(null);     // Shows "MSE = ?"
mseDisplay.update('?');      // Shows "MSE = ?"
```

### Static Methods

#### `MetricLabel.batchUpdate(updates)`

Efficiently update multiple displays with a single MathJax render call.

**Parameters:**
- `updates` (Array): Array of `{display, value}` objects

**Example:**
```javascript
MetricLabel.batchUpdate([
    { display: trainMse, value: 100.5 },
    { display: testMse, value: 250.3 }
]);
```

## Benefits

1. **No Inline Styles**: Theme-based approach keeps HTML clean (CSS classes instead of inline styles)
2. **Consistent Color Palette**: Reusable themes across all demos for visual consistency
3. **Less Code**: Pass theme name instead of 4 color properties (75% less configuration code)
4. **Easy Maintenance**: Change colors globally in CSS instead of updating each demo
5. **MathJax Integration**: Automatic MathJax rendering with proper escaping
6. **Performance**: Batch updates minimize MathJax re-renders, CSS classes faster than inline styles
7. **Flexibility**: Still supports custom colors via legacy mode when needed
8. **Single Source of Truth**: All metric display logic centralized in one class

## Complete Example

Here's a complete working example using the theme-based approach:

**HTML:**
```html
<div class="metriclabel" id="train-mse">MSE = 0.0</div>
<div class="metriclabel" id="test-mse">MSE = 0.0</div>

<script src="../phoebe-js/metriclabel.js"></script>
<script src="script.js"></script>
```

**JavaScript (script.js):**
```javascript
let displays;

function init() {
    // Initialize displays with theme colors
    displays = {
        trainMse: new MetricLabel('train-mse', 'MSE', {
            theme: 'blue',
            label: 'Training Set',
            decimals: 1,
            thousandsSeparator: true
        }),
        testMse: new MetricLabel('test-mse', 'MSE', {
            theme: 'orange',
            label: 'Test Set',
            decimals: 1,
            thousandsSeparator: true
        })
    };

    // Initial update
    updateMetrics();
}

function updateMetrics() {
    const trainMSE = 8540.6;
    const testMSE = 1200.5;

    MetricLabel.batchUpdate([
        { display: displays.trainMse, value: trainMSE },
        { display: displays.testMse, value: testMSE }
    ]);
}

init();
```

**Result:**
- No inline styles in HTML - just CSS classes
- Clean, maintainable code with 75% less configuration
- Consistent colors from global palette
- Full MathJax support built in
