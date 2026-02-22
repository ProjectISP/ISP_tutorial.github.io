# Denoising Using Discrete Wavelet Transform (DWT) in ISP

## Introduction

The **Discrete Wavelet Transform (DWT)** is a powerful tool in signal processing that decomposes a signal into multiple levels of approximation and detail coefficients. Unlike the Fourier transform, which only provides frequency information, wavelet transforms provide **both time and frequency localization**, making them highly effective for analyzing non-stationary signals like seismic data.

Wavelet denoising works by thresholding the **detail coefficients**, which usually contain noise, while preserving the important structures in the approximation components. Wavelet denoising suppresses **small-amplitude high-frequency noise**, while preserving sharp transients (like seismic P or S waves). It's more adaptive than global filtering (e.g., bandpass). Wavelet-based denoising is a powerful tool in the ISP toolkit, ideal for signals where traditional filters either miss detail or allow noise to pass.

---

## How DWT Works

Given a signal x(t), the DWT decomposes it into:

- **Approximation coefficients (cA)** – representing the low-frequency components (general trends).
- **Detail coefficients (cD)** – representing high-frequency components (details and noise).

The decomposition is performed recursively, creating a multi-resolution analysis of the signal.

---

## Mathematical Principle

The wavelet transform uses a **mother wavelet** psi(t) and scales/shifts it to analyze different parts of the signal.

**Equation:**  
$$
x(t) = \sum_{j} \sum_{k} c_{j,k} \, \psi_{j,k}(t)
$$

Where:
- \( j \): scale (level)  
- \( k \): translation (position)  
- \( \psi_{j,k}(t) \): scaled and shifted wavelet  
- \( c_{j,k} \): wavelet coefficients

---

## Denoising Strategy

1. **Decompose** the signal into wavelet coefficients up to a maximum level.
2. **Threshold** the detail coefficients to suppress noise.
3. **Reconstruct** the signal from the modified coefficients.

---

## Implementation in ISP

```python
def wavelet_denoise(tr, threshold=0.04, dwt='sym4'):
    w = pywt.Wavelet(dwt)
    maxlev = pywt.dwt_max_level(len(tr.data), w.dec_len)
    coeffs = pywt.wavedec(tr.data, dwt, level=maxlev)

    for i in range(1, len(coeffs)):
        coeffs[i] = pywt.threshold(coeffs[i], threshold * max(coeffs[i]))

    tr.data = pywt.waverec(coeffs, dwt)
    return tr
```

---

## Parameters Explained

- **`dwt`**: The wavelet type (e.g., 'sym4', 'db4'). Affects the shape of the filters used.
- **`threshold`**: Defines how aggressively detail coefficients are reduced. Higher values remove more noise but may also cut signal details.
- **`maxlev`**: Number of decomposition levels. Automatically chosen based on signal length and wavelet type.

---

## Practical Tips

- Use wavelets like 'sym4' or 'db4' for seismic signals—they balance smoothness and compact support.
- Apply **tapering and detrending** before wavelet denoising to avoid edge effects.
- Try different thresholds (e.g., 0.01–0.1) to find the best noise-signal tradeoff.

---

## Summary Table

| Component         | Role                     | Modified? |
|------------------|--------------------------|-----------|
| Approximation (cA)| Signal trend (low-freq)  | ❌ No      |
| Detail (cD)       | Noise + detail (hi-freq) | ✅ Yes     |

---

# Wiener Filter Denoising

## Introduction

The **Wiener filter** is a classic signal processing technique for noise reduction based on **statistical estimation**. The Wiener filter offers flexible, adaptive denoising with simple parameterization and reliable results. Unlike simple smoothing filters, the Wiener filter adapts to **local variance** in the signal and attempts to **preserve detail** while removing noise. The method assumes an **additive noise model** and minimizes the **mean squared error** between the estimated and true signal.


---

## Mathematical Background

Given a noisy signal:  
$$
x(t) = s(t) + n(t)
$$

Where:
- \( x(t) \): observed signal  
- \( s(t) \): true (unknown) signal  
- \( n(t) \): additive noise

The Wiener filter aims to estimate \( s(t) \) by computing a **weighted local average**, where weights depend on the signal-to-noise ratio.

**Key equation for 1D signal estimate:**
$$
\hat{s}[n] = \mu + \left( \frac{\sigma^2 - \nu^2}{\sigma^2} \right) \cdot (x[n] - \mu)
$$

Where:
- \( \mu \): local mean  
- \( \sigma^2 \): local variance  
- \( \nu^2 \): noise power (estimated or provided)  
- \( \hat{s}[n] \): denoised signal value

---

## Implementation in ISP

```python
def wiener_filter(tr, time_window, noise_power):
    data = tr.data

    if time_window == 0 and noise_power == 0:
        denoise = scipy.signal.wiener(data, mysize=None, noise=None)
        tr.data = denoise

    elif time_window != 0 and noise_power == 0:
        denoise = scipy.signal.wiener(data, mysize=int(time_window * tr.stats.sampling_rate), noise=None)
        tr.data = denoise

    elif time_window == 0 and noise_power != 0:
        noise = noise_power * np.std(data)
        noise = int(noise)
        denoise = scipy.signal.wiener(data, mysize=None, noise=noise)
        tr.data = denoise

    elif time_window != 0 and noise_power != 0:
        noise = noise_power * np.std(data)
        noise = int(noise)
        denoise = scipy.signal.wiener(data, mysize=int(time_window * tr.stats.sampling_rate), noise=noise)
        tr.data = denoise

    return tr
```

---

## Parameters

- **`time_window`**: Sets the size (in seconds) of the moving window used to compute local statistics.
- **`noise_power`**: Scales the noise estimate; higher values assume stronger noise.
- **`mysize`**: Converted from `time_window` to sample points for filtering.
- **`noise`**: Directly sets the noise power if known or estimated from signal standard deviation.

---

## Practical Use

- If unsure about noise characteristics, start with `time_window = 0`, `noise_power = 0` for adaptive filtering.
- For **strong, stationary noise**, estimate noise power using `noise_power = 1–3` and test visually.
- Increasing `time_window` smooths over longer trends but can suppress sharp signal features.

---

## Summary Table

| Parameter      | Effect                                             |
|----------------|----------------------------------------------------|
| `time_window`  | Controls local averaging window size               |
| `noise_power`  | Controls strength of noise suppression             |
| `mysize`       | Computed from time window and sampling rate        |
| `noise`        | Set from \\( \text{noise\_power} \times \text{std(signal)} \\) |

## Tips

- **Preprocess** your signal with detrending and tapering for best results.
- Use small windows (e.g., 0.2 to 1 second) for seismic signals to preserve transients.
- Validate results visually or via SNR measures if available.


---






