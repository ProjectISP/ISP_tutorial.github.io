# Numerical Integration and Differentiation

## Introduction

In signal processing, **integration** and **differentiation** are fundamental operations that allow us to compute displacement from velocity, or acceleration from velocity, among other conversions. These operations are especially critical in seismology, where sensors often record velocity or acceleration and users may need to convert between them.

Numerical integration and differentiation can be performed in both the **time domain** and the **frequency domain**, each with specific advantages and challenges.

---

## Numerical Integration

### 1. Trapezoidal Rule (`integrate_cumtrapz`)
**Description**:  
Uses the trapezoidal rule to perform cumulative integration of a time series.

**Equation**:  
For sample spacing Δt:

**y[n] = ∫ x(t) dt ≈ y[n-1] + (Δt / 2) * (x[n] + x[n-1])**

This method is robust and preserves signal length.

**Code Behavior**:
```python
ret = scipy.integrate.cumulative_trapezoid(data, dx=dx)
ret = np.concatenate([np.array([0]), ret])
```

---

### 2. Spline-Based Integration (`integrate_spline`)
**Description**:  
Creates an interpolating spline of the input signal, and computes the analytical integral of the spline.

**Key Parameters**:
- `k`: spline order (1 = linear, 3 = cubic, up to 5)

**Advantages**:
- Smoother result than trapezoidal rule
- Can handle non-uniformities more gracefully

**Conceptual Flow**:
1. Fit spline \( s(t) \) to input data.
2. Compute antiderivative \( S(t) = ∫ s(t) dt \)
3. Evaluate \( S(t) \) at each time point.

---

## Numerical Differentiation

### 3. Gradient Method (`differentiate(method='gradient')`)
**Description**:  
Calculates the time derivative using central differences (interior) and forward/backward differences at boundaries.

**Equation**:  
- Interior: **f'[n] ≈ (f[n+1] - f[n-1]) / (2Δt)**
- Boundaries: First-order differences

**Function Used**:  
`numpy.gradient`

**Advantages**:
- Simple and efficient
- Same output shape as input

---

## Frequency Domain Operations

### Integration in Frequency Domain
**Principle**:  
Integration corresponds to division by \( iω \), where ω = 2πf.

**Equation**:
**X(f) → Y(f) = X(f) / (i·2πf)**

### Differentiation in Frequency Domain
**Principle**:  
Differentiation corresponds to multiplication by \( iω \).

**Equation**:
**X(f) → Y(f) = X(f) · (i·2πf)**

**Caution**:
- Low-frequency components can cause **division by small numbers** (numerical instability)
- Preprocessing such as **highpass filtering** is recommended before integration
- Windowing may help reduce edge artifacts

---

## Preprocessing Recommendations

- **Detrending**: Remove any DC offset or trend to avoid ramp artifacts during integration.
- **Tapering**: Apply a window to reduce edge effects.
- **Filtering**: Highpass filter before integration to suppress low-frequency drift.
- **Padding**: Use zero-padding to reduce wrap-around artifacts in frequency-domain operations.

---

## Summary Table

| Method                | Domain       | Description                      | Key Feature                          |
|-----------------------|--------------|----------------------------------|--------------------------------------|
| `integrate_cumtrapz`  | Time         | Trapezoidal integration          | Simple, cumulative, stable           |
| `integrate_spline`    | Time         | Spline interpolation + integral | Smooth result, adjustable order      |
| `differentiate`       | Time         | Central differences              | Accurate and shape-preserving        |
| Integration (FFT)     | Frequency    | Divide by \( iω \)               | Powerful but needs careful filtering |
| Differentiation (FFT) | Frequency    | Multiply by \( iω \)             | Ideal for clean signals              |

---

These tools provide flexible, accurate methods for transforming signals between physical domains (e.g., acceleration ↔ velocity ↔ displacement) in ISP. Each has trade-offs between smoothness, stability, and simplicity.

---

## Which Integration Method Should You Use?

The best integration method depends on your signal quality, desired precision, and whether you’re working in the time or frequency domain.

### Trapezoidal Integration (Time Domain)
- **Pros**: Simple, fast, intuitive.
- **Cons**: Accumulates noise and low-frequency drift.
- **Best For**: Clean, detrended signals; quick and causal applications.

### Spline Integration (Time Domain)
- **Pros**: Smooth, accurate with noisy or irregular signals.
- **Cons**: Slightly slower, edge effects possible.
- **Best For**: Noisy data, offline processing, displacement recovery.

### Frequency-Domain Integration
- **Pros**: Clean handling of frequency components, zero-phase possible.
- **Cons**: Unstable near 0 Hz, requires preprocessing (detrend, taper, highpass).
- **Best For**: Band-limited data, spectral pipelines, zero-phase needs.

---

## Recommendation Table

| Signal Type           | Recommended Integration |
|------------------------|-------------------------|
| Clean, low-noise       | Trapezoidal             |
| Noisy or irregular     | Spline (e.g., cubic)    |
| Spectral domain work   | Frequency domain        |
| Requires smooth result | Spline                  |
| Low-frequency drift    | Always filter first     |

---

## Pro Tip

**Always**:
- **Detrend**: Remove linear trends
- **Demean**: Remove DC offset
- **Taper**: Smooth edges
- **Highpass Filter**: Suppress long-period drift

These steps are especially crucial before **frequency-domain integration** to avoid numerical artifacts and drift.







