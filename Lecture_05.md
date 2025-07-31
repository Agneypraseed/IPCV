## NOISE

Noise is an unavoidable artifact in digital images, arising from various physical, electronic, and procedural sources

Sensor-Induced Noise: Modern imaging sensors (e.g., CCD, CMOS) are inherently imperfect. Electronic interference, thermal fluctuations (especially in low-light conditions), and limitations in sensor design introduce randomness in pixel intensities. For example, increasing the ISO in a camera enhances the sensor’s sensitivity, but also amplifies noise.

Modality-Specific Noise:

-   In ultrasound imaging, speckle noise is a prominent artifact caused by the constructive and destructive interference of backscattered sound waves. Unlike random noise, speckle has a granular structure that correlates with the tissue texture and imaging method.

-   Magnetic Resonance Imaging (MRI) may suffer from Rician noise, while Poisson noise is common in photon-limited modalities like low-light fluorescence microscopy or X-ray imaging.

Noise is modeled **probabilistically (Stochastic)**, not deterministically. Although the exact value at a given pixel cannot be predicted, its distribution can be described. This enables the development of statistical and machine learning-based denoising techniques, which rely on assumptions about the noise distribution.

#### Additive Noise

Grey values and noise are assumed to be independent. It means that the amount of noise added to a pixel does not depend on the pixel's original brightness. A dark pixel (e.g., value 10) and a bright pixel (e.g., value 200) are equally likely to have the same amount of noise added to them.

Additive Gaussian Noise : assumes a **linear superposition** of the original signal and a noise component, which is both **independent** and **Gaussian-distributed**.

The observed noisy image is modeled as:

$g(x, y) = f(x, y) + \eta(x, y)$

Where:

-   $g(x, y)$ is the **observed noisy image**
-   $f(x, y)$ is the **true (unknown) image**
-   $\eta(x, y)$ is the **additive noise** term

noise $n$ may obey a variety of distributions, most common is Gaussian distribution

The noise term is modeled as:

$\eta(x, y) \sim \mathcal{N}(0, \sigma^2)$

This means that the noise at each pixel is independently drawn from a normal distribution with:

-   **Mean** ($\mu$) = 0
-   **Variance** ($\sigma^2$) known and fixed

#### Other Common Noise Models

-   **Salt-and-Pepper Noise**: Impulsive in nature; randomly assigns some pixels to minimum or maximum intensity (0 or 255).
-   **Speckle Noise**: Multiplicative and structured; often follows Rayleigh or Gamma distributions; common in radar and ultrasound images.
-   **Poisson Noise**: Signal-dependent noise; arises in photon-limited imaging such as low-light photography or microscopy.

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

-   **Lower MSE** implies that the noisy image is more visually similar to the original.
-   It is a direct measure of the noise's energy relative to the image signal.

---

### 2. Signal-to-Noise Ratio (SNR)

The **Signal-to-Noise Ratio** compares the maximum pixel intensity in the image to the noise energy.

$\text{SNR}_{\text{max}} = \frac{\max_{x,y} f(x, y)^2}{\text{MSE}(f, g)}$

-   A higher SNR value indicates better visual quality.
-   This ratio is unitless and represents relative signal strength.

---

### 3. Peak Signal-to-Noise Ratio (PSNR)

The **Peak Signal-to-Noise Ratio (PSNR)** is a logarithmic scale measure, commonly used to evaluate reconstruction quality in image processing.

$\text{PSNR}(f, g) = 10 \cdot \log_{10} \left( \frac{255^2}{\text{MSE}(f, g)} \right)$

-   Measured in **decibels (dB)**.
-   Assumes 8-bit images (i.e., maximum intensity is 255).
-   **Higher PSNR** values indicate **better image quality**:
    -   `≥ 40 dB`: Excellent (imperceptible noise)
    -   `30–40 dB`: Good
    -   `20–30 dB`: Acceptable
    -   `< 20 dB`: Poor

> Noise is typically **hardly visible** when `PSNR ≥ 30 dB`.

---

## Image Segmentation

Let `R` denote the complete spatial domain of an image. Image segmentation is the process of partitioning `R` into `n` subregions:

```
R₁, R₂, ..., Rₙ
```

These subregions must satisfy the following conditions:

1. **Completeness**  
   Every pixel in the image must belong to a region:

    ```
    R = R₁ ∪ R₂ ∪ ... ∪ Rₙ
    ```

    This ensures that the segmentation fully covers the spatial domain.

2. **Connectivity**  
   Each region `Rᵢ` must be a connected set:

    ```
    Rᵢ is connected, for all i ∈ {1, 2, ..., n}
    ```

    Connectivity means that every pixel in a region `R_i` is reachable from any other pixel in `R_i` **by a path that stays entirely within the region**.

    A set of pixels is **connected** if there exists a path (within the region) between any two pixels in that set. This path must obey a selected **connectivity rule**, commonly one of the following:

    - **4-connectivity**: A pixel is connected to its immediate horizontal and vertical neighbors only.
    - **8-connectivity**: A pixel is connected to all 8 of its immediate neighbors (including diagonal neighbors).

    The type of connectivity must be predefined.

    Without enforcing connectedness, a region might consist of scattered, unrelated pixels — which is typically **not meaningful** for segmentation or analysis.

3. **Disjointness**  
   The regions must be mutually exclusive:

    ```
    Rᵢ ∩ Rⱼ = ∅, for all i ≠ j
    ```

4. **Homogeneity**  
   A logical predicate `Q(Rᵢ)` must be satisfied by all pixels in region `Rᵢ`:

    ```
    Q(Rᵢ) = TRUE, for all i ∈ {1, 2, ..., n}
    ```

    This predicate defines a property such as intensity uniformity, texture similarity, etc.
    Each region must satisfy a given property (defined by the predicate Q).

5. **Maximality**  
   For any pair of adjacent regions `Rᵢ` and `Rⱼ`, the predicate `Q` must fail when applied to their union:
    ```
    Q(Rᵢ ∪ Rⱼ) = FALSE, if Rᵢ and Rⱼ are adjacent
    ```
    Two regions are adjacent if their union forms a connected set.

---

Segmentation algorithms for monochrome images generally fall into two basic categories based on intensity properties: **discontinuity** and **similarity**.

1. **Discontinuity-based Segmentation**  
   In this category, it is assumed that region boundaries differ significantly from each other and the background, allowing boundary detection by identifying local discontinuities in intensity. Edge-based segmentation is the principal technique employed here.

2. **Region-based Segmentation**  
   Approaches in this category partition an image into regions that are similar according to predefined criteria.

### Illustration of discontinuity-based segmentation:

![alt text](/images/image43.png)

-   **Figure (a)** shows an image composed of a region of constant intensity superimposed on a darker background, also with constant intensity. These two regions constitute the entire image.It has a bright object on a dark background, both uniform regions
-   **Figure (b)** shows the result of detecting the boundary of the inner region based on intensity discontinuities. Pixels inside and outside the boundary are zero (black) because there are no intensity discontinuities there.
-   To segment the image, pixels on or inside the boundary are assigned one intensity level (e.g., white), and pixels outside the boundary are assigned another (e.g., black).
-   **Figure (c)** displays the result of this procedure, satisfying segmentation conditions (1) through (3) previously stated.
-   The predicate for condition (4) is: _If a pixel lies on or inside the boundary, label it white; otherwise, label it black._ This predicate holds true for all pixels in Figure (c). Additionally, the two segmented regions (object and background) satisfy condition (5).

### illustration of region-based segmentation:

![alt text](/images/image44.png)

-   **Figure (d)** is similar to Figure (a), except the inner region now exhibits a textured pattern.
-   **Figure (e)** : the result of applying discontinuity-based segmentation. Numerous spurious intensity changes hinder the identification of a unique boundary, making edge-based segmentation unsuitable.
    Edge-based segmentation struggles because it detects too many false edges, failing to find a unique boundary.
-   Alternative approach: However, the outer region remains constant, so a predicate Q, differentiating textured regions from constant regions suffices to solve the segmentation problem.
-   The **standard deviation** of pixel values is a suitable measure here because it is nonzero in textured areas and zero elsewhere.
-   **Figure (f)** illustrates the original image divided into subregions of size `8 × 8`. Each subregion was labeled white if the standard deviation of its pixels was positive (predicate TRUE : white if 𝜎>0), and black otherwise.
-   This segmentation produces a “blocky” appearance along the region boundary due to uniform labeling of `8 × 8` squares; smaller squares would produce a smoother boundary.
-   These results satisfy all five segmentation conditions and works better than edge detection for textured images.
