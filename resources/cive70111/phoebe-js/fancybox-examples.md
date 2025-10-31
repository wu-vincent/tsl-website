# FancyBox Examples

Complete examples showing how to use the FancyBox component for interactive selection boxes.

## Basic Usage

### Example 1: SVG Graphics with Labels

Classifier selection with SVG icons:

```html
<div class="fancybox-panel">
    <h4>Classifier Type</h4>
    <div class="fancybox-container">
        <div class="fancybox active" data-type="linear" data-color="blue" data-label="Linear">
            <svg class="fancybox-graphic" viewBox="0 0 60 60" width="45" height="45">
                <line x1="10" y1="50" x2="50" y2="10" stroke="currentColor" stroke-width="3" stroke-linecap="round"/>
            </svg>
        </div>
        <div class="fancybox" data-type="step" data-color="orange" data-label="Step">
            <svg class="fancybox-graphic" viewBox="0 0 60 60" width="45" height="45">
                <path d="M 10 50 L 30 50 L 30 10 L 50 10" fill="none" stroke="currentColor" stroke-width="3"/>
            </svg>
        </div>
        <div class="fancybox" data-type="sigmoid" data-color="purple" data-label="Sigmoid">
            <svg class="fancybox-graphic" viewBox="0 0 60 60" width="45" height="45">
                <path d="M 10 50 Q 20 50 30 30 Q 40 10 50 10" fill="none" stroke="currentColor" stroke-width="3"/>
            </svg>
        </div>
    </div>
</div>
```

### Example 2: Emoji Icons Only

Simple icon-based selection:

```html
<div class="fancybox-panel">
    <h4>Select Category</h4>
    <div class="fancybox-container">
        <div class="fancybox active" data-type="structures" data-color="red"
             data-icon="🏗️" data-label="Structures"></div>
        <div class="fancybox" data-type="geotechnical" data-color="purple"
             data-icon="🏔️" data-label="Geotechnical"></div>
        <div class="fancybox" data-type="transport" data-color="blue"
             data-icon="🚊" data-label="Transport"></div>
        <div class="fancybox" data-type="environment" data-color="green"
             data-icon="🌱" data-label="Environment"></div>
    </div>
</div>
```

### Example 3: With Info Text

Dataset selection with additional info:

```html
<div class="fancybox-panel">
    <h4>Dataset Scenario</h4>
    <div class="fancybox-container">
        <div class="fancybox active" data-type="balanced" data-color="blue"
             data-icon="✨" data-label="Balanced">
            <span class="fancybox-info">50/50 split</span>
        </div>
        <div class="fancybox" data-type="imbalanced" data-color="orange"
             data-icon="⚖️" data-label="Imbalanced">
            <span class="fancybox-info">99/1 split</span>
        </div>
    </div>
</div>
```

## JavaScript Integration

### Basic Event Handling

```javascript
// Get all fancybox elements
const boxes = document.querySelectorAll('.fancybox');

// Add click handler
boxes.forEach(box => {
    box.addEventListener('click', () => {
        // Remove active from all
        boxes.forEach(b => b.classList.remove('active'));

        // Add active to clicked
        box.classList.add('active');

        // Get the selected type
        const selectedType = box.dataset.type;
        console.log('Selected:', selectedType);

        // Handle the selection
        handleSelection(selectedType);
    });
});

function handleSelection(type) {
    // Your custom logic here
    console.log(`User selected: ${type}`);
}
```

### Integration with Demo Class

```javascript
class MyDemo {
    constructor() {
        this.selectedClassifier = 'linear';
        this.classifierBoxes = document.querySelectorAll('.fancybox');
        this.setupEventListeners();
    }

    setupEventListeners() {
        this.classifierBoxes.forEach(box => {
            box.addEventListener('click', () => {
                // Update active state
                this.classifierBoxes.forEach(b => b.classList.remove('active'));
                box.classList.add('active');

                // Store selection
                this.selectedClassifier = box.dataset.type;

                // Update visualization
                this.updateChart();
            });
        });
    }

    updateChart() {
        // Use this.selectedClassifier to update your chart
        console.log(`Using classifier: ${this.selectedClassifier}`);
    }
}
```

## Color Themes

### All Available Colors

```html
<div class="fancybox-container">
    <!-- Blue: Primary options, training data -->
    <div class="fancybox active" data-type="blue" data-color="blue"
         data-icon="🔵" data-label="Blue"></div>

    <!-- Orange: Secondary options, test data -->
    <div class="fancybox" data-type="orange" data-color="orange"
         data-icon="🟠" data-label="Orange"></div>

    <!-- Purple: Tertiary options -->
    <div class="fancybox" data-type="purple" data-color="purple"
         data-icon="🟣" data-label="Purple"></div>

    <!-- Red: Class 0, negative cases -->
    <div class="fancybox" data-type="red" data-color="red"
         data-icon="🔴" data-label="Red"></div>

    <!-- Teal: Class 1, positive cases -->
    <div class="fancybox" data-type="teal" data-color="teal"
         data-icon="🔷" data-label="Teal"></div>

    <!-- Green: Success states -->
    <div class="fancybox" data-type="green" data-color="green"
         data-icon="🟢" data-label="Green"></div>
</div>
```

## Advanced Patterns

### Conditional Rendering

```javascript
// Dynamically create fancybox elements
function createClassifierBoxes(classifiers) {
    const container = document.querySelector('.fancybox-container');

    classifiers.forEach((classifier, index) => {
        const box = document.createElement('div');
        box.className = 'fancybox' + (index === 0 ? ' active' : '');
        box.dataset.type = classifier.type;
        box.dataset.color = classifier.color;
        box.dataset.label = classifier.label;

        if (classifier.icon) {
            box.dataset.icon = classifier.icon;
        } else if (classifier.svg) {
            box.innerHTML = classifier.svg;
        }

        container.appendChild(box);
    });
}

// Usage
const classifiers = [
    { type: 'linear', color: 'blue', label: 'Linear', icon: '📈' },
    { type: 'step', color: 'orange', label: 'Step', icon: '📊' },
    { type: 'sigmoid', color: 'purple', label: 'Sigmoid', icon: '〰️' }
];

createClassifierBoxes(classifiers);
```

### Two-Row Layout

For many options, use two rows:

```html
<div class="fancybox-panel">
    <h4>Select Dataset</h4>
    <div class="fancybox-container" style="flex-wrap: wrap; max-width: 400px;">
        <div class="fancybox active" data-type="d1" data-color="blue"
             data-icon="📊" data-label="Dataset 1" style="width: 90px;"></div>
        <div class="fancybox" data-type="d2" data-color="orange"
             data-icon="📈" data-label="Dataset 2" style="width: 90px;"></div>
        <div class="fancybox" data-type="d3" data-color="purple"
             data-icon="📉" data-label="Dataset 3" style="width: 90px;"></div>
        <div class="fancybox" data-type="d4" data-color="red"
             data-icon="📌" data-label="Dataset 4" style="width: 90px;"></div>
        <div class="fancybox" data-type="d5" data-color="teal"
             data-icon="📍" data-label="Dataset 5" style="width: 90px;"></div>
        <div class="fancybox" data-type="d6" data-color="green"
             data-icon="📎" data-label="Dataset 6" style="width: 90px;"></div>
    </div>
</div>
```

### Custom Spacing

```html
<!-- Spaced evenly across full width -->
<div class="fancybox-container" style="justify-content: space-between;">
    <div class="fancybox active" data-type="a" data-color="blue" data-label="Option A"></div>
    <div class="fancybox" data-type="b" data-color="orange" data-label="Option B"></div>
    <div class="fancybox" data-type="c" data-color="purple" data-label="Option C"></div>
</div>

<!-- Centered with gaps -->
<div class="fancybox-container" style="justify-content: center; gap: 30px;">
    <div class="fancybox active" data-type="x" data-color="red" data-label="X"></div>
    <div class="fancybox" data-type="y" data-color="teal" data-label="Y"></div>
</div>
```

## Common SVG Icons

### Geometric Shapes

```html
<!-- Circle -->
<svg class="fancybox-graphic" viewBox="0 0 60 60" width="45" height="45">
    <circle cx="30" cy="30" r="18" fill="none" stroke="currentColor" stroke-width="3"/>
</svg>

<!-- Square -->
<svg class="fancybox-graphic" viewBox="0 0 60 60" width="45" height="45">
    <rect x="12" y="12" width="36" height="36" rx="4" fill="none" stroke="currentColor" stroke-width="3"/>
</svg>

<!-- Triangle -->
<svg class="fancybox-graphic" viewBox="0 0 60 60" width="45" height="45">
    <path d="M 30 10 L 50 50 L 10 50 Z" fill="none" stroke="currentColor" stroke-width="3"/>
</svg>

<!-- Star -->
<svg class="fancybox-graphic" viewBox="0 0 60 60" width="45" height="45">
    <path d="M 30 10 L 35 25 L 50 25 L 38 35 L 43 50 L 30 40 L 17 50 L 22 35 L 10 25 L 25 25 Z"
          fill="none" stroke="currentColor" stroke-width="2"/>
</svg>
```

### Function Graphs

```html
<!-- Linear -->
<svg class="fancybox-graphic" viewBox="0 0 60 60" width="45" height="45">
    <line x1="10" y1="50" x2="50" y2="10" stroke="currentColor" stroke-width="3" stroke-linecap="round"/>
</svg>

<!-- Step Function -->
<svg class="fancybox-graphic" viewBox="0 0 60 60" width="45" height="45">
    <path d="M 10 50 L 30 50 L 30 10 L 50 10" fill="none" stroke="currentColor"
          stroke-width="3" stroke-linecap="round" stroke-linejoin="round"/>
</svg>

<!-- Sigmoid / S-curve -->
<svg class="fancybox-graphic" viewBox="0 0 60 60" width="45" height="45">
    <path d="M 10 50 Q 20 50 30 30 Q 40 10 50 10" fill="none" stroke="currentColor"
          stroke-width="3" stroke-linecap="round"/>
</svg>

<!-- Parabola -->
<svg class="fancybox-graphic" viewBox="0 0 60 60" width="45" height="45">
    <path d="M 10 50 Q 30 10 50 50" fill="none" stroke="currentColor"
          stroke-width="3" stroke-linecap="round"/>
</svg>
```

## Accessibility

### Keyboard Navigation

Add keyboard support:

```javascript
boxes.forEach((box, index) => {
    box.setAttribute('tabindex', '0');
    box.setAttribute('role', 'button');
    box.setAttribute('aria-label', box.dataset.label);

    // Keyboard support
    box.addEventListener('keydown', (e) => {
        if (e.key === 'Enter' || e.key === ' ') {
            e.preventDefault();
            box.click();
        }
    });
});
```

### ARIA Labels

```html
<div class="fancybox active"
     data-type="linear"
     data-color="blue"
     data-label="Linear"
     role="button"
     tabindex="0"
     aria-label="Select linear classifier"
     aria-pressed="true">
    <svg class="fancybox-graphic">...</svg>
</div>
```

## Responsive Design

### Mobile-Friendly Layout

```css
/* In your demo's style.css */
@media (max-width: 768px) {
    .fancybox-container {
        flex-wrap: wrap;
        justify-content: center;
    }

    .fancybox {
        width: 80px;
        padding: 8px;
    }

    .fancybox-graphic {
        width: 35px;
        height: 35px;
    }
}
```

## Tips and Best Practices

1. **Always use data-color** - This ensures consistent theming
2. **Use data-label for all boxes** - Keeps HTML cleaner than child spans
3. **Use `<span class="fancybox-info">` for info text** - Better styling control than data-info
4. **SVG viewBox should be 0 0 60 60** - Standard size for consistency
5. **Keep icons recognizable** - Test that graphics are clear at 45x45px
6. **Limit to 2-4 options per row** - More than that becomes cluttered
7. **Use semantic colors** - Blue for primary, Red for negative, Teal for positive
8. **Test touch targets** - Ensure boxes are at least 44x44px for mobile

## Demo Reference

See these demos for live examples:
- `lec03f_classification` - Classifier selection (SVG graphics)
- `lec03g_2d-classification` - Class selection (colored shapes)
- `lec03h_metrics` - Dataset selection (emoji icons with info)
