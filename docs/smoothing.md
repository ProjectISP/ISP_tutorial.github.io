# Signal Smoothing in Seismology

Smoothing is a fundamental preprocessing step in seismic signal processing. It reduces high-frequency noise while preserving the underlying trends or structures in the signal. Smoothing is often used before visualization, detection, or feature extraction.

In `ISP`, the `smoothing` function supports three types of smoothing techniques:

- **Mean Smoothing**
- **Gaussian Smoothing**
- **Teager-Kaiser Energy Operator (TKEO)**

---

## 1. Mean Smoothing

This technique replaces each sample with the **average of its neighbors** within a fixed time window. It’s useful for simple noise reduction.

**Equation:**

$$
x_{smoothed}[n] = \frac{1}{2k} \sum_{i=n-k}^{n+k} x[i]
$$

**Python Snippet:**
```python
for i in range(k, n - k - 1):
    filtsig[i] = np.mean(tr.data[i - k:i + k])
```

**Pros:**  
- Easy to implement  
- Effective for simple noise

**Cons:**  
- Can blur signal edges or transients  
- Uniform weighting may suppress meaningful features

---

## 2. Gaussian Smoothing

Gaussian smoothing weights neighbors with a **bell-shaped kernel**, giving more importance to samples near the center.

**Equation (kernel):**

$$
w[i] = \exp\left( -\frac{4 \ln(2) \cdot i^2}{\text{FWHM}^2} \right)
$$

The kernel is normalized to preserve signal energy:

$$
\sum w[i] = 1
$$

**Smoothing Operation:**

$$
x_{smoothed}[n] = \sum_{i=-k}^{k} x[n+i] \cdot w[i]
$$

**Python Snippet:**
```python
gauswin = np.exp(-(4 * np.log(2) * gtime ** 2) / fwhm ** 2)
gauswin /= np.sum(gauswin)
for i in range(k + 1, n - k - 1):
    filtsigG[i] = np.sum(tr.data[i - k:i + k] * gauswin)
```

**Pros:**  
- Smooths without sharp cutoffs  
- Preserves shape better than mean smoothing

**Tips:**  
- Use `FWHM` in seconds to control the spread  
- Always use tapering/detrending before smoothing

---

## 3. Teager-Kaiser Energy Operator (TKEO)

TKEO is a **nonlinear** operator used to enhance the energy content of rapid oscillations. It's especially useful in event detection and muscle/EMG analysis.

**Equation:**

$$
x_{tkeo}[n] = x[n]^2 - x[n-1] \cdot x[n+1]
$$

**Python Snippet:**
```python
emgf[1:-1] = emg[1:-1] ** 2 - emg[0:-2] * emg[2:]
```

**Pros:**  
- Highlights instantaneous energy  
- Useful for impulsive signal detection

**Cons:**  
- Sensitive to noise  
- Should be followed by smoothing or thresholding

---


## Summary Table

| Method   | Type       | Best For                | Preserves Phase | Notes                    |
|----------|------------|-------------------------|------------------|--------------------------|
| Mean     | Linear     | General noise           | Yes              | May blur transitions     |
| Gaussian | Linear     | Smooth trend + shape    | Yes              | Weighted for center bias |
| TKEO     | Nonlinear  | Oscillation energy      | No               | Use for detection stages |

---

Signal smoothing is a simple yet powerful tool to reduce noise and extract structure. Use with care to preserve signal characteristics needed for further seismic analysis.


---

## 4. Spike Removal with the Hampel Filter

The **Hampel filter** is a robust method for detecting and removing outliers (spikes) in time series. It replaces points that deviate significantly from the local median using a **threshold based on standard deviation**.

**Concept:**

For each point \( x[n] \), define a window \( W \) around it and compute:
- Median: \( m \)
- Median Absolute Deviation (MAD): \( 	ext{MAD} = 	ext{median}(|x[i] - m|) \)

If:

$$
|x[n] - m| > t \cdot 1.4826 \cdot 	ext{MAD}
$$

then \( x[n] \) is replaced by the local median \( m \).

**Python Code:**
```python
def hampel_filter(trace, window_size=10, threshold=3):
    x = trace.data
    n = len(x)
    new_x = x.copy()

    k = window_size
    for i in range(k, n - k):
        window = x[i - k:i + k + 1]
        median = np.median(window)
        mad = np.median(np.abs(window - median))
        if np.abs(x[i] - median) > threshold * 1.4826 * mad:
            new_x[i] = median

    trace.data = new_x
    return trace
```

**Pros:**  
- Excellent for spike removal without affecting underlying trends  
- Robust to outliers

**Tips:**  
- Use before smoothing or normalization  
- Adjust `window_size` and `threshold` depending on noise level

---

## Summary Update

| Method   | Type       | Best For                | Preserves Phase | Notes                        |
|----------|------------|-------------------------|------------------|------------------------------|
| Mean     | Linear     | General noise           | Yes              | May blur transitions         |
| Gaussian | Linear     | Smooth trend + shape    | Yes              | Weighted for center bias     |
| TKEO     | Nonlinear  | Oscillation energy      | No               | Use for detection stages     |
| Hampel   | Nonlinear  | Spike/outlier removal   | Yes              | Robust to outliers           |
