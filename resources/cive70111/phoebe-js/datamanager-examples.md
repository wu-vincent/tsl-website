# DataManager Examples

Complete examples showing how to use the DataManager class for CSV parsing, file loading, and data transformations.

## CSV Parsing (Synchronous)

### Example 1: Parse CSV Text

```javascript
const csvText = `
Name,Age,Score
Alice,25,95
Bob,30,87
Charlie,22,92
`;

const data = DataManager.parseCSV(csvText);
console.log(data);
// [
//   { Name: 'Alice', Age: 25, Score: 95 },
//   { Name: 'Bob', Age: 30, Score: 87 },
//   { Name: 'Charlie', Age: 22, Score: 92 }
// ]
```

### Example 2: Custom Parsing Options

```javascript
// Tab-delimited, no number parsing
const tsvText = "X\tY\n1.5\t2.3\n4.2\t5.1";

const data = DataManager.parseCSV(tsvText, {
    delimiter: '\t',
    parseNumbers: false  // Keep as strings
});
```

### Example 3: Convert to CSV

```javascript
const data = [
    { x: 1, y: 2 },
    { x: 3, y: 4 },
    { x: 5, y: 6 }
];

const csvText = DataManager.toCSV(data);
console.log(csvText);
// "x,y
// 1,2
// 3,4
// 5,6"
```

### Example 4: Extract Specific Columns

```javascript
const data = DataManager.parseCSV(csvText);

// Extract multiple columns as arrays
const columns = DataManager.extractColumns(data, ['Name', 'Score']);
// { Name: ['Alice', 'Bob', 'Charlie'], Score: [95, 87, 92] }

// Extract two columns as {x, y} points
const points = DataManager.extractXY(data, 'Age', 'Score');
// [{ x: 25, y: 95 }, { x: 30, y: 87 }, { x: 22, y: 92 }]
```

## File Loading (Asynchronous)

### Example 1: Load CSV File

```javascript
async function loadData() {
    try {
        // Load and extract x, y columns
        const data = await DataManager.loadCSV(
            'data/temperature.csv',
            'temp',      // X column
            'demand'     // Y column
        );

        console.log(data);
        // [{ x: 15, y: 120 }, { x: 20, y: 150 }, ...]

    } catch (error) {
        console.error('Failed to load data:', error);
    }
}
```

### Example 2: Load from Configuration

```javascript
const datasets = {
    structures: {
        file: 'example-data/structures.csv',
        xColumn: 'load',
        yColumn: 'deflection',
        xLabel: 'Load (kN)',
        yLabel: 'Deflection (mm)'
    },
    geotechnical: {
        file: 'example-data/geotechnical.csv',
        xColumn: 'depth',
        yColumn: 'pressure',
        xLabel: 'Depth (m)',
        yLabel: 'Pressure (kPa)'
    }
};

// Load single dataset
const structuresData = await DataManager.loadFromConfig(datasets.structures);

// Load multiple datasets in parallel
const allData = await DataManager.loadMultiple(datasets);
console.log(allData.structures);
console.log(allData.geotechnical);
```

### Example 3: Load Multiple Columns

```javascript
// Load specific columns from CSV
const data = await DataManager.loadColumns(
    'data/multi-column.csv',
    ['timestamp', 'temperature', 'humidity', 'pressure']
);

// Result: array of objects with only those columns
console.log(data[0]);
// { timestamp: '2024-01-01', temperature: 22.5, humidity: 65, pressure: 1013 }
```

### Example 4: Load Complete File

```javascript
// Load all columns from CSV
const fullData = await DataManager.loadFull('data/complete.csv');

// Access any column
fullData.forEach(row => {
    console.log(row.Name, row.Age, row.Score, row.Grade);
});
```

### Example 5: Load JSON File

```javascript
// Load JSON data
const config = await DataManager.loadJSON('data/config.json');
console.log(config.settings);

// Load preprocessed model weights
const weights = await DataManager.loadJSON('data/model_weights.json');
```

## Data Analysis

### Example 1: Filter Data

```javascript
// Filter data by condition
const highScores = DataManager.filter(data, row => row.Score > 90);

// Filter by multiple conditions
const youngHighScorers = DataManager.filter(data, row =>
    row.Age < 30 && row.Score > 90
);
```

### Example 2: Sort Data

```javascript
// Sort by column (ascending)
const sortedByAge = DataManager.sort(data, 'Age');

// Sort descending
const sortedByScoreDesc = DataManager.sort(data, 'Score', false);
```

### Example 3: Column Statistics

```javascript
const stats = DataManager.columnStats(data, 'Score');
console.log(stats);
// {
//   mean: 91.33,
//   std: 4.16,
//   min: 87,
//   max: 95,
//   median: 92
// }
```

## Data Transformations

### Example 1: Calculate Bounds

```javascript
const data = [
    { x: 10, y: 20 },
    { x: 50, y: 80 },
    { x: 30, y: 40 }
];

// Calculate bounds with 10% padding
const bounds = DataManager.calculateBounds(data, 10);
console.log(bounds);
// { xMin: 6, xMax: 54, yMin: 14, yMax: 86 }
```

### Example 2: Normalize Data

```javascript
const data = [
    { x: 10, y: 100 },
    { x: 20, y: 200 },
    { x: 30, y: 300 }
];

const { normalizedData, xScale, yScale } = DataManager.normalize(data);

console.log(normalizedData);
// [{ x: 0, y: 0 }, { x: 0.5, y: 0.5 }, { x: 1, y: 1 }]

console.log(xScale);
// { min: 10, max: 30, range: 20 }

// Denormalize back to original scale
const original = DataManager.denormalize(normalizedData, xScale, yScale);
// [{ x: 10, y: 100 }, { x: 20, y: 200 }, { x: 30, y: 300 }]
```

### Example 3: Standardize Data

```javascript
const { standardizedData, xStats, yStats } = DataManager.standardize(data);

console.log(standardizedData);
// Data with mean=0, std=1

console.log(xStats);
// { mean: 20, std: 8.165 }
```

### Example 4: Train/Test Split

```javascript
const data = Array.from({ length: 100 }, (_, i) => ({ x: i, y: i * 2 }));

// 80/20 split with shuffling
const { train, test } = DataManager.trainTestSplit(data, 0.8, true);

console.log(train.length);  // 80
console.log(test.length);   // 20

// Split without shuffling
const { train: trainSeq, test: testSeq } = DataManager.trainTestSplit(data, 0.7, false);
```

## Complete Demo Integration

### Example: Regression Demo

```javascript
class LinearRegressionDemo {
    constructor() {
        this.datasets = {
            temperature: {
                file: 'example-data/temperature.csv',
                xColumn: 'temp',
                yColumn: 'demand',
                xLabel: 'Temperature (°C)',
                yLabel: 'Demand (kW)'
            },
            pressure: {
                file: 'example-data/pressure.csv',
                xColumn: 'depth',
                yColumn: 'pressure',
                xLabel: 'Depth (m)',
                yLabel: 'Pressure (kPa)'
            }
        };

        this.dataPoints = [];
    }

    async loadDataset(key) {
        try {
            // Load data
            this.dataPoints = await DataManager.loadFromConfig(this.datasets[key]);

            // Calculate bounds for chart
            const bounds = DataManager.calculateBounds(this.dataPoints, 10);

            // Update chart axes
            this.updateChart(bounds);

            console.log(`Loaded ${this.dataPoints.length} points`);

        } catch (error) {
            console.error('Error loading dataset:', error);
        }
    }

    updateChart(bounds) {
        // Use bounds to set chart ranges
        this.chart.options.scales.x.min = bounds.xMin;
        this.chart.options.scales.x.max = bounds.xMax;
        this.chart.options.scales.y.min = bounds.yMin;
        this.chart.options.scales.y.max = bounds.yMax;
        this.chart.update();
    }
}
```

### Example: Data Preprocessing Pipeline

```javascript
async function preprocessData(filename) {
    // 1. Load CSV file
    const rawData = await DataManager.loadFull(filename);

    // 2. Filter outliers
    const filtered = DataManager.filter(rawData, row =>
        row.value > 0 && row.value < 1000
    );

    // 3. Sort by timestamp
    const sorted = DataManager.sort(filtered, 'timestamp');

    // 4. Extract x, y columns
    const points = DataManager.extractXY(sorted, 'time', 'value');

    // 5. Normalize data
    const { normalizedData, xScale, yScale } = DataManager.normalize(points);

    // 6. Split into train/test
    const { train, test } = DataManager.trainTestSplit(normalizedData, 0.8);

    return {
        train,
        test,
        scales: { xScale, yScale }
    };
}

// Usage
const { train, test, scales } = await preprocessData('data/timeseries.csv');
```

## Advanced Patterns

### Example 1: CSV Headers Inspection

```javascript
const csvText = `Name,Age,Score,Grade\nAlice,25,95,A`;

const headers = DataManager.getCSVHeaders(csvText);
console.log(headers);  // ['Name', 'Age', 'Score', 'Grade']
```

### Example 2: Custom CSV Export

```javascript
const data = [
    { name: 'Alice', score: 95, passed: true },
    { name: 'Bob', score: 87, passed: true },
    { name: 'Charlie', score: 65, passed: false }
];

// Export only specific columns
const csv = DataManager.toCSV(data, ['name', 'score']);

// Download as file
const blob = new Blob([csv], { type: 'text/csv' });
const url = URL.createObjectURL(blob);
const a = document.createElement('a');
a.href = url;
a.download = 'results.csv';
a.click();
```

### Example 3: Error Handling

```javascript
async function safeLoadData(file) {
    try {
        const data = await DataManager.loadCSV(file, 'x', 'y');
        return { success: true, data };
    } catch (error) {
        if (error.message.includes('HTTP error')) {
            return { success: false, error: 'File not found' };
        } else if (error.message.includes('not found in CSV')) {
            return { success: false, error: 'Invalid column names' };
        } else {
            return { success: false, error: 'Unknown error' };
        }
    }
}
```

### Example 4: Dynamic Column Selection

```javascript
// Let user choose which columns to plot
const headers = DataManager.getCSVHeaders(await fetch('data.csv').then(r => r.text()));

// Create dropdown menus
const xSelect = document.getElementById('x-column');
const ySelect = document.getElementById('y-column');

headers.forEach(header => {
    xSelect.add(new Option(header, header));
    ySelect.add(new Option(header, header));
});

// Load selected columns
document.getElementById('load-btn').addEventListener('click', async () => {
    const xCol = xSelect.value;
    const yCol = ySelect.value;
    const data = await DataManager.loadCSV('data.csv', xCol, yCol);
    updateChart(data);
});
```

## Performance Tips

1. **Use `loadFull()` once** - Load complete CSV once, then extract columns in memory rather than multiple file loads

2. **Parallel loading** - Use `loadMultiple()` to load datasets in parallel

3. **Filter before transform** - Filter/sort data before expensive operations like normalization

4. **Cache parsed data** - Store parsed CSV in memory if needed multiple times

```javascript
// Good: Load once, transform multiple times
const fullData = await DataManager.loadFull('large-dataset.csv');
const points = DataManager.extractXY(fullData, 'x', 'y');
const normalized = DataManager.normalize(points);
const standardized = DataManager.standardize(points);

// Bad: Multiple file loads
const points = await DataManager.loadCSV('large-dataset.csv', 'x', 'y');
const fullData = await DataManager.loadFull('large-dataset.csv');  // Redundant
```

## Common Pitfalls

1. **Forgetting `await`** - All load methods are async
```javascript
// Wrong
const data = DataManager.loadCSV('data.csv', 'x', 'y');  // Returns Promise!

// Correct
const data = await DataManager.loadCSV('data.csv', 'x', 'y');
```

2. **Column names must match CSV headers exactly** (case-sensitive)
```javascript
// CSV has "Temperature" but code uses "temperature" → Error
const data = await DataManager.loadCSV('data.csv', 'temperature', 'demand');
```

3. **Empty CSV** - Always check if data is empty
```javascript
const data = await DataManager.loadCSV('data.csv', 'x', 'y');
if (data.length === 0) {
    console.warn('No data loaded');
}
```

## Migration from Old API

If you were using the old `CSVParser` and `DatasetLoader` separately:

```javascript
// Old way (two classes)
const csvText = await fetch('data.csv').then(r => r.text());
const parsed = CSVParser.parse(csvText);
const points = CSVParser.extractXY(parsed, 'x', 'y');
const data = await DatasetLoader.loadCSV('data.csv', 'x', 'y');

// New way (one class)
const csvText = await fetch('data.csv').then(r => r.text());
const parsed = DataManager.parseCSV(csvText);
const points = DataManager.extractXY(parsed, 'x', 'y');
const data = await DataManager.loadCSV('data.csv', 'x', 'y');
```

Just replace `CSVParser` and `DatasetLoader` with `DataManager`.
