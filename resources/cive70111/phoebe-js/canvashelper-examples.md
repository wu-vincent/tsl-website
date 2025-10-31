# CanvasHelper Examples

Complete examples showing how to use the CanvasHelper class for high-DPI canvas operations, Chart.js overlays, and coordinate transformations.

## High-DPI Canvas Setup

### Example 1: Basic High-DPI Canvas

```javascript
// HTML
<canvas id="myCanvas" width="600" height="400"></canvas>

// JavaScript
const canvas = document.getElementById('myCanvas');

// Setup for high-DPI displays (retina screens)
const { ctx, dpr, displayWidth, displayHeight } = CanvasHelper.setupHighDPI(
    canvas,
    canvas.getContext('2d')
);

console.log(`Display: ${displayWidth}x${displayHeight}, DPR: ${dpr}`);
// On retina: Display: 600x400, DPR: 2

// Now draw normally - scaling is handled automatically
ctx.fillStyle = 'blue';
ctx.fillRect(10, 10, 100, 100);  // Crisp on all displays
```

### Example 2: High-DPI Canvas Without Context

```javascript
// Setup canvas dimensions without supplying a context
const { ctx, dpr, displayWidth, displayHeight } = CanvasHelper.setupHighDPI(canvas);

// Context is returned ready to use
ctx.scale(dpr, dpr);  // Manual scaling if additional transforms are required
```

---

## Chart.js Overlay Setup

### Example 1: Basic Overlay for Error Visualization

```html
<!-- HTML Structure -->
<div style="position: relative; width: 600px; height: 400px;">
    <canvas id="chart"></canvas>
    <canvas id="overlay" style="position: absolute; top: 0; left: 0; pointer-events: none;"></canvas>
</div>
```

```javascript
// Create Chart.js chart first
const chartCanvas = document.getElementById('chart');
const chart = new Chart(chartCanvas.getContext('2d'), {
    type: 'scatter',
    data: { datasets: [...] },
    options: { responsive: true, maintainAspectRatio: false }
});

// Setup overlay canvas aligned with chart
const overlayCanvas = document.getElementById('overlay');
const { ctx, dpr, displayWidth, displayHeight } = CanvasHelper.setupOverlay(overlayCanvas, chartCanvas);

// Draw on overlay (e.g., error squares)
ctx.fillStyle = 'rgba(255, 0, 0, 0.2)';
ctx.fillRect(100, 100, 50, 50);
```

### Example 2: Overlay with Dynamic Updates

```javascript
class RegressionDemo {
    constructor() {
        this.chartCanvas = document.getElementById('chart');
        this.overlayCanvas = document.getElementById('overlay');

        // Create chart
        this.chart = new Chart(this.chartCanvas.getContext('2d'), {...});

        // Setup overlay after chart is ready
        setTimeout(() => {
            const { ctx, dpr, displayWidth, displayHeight } =
                CanvasHelper.setupOverlay(this.overlayCanvas, this.chartCanvas);
            this.overlayCtx = ctx;
            this.dpr = dpr;
            this.overlayWidth = displayWidth;
            this.overlayHeight = displayHeight;
        }, 100);
    }

    updateOverlay() {
        // Clear overlay
        CanvasHelper.clear(this.overlayCanvas, this.overlayCtx);

        // Draw custom visualizations
        this.drawErrorSquares();
    }
}
```

---

## Coordinate Transformations

### Example 1: Data to Pixel Coordinates

```javascript
// Convert data space to pixel space for custom drawing
const dataPoints = [
    { x: 10, y: 20 },
    { x: 30, y: 50 },
    { x: 50, y: 80 }
];

// Draw custom markers on overlay
dataPoints.forEach(point => {
    const { x, y } = CanvasHelper.dataToPixel(chart, point.x, point.y);

    overlayCtx.fillStyle = 'red';
    overlayCtx.beginPath();
    overlayCtx.arc(x, y, 5, 0, 2 * Math.PI);
    overlayCtx.fill();
});
```

### Example 2: Pixel to Data Coordinates

```javascript
// Handle canvas clicks and convert to data coordinates
canvas.addEventListener('click', (event) => {
    const rect = canvas.getBoundingClientRect();
    const pixelX = event.clientX - rect.left;
    const pixelY = event.clientY - rect.top;

    // Convert to data coordinates
    const { x, y } = CanvasHelper.pixelToData(chart, pixelX, pixelY);

    console.log(`Clicked at data point: (${x.toFixed(2)}, ${y.toFixed(2)})`);

    // Add to dataset
    dataPoints.push({ x, y });
    updateChart();
});
```

### Example 3: Drawing Lines Between Data Points

```javascript
// Draw a line connecting data points
function drawConnectionLine(ctx, chart, point1, point2) {
    const p1 = CanvasHelper.dataToPixel(chart, point1.x, point1.y);
    const p2 = CanvasHelper.dataToPixel(chart, point2.x, point2.y);

    ctx.strokeStyle = 'blue';
    ctx.lineWidth = 2;
    ctx.beginPath();
    ctx.moveTo(p1.x, p1.y);
    ctx.lineTo(p2.x, p2.y);
    ctx.stroke();
}

// Usage
drawConnectionLine(overlayCtx, chart, { x: 10, y: 20 }, { x: 50, y: 80 });
```

---

## Error Square Visualization

### Example 1: Basic Error Squares

```javascript
// Visualize squared errors for regression
const dataPoints = [
    { x: 10, y: 25 },
    { x: 20, y: 45 },
    { x: 30, y: 62 }
];

// Prediction function (e.g., linear regression)
const alpha = 5;
const beta = 2;
const predictFn = (x) => alpha + beta * x;

// Draw error squares
CanvasHelper.drawErrorSquares(overlayCtx, chart, dataPoints, predictFn);
```

### Example 2: Custom Styled Error Squares

```javascript
// Draw with custom colors and style
CanvasHelper.drawErrorSquares(overlayCtx, chart, dataPoints, predictFn, {
    fillColor: 'rgba(52, 152, 219, 0.15)',     // Light blue fill
    strokeColor: 'rgba(41, 128, 185, 0.5)',    // Blue border
    lineWidth: 2
});
```

### Example 3: Interactive Error Visualization

```javascript
class LinearRegressionDemo {
    constructor() {
        // ... setup chart and overlay

        this.showSquares = false;

        document.getElementById('show-squares-checkbox').addEventListener('change', (e) => {
            this.showSquares = e.target.checked;
            this.updateOverlay();
        });
    }

    updateOverlay() {
        // Clear overlay
        CanvasHelper.clear(this.overlayCanvas, this.overlayCtx);

        if (this.showSquares && this.dataPoints.length > 0) {
            // Get current parameters
            const alpha = parseFloat(this.alphaSlider.value);
            const beta = parseFloat(this.betaSlider.value);

            // Prediction function
            const predict = (x) => alpha + beta * x;

            // Draw error squares
            CanvasHelper.drawErrorSquares(
                this.overlayCtx,
                this.chart,
                this.dataPoints,
                predict,
                {
                    fillColor: 'rgba(231, 76, 60, 0.15)',
                    strokeColor: 'rgba(231, 76, 60, 0.4)',
                    lineWidth: 1
                }
            );
        }

        // Make overlay visible
        this.overlayCanvas.style.visibility = this.showSquares ? 'visible' : 'hidden';
    }
}
```

---

## Clearing Canvas

### Example 1: Clear Overlay Between Draws

```javascript
function updateVisualization() {
    // Clear previous drawings
    CanvasHelper.clear(overlayCanvas, overlayCtx);

    // Draw new content
    drawErrorSquares();
    drawConfidenceIntervals();
}

// Update on parameter change
alphaSlider.addEventListener('input', updateVisualization);
betaSlider.addEventListener('input', updateVisualization);
```

### Example 2: Conditional Clearing

```javascript
function toggleOverlay(show) {
    if (show) {
        CanvasHelper.clear(overlayCanvas, overlayCtx);
        drawCustomVisualizations();
        overlayCanvas.style.visibility = 'visible';
    } else {
        CanvasHelper.clear(overlayCanvas, overlayCtx);
        overlayCanvas.style.visibility = 'hidden';
    }
}
```

---

## Complete Demo Integration

### Example: Linear Regression with Overlay

```javascript
class LinearRegressionDemo {
    constructor() {
        this.chartCanvas = document.getElementById('chart');
        this.overlayCanvas = document.getElementById('overlay');

        this.dataPoints = [];
        this.alpha = 0;
        this.beta = 1;

        this.initializeChart();
        this.setupOverlay();
        this.setupEventListeners();
    }

    initializeChart() {
        this.chart = new Chart(this.chartCanvas.getContext('2d'), {
            type: 'scatter',
            data: {
                datasets: [{
                    label: 'Data Points',
                    data: [],
                    backgroundColor: '#ff6b6b',
                    borderColor: '#d63447',
                    borderWidth: 2,
                    pointRadius: 5
                }, {
                    label: 'Regression Line',
                    data: [],
                    borderColor: '#4ecdc4',
                    borderWidth: 2,
                    showLine: true,
                    fill: false
                }]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                scales: {
                    x: { type: 'linear', min: 0, max: 100 },
                    y: { type: 'linear', min: 0, max: 100 }
                },
                onClick: (event) => this.handleClick(event)
            }
        });
    }

    setupOverlay() {
        setTimeout(() => {
            const { ctx, dpr, displayWidth, displayHeight } =
                CanvasHelper.setupOverlay(this.overlayCanvas, this.chartCanvas);

            this.overlayCtx = ctx;
            this.dpr = dpr;
            this.overlayWidth = displayWidth;
            this.overlayHeight = displayHeight;

            this.overlayCanvas.style.visibility = 'hidden';
        }, 100);
    }

    setupEventListeners() {
        document.getElementById('alpha-slider').addEventListener('input', (e) => {
            this.alpha = parseFloat(e.target.value);
            this.updateChart();
            this.updateOverlay();
        });

        document.getElementById('beta-slider').addEventListener('input', (e) => {
            this.beta = parseFloat(e.target.value);
            this.updateChart();
            this.updateOverlay();
        });

        document.getElementById('show-squares').addEventListener('change', (e) => {
            this.showSquares = e.target.checked;
            this.updateOverlay();
        });
    }

    handleClick(event) {
        const rect = this.chartCanvas.getBoundingClientRect();
        const pixelX = event.native.clientX - rect.left;
        const pixelY = event.native.clientY - rect.top;

        // Convert to data coordinates
        const { x, y } = CanvasHelper.pixelToData(this.chart, pixelX, pixelY);

        // Add data point
        this.dataPoints.push({ x, y });
        this.updateChart();
        this.updateOverlay();
    }

    updateChart() {
        // Update data points
        this.chart.data.datasets[0].data = this.dataPoints;

        // Update regression line
        const linePoints = [
            { x: 0, y: this.alpha },
            { x: 100, y: this.alpha + this.beta * 100 }
        ];
        this.chart.data.datasets[1].data = linePoints;

        this.chart.update('none');  // Update without animation
    }

    updateOverlay() {
        CanvasHelper.clear(this.overlayCanvas, this.overlayCtx);

        if (this.showSquares && this.dataPoints.length > 0) {
            const predict = (x) => this.alpha + this.beta * x;

            CanvasHelper.drawErrorSquares(
                this.overlayCtx,
                this.chart,
                this.dataPoints,
                predict,
                {
                    fillColor: 'rgba(231, 76, 60, 0.15)',
                    strokeColor: 'rgba(231, 76, 60, 0.4)',
                    lineWidth: 1
                }
            );

            this.overlayCanvas.style.visibility = 'visible';
        } else {
            this.overlayCanvas.style.visibility = 'hidden';
        }
    }
}

// Initialize
const demo = new LinearRegressionDemo();
```

---

## Advanced Patterns

### Example 1: Responsive Overlay Updates

```javascript
// Handle window resize
window.addEventListener('resize', () => {
    // Reconfigure overlay to match chart
    const { ctx, dpr, displayWidth, displayHeight } =
        CanvasHelper.setupOverlay(overlayCanvas, chartCanvas);

    overlayCtx = ctx;

    // Redraw overlay content
    updateOverlay();
});
```

### Example 2: Multiple Overlay Layers

```javascript
// Setup multiple overlays for different visualizations
const errorOverlay = document.getElementById('error-overlay');
const confidenceOverlay = document.getElementById('confidence-overlay');

const { ctx: errorCtx } = CanvasHelper.setupOverlay(errorOverlay, chartCanvas);
const { ctx: confidenceCtx } = CanvasHelper.setupOverlay(confidenceOverlay, chartCanvas);

// Draw different things on each layer
CanvasHelper.drawErrorSquares(errorCtx, chart, dataPoints, predictFn);
drawConfidenceIntervals(confidenceCtx, chart, dataPoints);
```

### Example 3: Custom Drawing with Data Coordinates

```javascript
// Draw a custom shape using data coordinates
function drawCustomPolygon(ctx, chart, vertices) {
    // Convert all vertices to pixel coordinates
    const pixelVertices = vertices.map(v =>
        CanvasHelper.dataToPixel(chart, v.x, v.y)
    );

    // Draw polygon
    ctx.beginPath();
    ctx.moveTo(pixelVertices[0].x, pixelVertices[0].y);

    for (let i = 1; i < pixelVertices.length; i++) {
        ctx.lineTo(pixelVertices[i].x, pixelVertices[i].y);
    }

    ctx.closePath();
    ctx.fillStyle = 'rgba(100, 149, 237, 0.2)';
    ctx.fill();
    ctx.strokeStyle = 'rgba(100, 149, 237, 0.6)';
    ctx.stroke();
}

// Usage
const polygon = [
    { x: 20, y: 30 },
    { x: 40, y: 50 },
    { x: 50, y: 40 },
    { x: 30, y: 20 }
];
drawCustomPolygon(overlayCtx, chart, polygon);
```

---

## Performance Tips

1. **Setup overlay once** - Call `setupOverlay()` once after chart initialization, not on every draw

2. **Clear efficiently** - Use `CanvasHelper.clear()` instead of recreating context

3. **Batch transformations** - Convert all coordinates before drawing rather than converting per draw call

4. **Update without animation** - Use `chart.update('none')` to prevent animation lag

5. **Conditional visibility** - Toggle canvas visibility instead of redrawing empty canvas

```javascript
// Good: Setup once
const { ctx } = CanvasHelper.setupOverlay(overlayCanvas, chartCanvas);

// Bad: Setup on every draw
function draw() {
    const { ctx } = CanvasHelper.setupOverlay(overlayCanvas, chartCanvas);  // Inefficient
    // ... drawing code
}
```

---

## Common Pitfalls

1. **Forgetting to wait for chart initialization**
```javascript
// Wrong - chart may not be ready
const { ctx } = CanvasHelper.setupOverlay(overlayCanvas, chartCanvas);

// Correct - wait for chart
setTimeout(() => {
    const { ctx } = CanvasHelper.setupOverlay(overlayCanvas, chartCanvas);
}, 100);
```

2. **Not clearing between draws**
```javascript
// Wrong - drawings accumulate
function update() {
    CanvasHelper.drawErrorSquares(ctx, chart, data, predict);
}

// Correct - clear first
function update() {
    CanvasHelper.clear(overlayCanvas, ctx);
    CanvasHelper.drawErrorSquares(ctx, chart, data, predict);
}
```

3. **Using wrong canvas dimensions**
```javascript
// Wrong - doesn't account for DPR
ctx.clearRect(0, 0, canvas.width, canvas.height);

// Correct - use display dimensions
CanvasHelper.clear(canvas, ctx);
```

---

## Demo Reference

See these demos for live examples:
- `lec02a_linear-regression` - Overlay with error squares and confidence intervals
- `lec03c_regularisation` - Polynomial fitting with error visualization
