## NOISE

Noise is an unavoidable artifact in digital images, arising from various physical, electronic, and procedural sources

Sensor-Induced Noise: Modern imaging sensors (e.g., CCD, CMOS) are inherently imperfect. Electronic interference, thermal fluctuations (especially in low-light conditions), and limitations in sensor design introduce randomness in pixel intensities. For example, increasing the ISO in a camera enhances the sensor’s sensitivity, but also amplifies noise.

Modality-Specific Noise:

-   In ultrasound imaging, speckle noise is a prominent artifact caused by the constructive and destructive interference of backscattered sound waves. Unlike random noise, speckle has a granular structure that correlates with the tissue texture and imaging method.

-   Magnetic Resonance Imaging (MRI) may suffer from Rician noise, while Poisson noise is common in photon-limited modalities like low-light fluorescence microscopy or X-ray imaging.

Noise is modeled **probabilistically (Stochastic)**, not deterministically. Although the exact value at a given pixel cannot be predicted, its distribution can be described. This enables the development of statistical and machine learning-based denoising techniques, which rely on assumptions about the noise distribution.

#### Additive Noise

Grey values and noise are assumed to be independent.  It means that the amount of noise added to a pixel does not depend on the pixel's original brightness. A dark pixel (e.g., value 10) and a bright pixel (e.g., value 200) are equally likely to have the same amount of noise added to them.

Additive Gaussian Noise : assumes a **linear superposition** of the original signal and a noise component, which is both **independent** and **Gaussian-distributed**.

The observed noisy image is modeled as:

$g(x, y) = f(x, y) + \eta(x, y)$

Where:
- $g(x, y)$ is the **observed noisy image**
- $f(x, y)$ is the **true (unknown) image**
- $\eta(x, y)$ is the **additive noise** term

noise $n$ may obey a variety of distributions, most common is Gaussian distribution

The noise term is modeled as:

$\eta(x, y) \sim \mathcal{N}(0, \sigma^2)$

This means that the noise at each pixel is independently drawn from a normal distribution with:
- **Mean** ($\mu$) = 0
- **Variance** ($\sigma^2$) known and fixed

#### Other Common Noise Models

- **Salt-and-Pepper Noise**: Impulsive in nature; randomly assigns some pixels to minimum or maximum intensity (0 or 255).
- **Speckle Noise**: Multiplicative and structured; often follows Rayleigh or Gamma distributions; common in radar and ultrasound images.
- **Poisson Noise**: Signal-dependent noise; arises in photon-limited imaging such as low-light photography or microscopy.

##### Implementing Additive Gaussian Noise in MATLAB
```matlab
n = random('Normal', 0, 10);  % Generates a single Gaussian noise sample with μ = 0, σ = 10
f = g + n;                    % Adds noise to the original image pixelwise
```

## Measuring the Strength of Additive Gaussian Noise

To evaluate the degradation introduced by **Additive Gaussian Noise (AGN)** in a noisy image `f`, compared to the original image `g`, we use several quantitative metrics.

### 1. Mean Squared Error (MSE)

The **Mean Squared Error** is a standard metric that quantifies the average squared difference between the original and noisy images over all pixels.

For an image of size `M × N`, the MSE is given by:

$\text{MSE}(f, g) = \frac{1}{M \cdot N} \sum_{i=1}^{M} \sum_{j=1}^{N} \left[ f(i, j) - g(i, j) \right]^2$

- **Lower MSE** implies that the noisy image is more visually similar to the original.
- It is a direct measure of the noise's energy relative to the image signal.

---

### 2. Signal-to-Noise Ratio (SNR)

The **Signal-to-Noise Ratio** compares the maximum pixel intensity in the image to the noise energy.

$\text{SNR}_{\text{max}} = \frac{\max_{x,y} f(x, y)^2}{\text{MSE}(f, g)}$

- A higher SNR value indicates better visual quality.
- This ratio is unitless and represents relative signal strength.

---

### 3. Peak Signal-to-Noise Ratio (PSNR)

The **Peak Signal-to-Noise Ratio (PSNR)** is a logarithmic scale measure, commonly used to evaluate reconstruction quality in image processing.

$\text{PSNR}(f, g) = 10 \cdot \log_{10} \left( \frac{255^2}{\text{MSE}(f, g)} \right)$

- Measured in **decibels (dB)**.
- Assumes 8-bit images (i.e., maximum intensity is 255).
- **Higher PSNR** values indicate **better image quality**:
  - `≥ 40 dB`: Excellent (imperceptible noise)
  - `30–40 dB`: Good
  - `20–30 dB`: Acceptable
  - `< 20 dB`: Poor

> Noise is typically **hardly visible** when `PSNR ≥ 30 dB`.

---
