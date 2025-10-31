# ParamSlider - Usage Examples

The `paramslider.js` utility provides a standardized, reusable component for slider controls with automatic HTML generation, value display, optional fine-tune buttons, MathJax label support, and flexible formatting.

## Basic Setup

### 1. Include the script in your HTML

```html
<script src="../phoebe-js/paramslider.js"></script>
<script src="script.js"></script>
```

### 2. HTML Structure

Simply create an empty container div for each slider using the `paramslider-{name}` naming convention:
```html
<div id="paramslider-alpha"></div>
<div id="paramslider-beta"></div>
```

The ParamSlider component will automatically generate all required HTML structure.

---

## Usage Examples

### Example 1: Simple Linear Slider (Recommended)

**HTML:** `<div id="paramslider-degree"></div>`

```javascript
const degreeSlider = new ParamSlider('paramslider-degree', {
    inputId: 'degree',
    label: 'Polynomial Degree',
    min: 1,
    max: 10,
    step: 1,
    value: 1,
    onChange: (value) => {
        console.log('Degree changed to:', value);
        this.updateModel();
    }
});

// Generates complete HTML structure automatically
// Decimals auto-detected from step (step=1 → decimals=0)
// Value display shows: "1"
```

### Example 2: Slider with MathJax Label

**HTML:** `<div id="paramslider-alpha"></div>`

```javascript
const alphaSlider = new ParamSlider('paramslider-alpha', {
    inputId: 'alpha',
    label: '$\\alpha$ (Intercept)',
    min: -100,
    max: 200,
    step: 0.1,
    value: 0,
    onChange: (value) => {
        this.updateDisplay();
        this.updateChart();
    }
});

// MathJax automatically renders the label
// Value display shows: "0.0"
```

### Example 3: Slider with Fine-Tune Buttons

**HTML:** `<div id="paramslider-beta"></div>`

```javascript
const betaSlider = new ParamSlider('paramslider-beta', {
    inputId: 'beta',
    label: '$\\beta$ (Slope)',
    min: -10,
    max: 30,
    step: 0.01,
    value: 1,
    fineTuneStep: 0.01,  // Creates +/- buttons!
    onChange: (value) => {
        this.updateRegression();
    }
});

// Component automatically creates:
// - Label with +/- buttons in label-with-buttons wrapper
// - Slider with value display
// - Button clicks adjust by fineTuneStep
```

### Example 4: Custom Value Formatting

**HTML:** `<div id="paramslider-eta"></div>`

```javascript
const learningRateSlider = new ParamSlider('paramslider-eta', {
    inputId: 'eta',
    label: '$\\eta$ (Learning Rate)',
    min: 0.001,
    max: 1.5,
    step: 0.001,
    value: 0.1,
    formatValue: (value) => value.toExponential(2),  // Custom formatter
    onChange: (value) => {
        this.runGradientDescent();
    }
});

// Value display shows: "1.00e-1"
```

### Example 5: Prefix/Suffix

**HTML:** `<div id="paramslider-weight"></div>`

```javascript
const weightSlider = new ParamSlider('paramslider-weight', {
    inputId: 'weight',
    label: 'Weight',
    min: 0,
    max: 100,
    step: 0.1,
    value: 50,
    prefix: '',
    suffix: ' kg',
    onChange: (value) => {
        this.updatePhysics();
    }
});

// Value display shows: "50.0 kg"
```

### Example 6: Debounced Updates (Performance)

**HTML:** `<div id="paramslider-complexity"></div>`

```javascript
const complexSlider = new ParamSlider('paramslider-complexity', {
    inputId: 'complexity',
    label: 'Model Complexity',
    min: 1,
    max: 100,
    step: 1,
    value: 10,
    debounceMs: 150,  // Wait 150ms after user stops moving
    onChange: (value) => {
        // This expensive operation only runs after debounce delay
        this.retrainComplexModel();
    },
    onCommit: (value) => {
        // This runs immediately when user releases slider
        console.log('Final value:', value);
    }
});
```

### Example 7: Programmatic Control

**HTML:** `<div id="paramslider-parameter"></div>`

```javascript
const slider = new ParamSlider('paramslider-parameter', {
    inputId: 'parameter',
    label: 'Parameter',
    min: 0,
    max: 100,
    step: 1,
    value: 50,
    onChange: (value) => console.log('Changed:', value)
});

// Get value
const currentValue = slider.value;
// or
const currentValue = slider.getValue();

// Set value (triggers onChange)
slider.setValue(75);

// Set value silently (no onChange)
slider.setValue(75, { silent: true });

// Set value and trigger onCommit
slider.setValue(75, { commit: true });

// Alias for setValue (consistent with MetricLabel)
slider.update(80);

// Update configuration
slider.setConfig({ min: 0, max: 200, step: 2 });

// Enable/disable
slider.disable();
slider.enable();

// Cleanup
slider.destroy();
```

### Example 8: Multiple Sliders (Bulk Initialization)

**HTML:**
```html
<div id="paramslider-w1"></div>
<div id="paramslider-w2"></div>
<div id="paramslider-bias"></div>
<div id="paramslider-w11"></div>
<div id="paramslider-w22"></div>
<div id="paramslider-w12"></div>
```

```javascript
// For demos with many similar sliders (e.g., lec03c with 6 sliders)
class ClassificationDemo {
    constructor() {
        // Define slider configurations
        this.sliderConfigs = {
            w1: { label: '$w_1$ (X1 Weight)', min: -5, max: 5, value: 0.1 },
            w2: { label: '$w_2$ (X2 Weight)', min: -5, max: 5, value: 0.1 },
            bias: { label: '$b$ (Bias)', min: -10, max: 10, value: 0 },
            w11: { label: '$w_{11}$ (X₁²)', min: -2, max: 2, value: 0 },
            w22: { label: '$w_{22}$ (X₂²)', min: -2, max: 2, value: 0 },
            w12: { label: '$w_{12}$ (X₁X₂)', min: -2, max: 2, value: 0 }
        };

        // Create all sliders
        this.sliders = {};
        Object.keys(this.sliderConfigs).forEach(id => {
            const config = this.sliderConfigs[id];
            this.sliders[id] = new ParamSlider(`paramslider-${id}`, {
                inputId: id,
                ...config,
                step: 0.1,
                decimals: 1,
                onChange: () => this.updateDecisionBoundary()
            });
        });
    }

    updateDecisionBoundary() {
        const weights = {
            w1: this.sliders.w1.value,
            w2: this.sliders.w2.value,
            bias: this.sliders.bias.value,
            w11: this.sliders.w11.value,
            w22: this.sliders.w22.value,
            w12: this.sliders.w12.value
        };
        // ... update visualization
    }
}
```

### Example 9: With Accessibility

**HTML:** `<div id="paramslider-alpha"></div>`

```javascript
const slider = new ParamSlider('paramslider-alpha', {
    inputId: 'alpha',
    label: '$\\alpha$ (Intercept)',
    min: -100,
    max: 200,
    step: 0.1,
    value: 0,
    ariaLabel: 'Alpha parameter for linear regression',
    ariaValueText: (value) => `Alpha equals ${value.toFixed(1)} units`,
    onChange: (value) => this.updateModel()
});
```

---

## API Reference

### Constructor

```javascript
new ParamSlider(containerId, options)
```

**Parameters:**

- `containerId` (string): ID of the container div (following `paramslider-{name}` convention)
- `options` (object): Configuration options

**Options:**

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `inputId` | string | **required** | ID for the slider input element (e.g., `'alpha'` for `paramslider-alpha`) |
| `label` | string | - | Label text (supports MathJax `$...$`) |
| `min` | number | **required** | Minimum value |
| `max` | number | **required** | Maximum value |
| `step` | number | **required** | Step increment |
| `value` | number | **required** | Initial value |
| `decimals` | number | auto-detect | Decimal places for display |
| `fineTuneStep` | number | - | If provided, creates +/- buttons |
| `onChange` | function | - | Callback: `(value) => {}` |
| `onCommit` | function | - | Callback on drag release: `(value) => {}` |
| `formatValue` | function | - | Custom formatter: `(value) => string` |
| `prefix` | string | `''` | Prefix for value display |
| `suffix` | string | `''` | Suffix for value display |
| `debounceMs` | number | `0` | Debounce delay for onChange (ms) |
| `mathJax` | string | `'auto'` | MathJax mode: `'auto'` \| `'on'` \| `'off'` |
| `disabled` | boolean | `false` | Initial disabled state |
| `ariaLabel` | string | - | Accessibility label |
| `ariaValueText` | function | - | Accessibility value text: `(value) => string` |
| `containerId` | string | - | Container to inject into |
| `valueDisplayId` | string | `{id}-value` | ID for value display |
| `plusButtonId` | string | `{id}-plus` | ID for plus button |
| `minusButtonId` | string | `{id}-minus` | ID for minus button |

### Instance Methods

#### `getValue()`
Returns the current slider value as a number.

```javascript
const value = slider.getValue();
```

#### `setValue(value, options)`
Set the slider value programmatically.

**Parameters:**
- `value` (number): New value
- `options` (object):
  - `silent` (boolean): Don't trigger onChange callback (default: `false`)
  - `commit` (boolean): Trigger onCommit callback (default: `false`)

```javascript
slider.setValue(50);
slider.setValue(50, { silent: true });
slider.setValue(50, { commit: true });
```

#### `update(value, options)`
Alias for `setValue()` - consistent with MetricLabel API.

```javascript
slider.update(75);
```

#### `setConfig(config)`
Update slider configuration.

**Parameters:**
- `config` (object): Configuration to update (`min`, `max`, `step`, `decimals`)

```javascript
slider.setConfig({ min: -50, max: 150, step: 0.5 });
```

#### `enable()`
Enable the slider and buttons.

```javascript
slider.enable();
```

#### `disable()`
Disable the slider and buttons.

```javascript
slider.disable();
```

#### `destroy()`
Remove event listeners and cleanup.

```javascript
slider.destroy();
```

### Instance Properties

#### `value` (getter)
Get current value.

```javascript
const currentValue = slider.value;
```

#### `element`
The slider input element.

```javascript
const input = slider.element;
```

#### `container`
The control-group container element.

```javascript
const container = slider.container;
```

#### `labelElement`
The label element.

```javascript
const label = slider.labelElement;
```

#### `valueDisplayElement`
The value display element.

```javascript
const display = slider.valueDisplayElement;
```

### Static Methods

#### `ParamSlider.initAll(selector, buildOptions)`
Bulk initialize multiple sliders.

**Parameters:**
- `selector` (string): CSS selector for slider inputs
- `buildOptions` (function): Function to build options: `(element) => options`

**Returns:** Array of ParamSlider instances

```javascript
const sliders = ParamSlider.initAll('input[type="range"]', (element) => ({
    label: element.dataset.label,
    onChange: () => this.update()
}));
```

---

## Migration from Manual Sliders

### Before (Manual Implementation)
```html
<!-- HTML: 12 lines per slider -->
<div class="control-group">
    <label for="alpha">$\alpha$ (Intercept)</label>
    <div class="slider-container">
        <input type="range" id="alpha" min="-100" max="200" step="0.1" value="0">
        <div class="value-display" id="alpha-value">0.0</div>
    </div>
</div>
```

```javascript
// JavaScript: ~15-20 lines per slider
this.alphaSlider = document.getElementById('alpha');
this.alphaValue = document.getElementById('alpha-value');

this.alphaSlider.addEventListener('input', () => {
    const value = parseFloat(this.alphaSlider.value);
    this.alphaValue.textContent = value.toFixed(1);
    this.updateDisplay();
    this.updateChart();
});
```

### After (ParamSlider)
```html
<!-- HTML: 1 line per slider -->
<div id="paramslider-alpha"></div>
```

```javascript
// JavaScript: Configuration in one place
this.alphaSlider = new ParamSlider('paramslider-alpha', {
    inputId: 'alpha',
    label: '$\\alpha$ (Intercept)',
    min: -100,
    max: 200,
    step: 0.1,
    value: 0,
    onChange: (value) => {
        this.updateDisplay();
        this.updateChart();
    }
});
```

**Benefits:**
- **HTML reduced by 92%**: 12 lines → 1 line per slider
- **JavaScript centralized**: All configuration in one place
- **Auto-generated structure**: Component builds its own HTML
- **No manual DOM queries**: Component manages elements
- **No manual event listeners**: Built-in event handling
- **No manual value formatting**: Auto-detects decimals from step
- **Automatic MathJax rendering**: Supports `$...$` notation
- **Optional +/- buttons**: Add with single `fineTuneStep` parameter

---

## Best Practices

1. **Let decimals auto-detect** - Only override when you need specific formatting
2. **Use debounceMs for expensive operations** - Improves performance on complex updates
3. **Use onCommit for final actions** - Good for analytics or saving state
4. **Consistent with MetricLabel** - Both use `update()` method
5. **Enable accessibility** - Use `ariaLabel` and `ariaValueText` for screen readers
6. **Group related sliders** - Store in object for easy access

---

## Common Patterns

### Pattern 1: Reset Button
```javascript
const slider = new ParamSlider('param', { /* ... */ });

document.getElementById('reset-btn').addEventListener('click', () => {
    slider.setValue(0);  // Triggers onChange
});
```

### Pattern 2: Read from Multiple Sliders
```javascript
function computeResult() {
    const alpha = this.alphaSlider.value;
    const beta = this.betaSlider.value;
    return alpha * beta;
}
```

### Pattern 3: Conditional Updates
```javascript
const slider = new ParamSlider('threshold', {
    /* ... */,
    onChange: (value) => {
        if (value > 0.5) {
            this.showWarning();
        }
        this.updateVisualization();
    }
});
```

---

## Troubleshooting

### Slider doesn't appear
- Ensure the container div exists before creating ParamSlider
- Check that the container ID matches the `paramslider-{name}` convention
- Verify `inputId` parameter is provided in options

### MathJax not rendering
- Ensure MathJax is loaded before creating sliders
- Check label contains `$...$` notation
- Try setting `mathJax: 'on'` explicitly

### onChange not firing
- Check that the callback function is defined
- Verify the slider is not disabled
- For programmatic updates, ensure `silent: true` is not set

### Value display not updating
- Component should handle this automatically
- Check console for errors
- Verify `decimals` or `formatValue` options are correct

### Fine-tune buttons not appearing
- Ensure `fineTuneStep` option is provided
- Check that the value is a number (not undefined)

---

## Performance Considerations

- Use `debounceMs` for expensive onChange operations
- Use `onCommit` for operations that only need final value
- For bulk operations, group slider updates and use single visualization update
- The component is lightweight and has minimal overhead

---

## Compatibility

- Automatically generates HTML structure from minimal container divs
- Compatible with all existing CSS classes in phoebe.css
- MathJax is optional (works without it)
- Supports both linear and logarithmic scale sliders
