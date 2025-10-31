# SelectorBox - Usage Examples

The `selectorbox` component provides a standardized, reusable toggle box pattern for enabling/disabling options across demos. It supports automatic styling via data attributes, extensive color palette, and minimal HTML markup.

## Features

- **Data-Driven Content**: Uses `data-icon` and `data-label` attributes with CSS pseudo-elements
- **Color Palette**: 15 predefined color variants via `data-color` attribute
- **Minimal HTML**: Single line per selector box
- **Flexible Usage**: Features, boundaries, datasets, and other toggle options
- **Auto-Styling**: Content rendered via CSS ::before and ::after pseudo-elements

---

## Basic Usage

### HTML Structure

Simply create a div with `selectorbox` class and required data attributes:

```html
<div class="selector-grid">
    <div class="selectorbox active" data-color="blue" data-icon="📐" data-label="Area (sq ft)" data-feature="area"></div>
    <div class="selectorbox active" data-color="purple" data-icon="🛏️" data-label="Bedrooms" data-feature="bedrooms"></div>
</div>
```

**Key Data Attributes**:
- `data-color`: Color variant (required) - see color palette below
- `data-icon`: Emoji or symbol to display (required)
- `data-label`: Text label below icon (required)
- `data-*`: Custom attributes for your logic (e.g., `data-feature`, `data-boundary`, etc.)

---

## Color Palette

15 color variants available via `data-color` attribute:

| Color | Hex | Typical Use Case |
|-------|-----|------------------|
| `red` | #dc2626 | Error/residual visualizations |
| `blue` | #5a9fd4 | Primary features, train data |
| `purple` | #a87ab8 | Secondary features |
| `orange` | #f59e0b | Test data, warnings |
| `green` | #059669 | Success, positive features |
| `teal` | #14b8a6 | Aqua/turquoise shades |
| `indigo` | #6366f1 | Deep blue shades |
| `pink` | #ec4899 | Warm pink shades |
| `cyan` | #06b6d4 | Bright sky blue |
| `yellow` | #eab308 | Bright warm yellow |
| `amber` | #f59e0b | Orange-yellow blend |
| `lime` | #84cc16 | Bright green-yellow |
| `emerald` | #10b981 | Rich green |
| `violet` | #8b5cf6 | Purple-blue blend |
| `rose` | #f43f5e | Pink-red blend |

---

## Usage Examples

### Example 1: Feature Selection (Multi-Linear Regression)

**HTML**:
```html
<div class="control-group">
    <label>Select Features:</label>
    <div class="selector-grid">
        <div class="selectorbox active" data-color="blue" data-feature="area" data-icon="📐" data-label="Area (sq ft)"></div>
        <div class="selectorbox active" data-color="purple" data-feature="bedrooms" data-icon="🛏️" data-label="Bedrooms"></div>
        <div class="selectorbox active" data-color="teal" data-feature="bathrooms" data-icon="🚿" data-label="Bathrooms"></div>
        <div class="selectorbox active" data-color="cyan" data-feature="stories" data-icon="🏢" data-label="Stories"></div>
    </div>
</div>
```

**JavaScript**:
```javascript
const featureBoxes = document.querySelectorAll('.selectorbox');
featureBoxes.forEach(box => {
    box.addEventListener('click', () => {
        box.classList.toggle('active');
        const feature = box.getAttribute('data-feature');
        this.updateModel();
    });
});
```

### Example 2: Display Options (Bias-Variance)

**HTML**:
```html
<div class="control-group">
    <label><strong>Display Options:</strong></label>
    <div class="selector-grid">
        <div class="selectorbox" data-color="red" id="toggle-squared-errors" data-icon="📐" data-label="Squared Errors"></div>
        <div class="selectorbox" data-color="orange" id="toggle-test-data" data-icon="🔬" data-label="Test Data"></div>
    </div>
</div>
```

**JavaScript**:
```javascript
document.getElementById('toggle-squared-errors').addEventListener('click', (e) => {
    e.currentTarget.classList.toggle('active');
    this.showSquaredErrors = e.currentTarget.classList.contains('active');
    this.updateDisplay();
});
```

### Example 3: Dataset Toggle (Feature Engineering)

**HTML**:
```html
<div class="dataset-toggle">
    <div class="selectorbox dataset-box active" data-color="blue" id="toggle-train" data-icon="📚" data-label="Train"></div>
    <div class="selectorbox dataset-box" data-color="orange" id="toggle-test" data-icon="🔬" data-label="Test"></div>
</div>
```

**JavaScript**:
```javascript
const trainBox = document.getElementById('toggle-train');
const testBox = document.getElementById('toggle-test');

trainBox.addEventListener('click', () => {
    trainBox.classList.add('active');
    testBox.classList.remove('active');
    this.updateMetrics('train');
});

testBox.addEventListener('click', () => {
    testBox.classList.add('active');
    trainBox.classList.remove('active');
    this.updateMetrics('test');
});
```

### Example 4: Boundary Type Selection (2D Classification)

**HTML**:
```html
<div class="control-group">
    <label>Decision Boundary Type</label>
    <div class="selector-grid">
        <div class="selectorbox active" data-color="blue" data-boundary="linear" data-icon="📏" data-label="Linear"></div>
        <div class="selectorbox" data-color="orange" data-boundary="quadratic" data-icon="〰️" data-label="Quadratic"></div>
    </div>
</div>
```

**JavaScript**:
```javascript
const boundaryBoxes = document.querySelectorAll('.selectorbox[data-boundary]');
boundaryBoxes.forEach(box => {
    box.addEventListener('click', () => {
        const boundaryType = box.getAttribute('data-boundary');
        this.selectedBoundaryType = boundaryType;

        // Update active state
        boundaryBoxes.forEach(b => b.classList.remove('active'));
        box.classList.add('active');

        this.updateDisplay();
    });
});
```

### Example 5: Programmatic Control

**JavaScript**:
```javascript
// Enable/disable programmatically
const box = document.querySelector('.selectorbox[data-feature="area"]');
box.classList.add('active');      // Enable
box.classList.remove('active');   // Disable
box.classList.toggle('active');   // Toggle

// Disable interaction
box.style.pointerEvents = 'none';
box.style.opacity = '0.3';

// Re-enable interaction
box.style.pointerEvents = 'auto';
box.style.opacity = '';

// Get current state
const isActive = box.classList.contains('active');

// Get data attributes
const feature = box.getAttribute('data-feature');
const color = box.getAttribute('data-color');
```

---

## CSS Architecture

### How It Works

The component uses CSS pseudo-elements to render content from data attributes:

```css
/* Icon rendered via ::before */
.selectorbox[data-icon]::before {
    content: attr(data-icon);
    font-size: 28px;
}

/* Label rendered via ::after */
.selectorbox[data-label]::after {
    content: attr(data-label);
    font-size: 12px;
}
```

**Benefits**:
- Minimal HTML (single div per selector)
- Content defined in markup, styled in CSS
- No JavaScript needed for rendering
- Easy to update content dynamically

### States

**Inactive (default)**:
- `opacity: 0.4`
- `filter: grayscale(50%)`

**Active**:
- `opacity: 1`
- `filter: grayscale(0%)`
- Border color matches data-color

**Hover**:
- `transform: translateY(-2px)`
- `opacity: 0.6`

---

## Grid Layout

The `.selector-grid` class creates a 2-column grid:

```css
.selector-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 12px;
}
```

For more columns, override in demo-specific CSS:

```css
.selector-grid-3 {
    grid-template-columns: repeat(3, 1fr);
}

.selector-grid-4 {
    grid-template-columns: repeat(4, 1fr);
}
```

---

## Best Practices

1. **Use Semantic Data Attributes**: Add custom `data-*` attributes for your logic (e.g., `data-feature`, `data-boundary`, `data-class`)

2. **Choose Appropriate Colors**: Select colors that match the semantic meaning:
   - `blue` for primary/train data
   - `orange` for test/warning data
   - `red` for errors/residuals
   - `green` for success/positive

3. **Consistent Icons**: Use emojis that clearly represent the option

4. **Clear Labels**: Keep labels concise (1-3 words)

5. **Active State Management**: Ensure only appropriate boxes are active based on your logic

---

## Common Patterns

### Pattern 1: Single Selection (Radio Behavior)

```javascript
const boxes = document.querySelectorAll('.selectorbox[data-boundary]');
boxes.forEach(box => {
    box.addEventListener('click', () => {
        // Remove active from all
        boxes.forEach(b => b.classList.remove('active'));
        // Add active to clicked
        box.classList.add('active');
    });
});
```

### Pattern 2: Multiple Selection (Checkbox Behavior)

```javascript
const boxes = document.querySelectorAll('.selectorbox[data-feature]');
boxes.forEach(box => {
    box.addEventListener('click', () => {
        // Toggle individual box
        box.classList.toggle('active');
        this.updateModel();
    });
});
```

### Pattern 3: Conditional Enabling

```javascript
function disableOption(selector) {
    const box = document.querySelector(selector);
    if (box) {
        box.style.pointerEvents = 'none';
        box.style.opacity = '0.3';
    }
}

function enableOption(selector) {
    const box = document.querySelector(selector);
    if (box) {
        box.style.pointerEvents = 'auto';
        box.style.opacity = '';
    }
}

// Usage
disableOption('.selectorbox[data-boundary="quadratic"]');
enableOption('.selectorbox[data-boundary="quadratic"]');
```

---

## Migration from Feature-Box

The `selectorbox` component replaces the legacy `feature-box` pattern:

### Before (Feature-Box with Nested Divs)

```html
<div class="feature-grid">
    <div class="feature-box feature-area active" data-feature="area">
        <div class="feature-icon">📐</div>
        <div class="feature-label">Area (sq ft)</div>
    </div>
</div>
```

### After (SelectorBox with Data Attributes)

```html
<div class="selector-grid">
    <div class="selectorbox active" data-color="blue" data-feature="area" data-icon="📐" data-label="Area (sq ft)"></div>
</div>
```

**Benefits**:
- **67% HTML reduction**: 5 lines → 1 line per selector
- **Cleaner markup**: No nested divs
- **Flexible colors**: 15-color palette vs fixed class names
- **Easier maintenance**: Content in attributes, not HTML structure

---

## Troubleshooting

### Selector box doesn't appear
- Ensure `data-icon` and `data-label` attributes are provided
- Check that `data-color` is one of the 15 valid colors
- Verify phoebe.css is loaded

### Icon or label not showing
- Confirm attributes are spelled correctly: `data-icon`, `data-label`
- Check for typos in attribute values
- Inspect element to verify CSS pseudo-elements are applied

### Click not working
- Ensure JavaScript event listener is attached
- Check that `pointer-events` is not set to `none`
- Verify element is not disabled programmatically

### Colors not applying
- Check `data-color` value matches one of the 15 palette colors
- Ensure `.active` class is present for full color visibility
- Verify no demo-specific CSS is overriding colors

---

## Component Files

- **CSS**: `demos/source/phoebe-js/phoebe.css` (lines 807-1088)
- **Examples**: `demos/source/phoebe-js/selectorbox-examples.md` (this file)
- **Demos Using**: lec02c, lec03a, lec03b, lec03g

---

## Related Components

- **MetricLabel**: Displays metric values with theme colors
- **ParamSlider**: Interactive parameter sliders with auto-HTML generation
- **InfoTab**: Tabbed information panels

---

*Component standardized October 2025. Replaces legacy `feature-box` pattern.*
