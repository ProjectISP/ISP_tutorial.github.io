# Signal Resampling and Downsampling in Seismology


**Resampling** changes the sampling rate of a signal, either by increasing it (**upsampling**) or decreasing it (**downsampling**). In seismology, downsampling is especially important when:

- Reducing file sizes for long time series
- Matching sampling rates between stations
- Preparing data for band-limited analyses

---

## Why Be Careful with Downsampling?

Downsampling without preparation can lead to **aliasing**, where high-frequency energy folds back into lower frequencies and distorts the signal.

### Key Steps to Proper Downsampling:

1. **Detrend the signal** to remove DC offsets.
2. **Apply a lowpass filter** to eliminate frequencies above the new Nyquist frequency.
3. **Resample in small steps**, especially when reducing by large factors (e.g., 100 Hz → 1 Hz).

---

## Recommended Downsampling Strategy

```python
def safe_downsample(trace, target_rate, max_factor=10, pre_filter=True):
    """
    Downsample a trace to target_rate in steps, applying filtering.

    Parameters:
    - trace: ObsPy Trace object
    - target_rate: Final desired sampling rate (Hz)
    - max_factor: Maximum downsample ratio per step (e.g., 10)
    - pre_filter: Whether to apply lowpass filter before each step
    """
    from obspy import Trace
    import copy

    tr = trace.copy()


    while tr.stats.sampling_rate > target_rate:
        current_rate = tr.stats.sampling_rate
        factor = min(current_rate / target_rate, max_factor)
        new_rate = current_rate / factor

        tr.detrend("demean")
        tr.taper(max_percentage=0.05)

        if pre_filter:
            f_nyquist = new_rate * 0.5
            tr.filter("lowpass", freq=0.9 * f_nyquist, corners=4, zerophase=True)

        tr.resample(new_rate)

    return tr
```

---

## Mathematical Concept of Fourier-Based Resampling

Fourier-based resampling operates in the frequency domain using the following key steps:

1. Compute the FFT of the time-domain signal:
   $$
   X[k] = \sum_{n=0}^{N-1} x[n] \cdot e^{-2\pi i \cdot kn/N}
   $$

2. Interpolate or zero-pad/truncate the spectrum to match the new length \( N' \), corresponding to the desired sampling rate.

3. Optionally apply a window \( W[k] \) (e.g., Hann) to reduce spectral leakage.

4. Perform the inverse FFT to return to the time domain:
   $$
   x'[n] = \text{IFFT}(X'[k])
   $$

**Note:** This method assumes periodicity of the signal and is well-suited for smoothly varying signals.

---

## Adjusting Spectrum Length for Resampling

To change the sampling rate using Fourier methods, we adjust the number of frequency components before applying the inverse FFT. This process depends on whether we are downsampling or upsampling.

---

### 🔽 Downsampling: Truncate the Spectrum

When reducing the sampling rate, the new Nyquist frequency is lower. We must **truncate** high-frequency components that the new sampling rate cannot represent.

**How?**
```python
# Assume X is the FFT of the original signal
X_truncated = X[:N_new // 2 + 1]
```

---

### 🔼 Upsampling: Zero-Pad the Spectrum

When increasing the sampling rate, the signal must contain more samples, so we **insert zeros** in the frequency domain.

**How?**
```python
# Assume X is the FFT of the original signal
pad_length = N_new // 2 + 1 - len(X)
X_padded = np.pad(X, (0, pad_length), 'constant')
```

---

### Important Note on Symmetry

When working with real signals and `rfft`, ensure the spectrum retains Hermitian symmetry. Both truncation and padding should only be done on the one-sided spectrum and handled carefully when converting back to time domain with `irfft`.

---

## Summary Table

| Step           | Purpose                                        |
|----------------|------------------------------------------------|
| Detrend        | Removes constant offset                        |
| Lowpass Filter | Prevents aliasing by cutting high frequencies  |
| Resample       | Adjusts time step and signal length            |
| Multi-Step     | Ensures safe and smooth reduction              |
| FFT Adjustments| Truncate or pad spectrum appropriately         |

---

This strategy ensures high-quality downsampling for long-duration or broadband seismic signals, protecting both time and frequency content.