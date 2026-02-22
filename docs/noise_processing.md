# Noise Processing in Seismology

## Introduction

Seismic noise is not merely a nuisance — it holds valuable information about the Earth's structure when properly processed. In modern seismology, especially in ambient noise tomography and passive imaging, noise becomes the **signal**. Understanding how to manipulate, simulate, and prepare noise for analysis is essential.

This tutorial presents techniques to:
- **Simulate noise** (white or colored)
- **Preprocess data** for noise cross-correlation
- Apply **time-domain normalization** and **spectral whitening**

---

## 1. Simulating Noise

Synthetic noise allows us to test algorithms, study SNR effects, and validate signal extraction procedures.

### 1.1 Adding White Noise

White noise has equal power across all frequencies. It's useful for basic testing or simulating high-frequency noise.

**Equation:**  
$$
x_{noisy}[n] = x[n] + \alpha \cdot \mathcal{N}(0, 1)
$$

Where:
- \( x[n] \): original signal
- \( \alpha \): noise amplitude scaling factor
- \( \mathcal{N}(0, 1) \): standard normal distribution

```python
def add_white_noise(trace, noise_level=0.01):
    noise = np.random.normal(0, noise_level, len(trace.data))
    trace.data += noise
    return trace
```

---

### 1.2 Adding Colored Noise (e.g., Pink, Brown)

Colored noise has a power spectral density inversely proportional to frequency.

**Example**:  
- Pink noise: \( P(f) \propto 1/f \)
- Brown noise: \( P(f) \propto 1/f^2 \)

```python
def add_colored_noise(trace, exponent=1):
    white = np.random.randn(len(trace.data))
    fft = np.fft.rfft(white)
    freqs = np.fft.rfftfreq(len(trace.data), d=trace.stats.delta)
    fft /= np.where(freqs == 0, 1, freqs ** (exponent / 2))
    noise = np.fft.irfft(fft, n=len(trace.data))
    trace.data += noise
    return trace
```

---

## 2. Preparing Noise for Cross-Correlation

Before computing noise cross-correlations, raw noise traces must be **normalized and whitened** to emphasize coherent signals and suppress local bursts or instrument artifacts.

---

### 2.1 Time-Domain Normalization (e.g., One-Bit Normalization)

Time normalization standardizes amplitude fluctuations to reduce the influence of transient spikes or earthquakes.

**One-bit normalization:**

$$
x_{norm}[n] = 	ext{sign}(x[n])
$$

Removes amplitude info, keeps polarity.

```python
def one_bit_normalize(trace):
    trace.data = np.sign(trace.data)
    return trace
```

- **Clipping normalization** (cap values at ± threshold)

$$
x_{norm}[n] = 
\begin{cases}
  -C &  ext{if } x[n] < -C \\
  x[n] &    ext{if } -C \leq x[n] \leq C \\
  +C &  ext{if } x[n] > C
\end{cases}
$$

```python

def normalize_clip(tr,clip_factor=6, clip_weight=10, norm_method='clipping')

    if norm_method == 'clipping':
        lim = clip_factor * np.std(tr.data)
        tr.data[tr.data > lim] = lim
        tr.data[tr.data < -lim] = -lim

    elif norm_method == "clipping_iter":
        lim = clip_factor * np.std(np.abs(self.tr.data))

        # as long as still values left above the waterlevel, clip_weight
        while tr.data[np.abs(self.tr.data) > lim] != []:
            tr.data[tr.data > lim] /= clip_weight
            tr.data[tr.data < -lim] /= clip_weight
```


- **Running absolute mean normalization**

$$
x_{norm}[n] = \frac{x[n]}{\frac{1}{W} \sum_{i=n-W/2}^{n+W/2} |x[i]|}
$$

Where:
- \( W \): window length in samples  
- \( |x[i]| \): absolute value of the signal within the window


```python
def normalize(tr, norm_win=15):
    norm_win = int(norm_win*tr.stats.sampling_rate)
    window = np.ones(norm_win)/norm_win
    tr.data = tr.data/np.convolve(np.abs(self.tr.data), window, mode='same')
    tr.taper(type="blackman", max_percentage=0.05)
```
---

### 2.2 Spectral Whitening

Spectral whitening equalizes all frequency bands to prevent dominance by low frequencies.

**Conceptual Equation:**

Given the FFT of a trace \( X(f) \), and the smoothed amplitude \( A_{avg}(f) \), whitening is done as:

$$
X_{white}(f) = \frac{X(f)}{A_{avg}(f)}
$$

Where:
- \( A_{avg}(f) \): computed as a moving average of \( |X(f)| \) over a specified frequency bandwidth

```python
def spectral_whitening(tr, freq_width=0.05, taper_edge=True):

    """"
    freq_width: Frequency smoothing windows [Hz] / both sides
    taper_edge: taper with cosine window the low frequencies

    return: whithened trace (Phase is not modified)
    """""

    fs = tr.stats.sampling_rate
    N = tr.count()
    D = 2 ** math.ceil(math.log2(N))
    freq_res = 1 / (D / fs)
    # N_smooth = int(freq_width / (2 * freq_res))
    N_smooth = int(freq_width / (freq_res))

    if N_smooth % 2 == 0:  # To have a central point
        N_smooth = N_smooth + 1
    else:
        pass

    # avarage_window_width = (2 * N_smooth + 1) #Denominador
    avarage_window_width = (N_smooth + 1)  # Denominador
    half_width = int((N_smooth + 1) / 2)  # midpoint
    half_width_pos = half_width - 1

    # Prefilt
    tr.detrend(type='simple')
    tr.taper(max_percentage=0.05)

    # ready to whiten
    data = tr.data
    data_f = np.fft.rfft(data, D)
    freq = np.fft.rfftfreq(D, 1. / fs)
    N_rfft = len(data_f)
    data_f_whiten = data_f.copy()
    index = np.arange(0, N_rfft - half_width, 1)

    data_f_whiten = whiten_aux(data_f, data_f_whiten, index, half_width, avarage_window_width, half_width_pos)

    # Taper (optional) and remove mean diffs in edges of the frequency domain

    wf = (np.cos(np.linspace(np.pi / 2, np.pi, half_width)) ** 2)

    if taper_edge:

        diff_mean = np.abs(np.mean(np.abs(data_f[0:half_width])) - np.mean(np.abs(data_f_whiten[half_width:])) * wf)

    else:

        diff_mean = np.abs(np.mean(np.abs(data_f[0:half_width])) - np.mean(np.abs(data_f_whiten[half_width:])))

    diff_mean2 = np.abs(
        np.mean(np.abs(data_f[(N_rfft - half_width):])) - np.mean(np.abs(data_f_whiten[(N_rfft - half_width):])))

    if taper_edge:

        data_f_whiten[0:half_width] = ((data_f[0:half_width]) / diff_mean)*wf  # First part of spectrum tapered
    else:

        data_f_whiten[0:half_width] = ((data_f[0:half_width]) / diff_mean)


    data_f_whiten[(N_rfft - half_width):] = (data_f[(N_rfft - half_width):]) / diff_mean2  # end of spectrum
    data = np.fft.irfft(data_f_whiten)
    data = data[0:N]
    tr.data = data

    return tr
```
**Best Practice:**  
Always apply tapering by applying a bandpass filtering before whitening to avoid spectral edge effects and aliasing.

---

## 3. Best Practices for Noise Processing

| Step                  | Purpose                                |
|-----------------------|----------------------------------------|
| Add synthetic noise   | Test methods, control SNR              |
| One-bit normalization | Reduce spike/earthquake bias           |
| Spectral whitening    | Flatten spectrum, enhance coherence    |
| Detrend & taper       | Prevent spectral artifacts             |
| Bandpass filter       | Limit frequency range before analysis  |

---

## 4. Use Case: Noise Cross-Correlation

After applying time and spectral normalization:

```python
from obspy.signal.cross_correlation import correlate

# Preprocess both traces
tr1 = spectral_whitening(one_bit_normalize(tr1))
tr2 = spectral_whitening(one_bit_normalize(tr2))

# Cross-correlate
cc = correlate(tr1.data, tr2.data, shift=len(tr1.data)//2)
```

This produces a **noise correlation function (NCF)** which may approximate the Green's function between the two stations.

---

## Summary

Noise processing transforms chaotic raw signals into usable seismic information. Whether simulating realistic environments with colored noise or preparing for cross-correlation via one-bit normalization and spectral whitening, these steps are essential for passive imaging and ambient noise studies.

This pipeline is fully integrated into the `ISP` processing tools, enabling robust and flexible ambient noise workflows.

