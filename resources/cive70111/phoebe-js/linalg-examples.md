# LinAlg Helper Examples

This guide highlights the lightweight matrix utilities exposed by `linalg.js` and shows how to compose them inside Phoebe demos or standalone scripts.

## Basic usage

```javascript
const LinAlg = require('./linalg.js');

const X = [
    [1, 2],
    [3, 4],
];
const y = [5, 6];

// Transpose and multiply matrices
const XT = LinAlg.transpose(X);
const gram = LinAlg.multiply(XT, X);

// Solve normal equations using Gaussian elimination
const rhs = LinAlg.vectorMultiply(XT, y);
const weights = LinAlg.gaussianElimination(gram, rhs);
```

## Prediction helpers

```javascript
const features = [
    [2.5],
    [3.5],
];

const withBias = LinAlg.addBiasColumn(features);
const predictions = LinAlg.predict(withBias, weights);
```

## Browser integration

When bundled with the Phoebe demos, `linalg.js` registers `LinAlg` on the global object (and keeps `LinearAlgebra` as a backwards-compatible alias). You can therefore access it directly from scripts loaded in `content.html` without additional imports:

```html
<script src="../phoebe-js/linalg.js"></script>
<script>
    const identity = LinAlg.identity(3);
    console.log(identity);
</script>
```

The implementations are optimised for small matrices (≈12×12) where clarity and determinism matter more than raw performance. For larger workloads consider delegating to a dedicated numerical library.
