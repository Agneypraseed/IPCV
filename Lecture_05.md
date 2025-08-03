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

$\text{SNR}\_{\max} = \frac{\max\_{x,y} f(x, y)^2}{\text{MSE}(f, g)}$

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

1.  **Completeness**  
     Every pixel in the image must belong to a region:

        ` R = R₁ ∪ R₂ ∪ ... ∪ Rₙ

    `

        This ensures that the segmentation fully covers the spatial domain.

2.  **Connectivity**  
     Each region `Rᵢ` must be a connected set:

        ` Rᵢ is connected, for all i ∈ {1, 2, ..., n}

    `

        Connectivity means that every pixel in a region `R_i` is reachable from any other pixel in `R_i` **by a path that stays entirely within the region**.

        A set of pixels is **connected** if there exists a path (within the region) between any two pixels in that set. This path must obey a selected **connectivity rule**, commonly one of the following:

        - **4-connectivity**: A pixel is connected to its immediate horizontal and vertical neighbors only.
        - **8-connectivity**: A pixel is connected to all 8 of its immediate neighbors (including diagonal neighbors).

        The type of connectivity must be predefined.

        Without enforcing connectedness, a region might consist of scattered, unrelated pixels — which is typically **not meaningful** for segmentation or analysis.

3.  **Disjointness**  
     The regions must be mutually exclusive:

        ` Rᵢ ∩ Rⱼ = ∅, for all i ≠ j

    `

4.  **Homogeneity**  
     A logical predicate `Q(Rᵢ)` must be satisfied by all pixels in region `Rᵢ`:

        ` Q(Rᵢ) = TRUE, for all i ∈ {1, 2, ..., n}

    `

        This predicate defines a property such as intensity uniformity, texture similarity, etc.
        Each region must satisfy a given property (defined by the predicate Q).

5.  **Maximality**  
     For any pair of adjacent regions `Rᵢ` and `Rⱼ`, the predicate `Q` must fail when applied to their union:

        ` Q(Rᵢ ∪ Rⱼ) = FALSE, if Rᵢ and Rⱼ are adjacent

    `

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
-   If the block has some **texture** (i.e., variation in intensity),  
    → $σ_k > 0$  
    → The patch is labeled **white** (indicating it is part of a textured region).
-   If the block is **uniform** (i.e., all pixel values are the same),  
    → $σ_k = 0$  
    → The patch is labeled **black** (indicating it is part of the uniform background).
-   This segmentation produces a “blocky” appearance along the region boundary due to uniform labeling of `8 × 8` squares; smaller squares would produce a smoother boundary.
-   These results satisfy all five segmentation conditions and works better than edge detection for textured images.

---

### Edge-Based Segmentation: Detection of Local Intensity Changes

Segmentation techniques that rely on detecting **sharp, localized changes in image intensity** - which are signs of important features like:

1. **Isolated Points**
2. **Lines**
3. **Edges**

These features are the foundation of discontinuity-based segmentation, especially edge-based methods.

#### Edge Pixels and Edge Segments

An **edge pixel** is defined as a pixel at which the intensity of an image undergoes an abrupt change. A collection of such pixels that are **connected** forms an **edge** or **edge segment**.

Edge detection is typically implemented using **edge detectors**(Detected using derivatives), which are local image processing operators designed to identify edge pixels.

#### Lines and Roof Edges

A **line** in an image can be interpreted as a **narrow edge segment** in which the intensity of the background on either side of the line differs significantly—either being much higher or much lower—compared to the intensity of the line pixels themselves.

Lines, when analyzed through gradient-based approaches, often produce what are referred to as **roof edges** due to the gradual transition of intensity values.

#### Isolated Points

An **isolated point** may be understood as a **foreground pixel** entirely surrounded by **background pixels**, or vice versa. From a processing standpoint, it is a point of high contrast compared to its immediate neighbors, standing out distinctly due to its intensity disparity.

---

### Derivatives for Edge Detection in Digital Images

**Local averaging** smooths an image. Given that averaging is analogous to integration, it is intuitive that abrupt, localized changes in intensity can be effectively detected using **derivatives**.

To detect sharp changes in intensity (edges), we use derivatives

Specifically, **first-order** and **second-order derivatives** are particularly suitable for this task.

#### Properties of Digital Derivatives

Derivatives of a digital function are defined in terms of **finite differences**. While there are various methods to compute these differences, approximations for the first and second derivatives must satisfy the following conditions.

-   **First-order derivative approximations must:**

    1. Be zero in regions of constant intensity.
    2. Be nonzero at the onset of an intensity step or ramp.
    3. Be nonzero at points along an intensity ramp.

-   **Second-order derivative approximations must:**
    1. Be zero in regions of constant intensity.
    2. Be nonzero at the onset and end of an intensity step or ramp.
    3. Be zero along intensity ramps.

Given that we are working with **digital quantities** (i.e., finite values), the maximum possible change in intensity is finite, and the shortest interval over which this change can occur is **between adjacent pixels**.

---

#### Taylor Series Expansion

An approximation to the first derivative at an arbitrary point $x$ in a one-dimensional function $f(x)$ can be derived by expanding $f(x + \Delta x)$ into a **Taylor series** about $x$:

$$
f(x + \Delta x) = f(x) + \frac{\partial f(x)}{\partial x} \Delta x + \frac{\partial^2 f(x)}{\partial x^2} \frac{\Delta x^2}{2!} + \frac{\partial^3 f(x)}{\partial x^3} \frac{\Delta x^3}{3!} + \cdots
$$

For practical digital processing, the separation $\Delta x$ between samples is measured in **pixel units**. Conventionally, we let $\Delta x = 1$ for the next pixel and $\Delta x = -1$ for the previous pixel.

-   When $\Delta x = 1$, Equation becomes:

$$
f(x + 1) = f(x) + \frac{\partial f(x)}{\partial x} + \frac{\partial^2 f(x)}{\partial x^2} \frac{1}{2!} + \frac{\partial^3 f(x)}{\partial x^3} \frac{1}{3!} + \cdots \qquad (Eq 10-2)
$$

-   Similarly, when $\Delta x = -1$:

$$
f(x - 1) = f(x) - \frac{\partial f(x)}{\partial x} + \frac{\partial^2 f(x)}{\partial x^2} \frac{1}{2!} - \frac{\partial^3 f(x)}{\partial x^3} \frac{1}{3!} + \cdots
\qquad (Eq 10-3)
$$

#### Finite Difference Approximations

By retaining only the **linear terms** in the series expansions above, we can form approximations to the first derivative using finite differences:

-   **Forward difference** (from Eq. 10-2):

$$
\frac{\partial f(x)}{\partial x} \approx f(x + 1) - f(x)
\qquad (Eq 10-4)
$$

-   **Backward difference** (from Eq. 10-3):

$$
\frac{\partial f(x)}{\partial x} \approx f(x) - f(x - 1)
\qquad (Eq 10-5)
$$

-   **Central difference** (obtained by subtracting Eq. 10-3 from Eq. 10-2):

$$
\frac{\partial f(x)}{\partial x} \approx \frac{f(x + 1) - f(x - 1)}{2}
\qquad (Eq 10-6)
$$

#### Error Considerations

The higher-order terms that are excluded in the finite difference approximations represent the **error** between the exact derivative and its approximation. In general, the **accuracy** of a derivative approximation increases with the number of terms retained from the Taylor series, as this effectively incorporates more neighboring points into the calculation.

However, it has been shown that **central difference approximations** provide lower approximation error for a given number of points. Consequently, **central differences are typically preferred** in digital image processing applications when estimating derivatives.

![alt text](/images/image45.png)
![alt text](/images/image46.png)
![alt text](/images/image47.png)
![alt text](/images/image48.png)

---

### Higher-Order Central Derivatives in Digital Image Processing

The **second-order derivative** based on a **central difference**, denoted by $\frac{\partial^2 f(x)}{\partial x^2}$, is obtained by **adding** Equations (10-2) and (10-3). This results in:

$$
\frac{\partial^2 f(x)}{\partial x^2} \approx f(x+1) + f(x-1) - 2f(x)
\qquad (Eq 10-7)
$$

To compute the **third-order central derivative**, one additional point on each side of $x$ is required. Specifically, we expand $f(x+2)$ and $f(x-2)$ using the Taylor series with $\Delta x = 2$ and $\Delta x = -2$, respectively. By combining these expansions and **eliminating all derivatives of order less than three**, the third-order approximation (ignoring higher-order terms) is given by :

$$
\frac{\partial^3 f(x)}{\partial x^3} \approx -f(x+2) + 2f(x+1) - 2f(x-1) + f(x-2)
\qquad (Eq 10-8)
$$

Similarly, the **fourth-order finite difference**, which is the highest derivative considered in the text, is derived using additional Taylor expansions and eliminating terms up to the third order. The result is:

$$
\frac{\partial^4 f(x)}{\partial x^4} \approx f(x+2) - 4f(x+1) + 6f(x) - 4f(x-1) + f(x-2)
\qquad (Eq 10-9)
$$

### Summary of Central Difference Formulas

![alt text](/images/image49.png)

One key observation is the **symmetry of the coefficients** about the center point x. This symmetry contributes to the **lower approximation error** of central differences, compared to forward or backward differences, for a given number of points.

### Extension to Two Variables

For functions of **two variables**, the same central difference formulas are applied **independently** to each variable:

-   Second derivative with respect to $x$:

$$
\frac{\partial^2 f(x, y)}{\partial x^2} \approx f(x+1, y) + f(x-1, y) - 2f(x, y)
\qquad (Eq 10-10)
$$

-   Second derivative with respect to $y$:

$$
\frac{\partial^2 f(x, y)}{\partial y^2} \approx f(x, y+1) + f(x, y-1) - 2f(x, y)
\qquad (Eq 10-11)
$$

These approximations are consistent with the properties required of first- and second-order derivatives.

#### Illustration of the behavior of these derivatives:

![alt text](/images/image50.png)

-   **(a)** displays an image containing various objects, a line, and an isolated point.
-   **(b)** presents a horizontal intensity profile (scan line : A horizontal or vertical line or even diagonal that you draw across an image to sample the intensity values of the pixels along that line, and
    Plot those values as a function of position) through the center of the image, including the isolated point.

The **intensity transitions** observed along this scan line demonstrate two edge types:

-   **Ramp edges** (gradual transitions) on the left
-   **Step edges** (abrupt transitions) on the right

Additionally, transitions associated with **thin objects**, such as lines, often manifest as **roof edges**.

---

### Behavior of First- and Second-Order Derivatives

```
🔷 What is a Ramp Edge?

  A ramp is a gradual change in intensity across multiple pixels.

  It can go from dark to light or light to dark — the direction does not define a ramp; it's the smoothness of the transition.

  The slope is consistent, meaning the brightness increases or decreases steadily

🔷 What is a Step Edge?

  A step edge is a sudden change in intensity between two adjacent pixels.

  There is no gradual slope — the change is abrupt.

  Ex : 20, 20, 100, 100 → Step edge at transition from 20 → 100

```

![alt text](/images/image51.png)

As we traverse this profile from left to right, the following properties are observed:

1. **Ramp Edge**:
    - The **first-order derivative** is **nonzero** at the onset and **along** the ramp.
    - The **second-order derivative** is **nonzero only at the onset and end** of the ramp.
    - This implies that **first-order derivatives produce "thick" edges**, while **second-order derivatives yield thinner edges**.
2. **Isolated Noise Point**:
    - The **second-order derivative** produces a **stronger response** than the first-order derivative.
    - This is expected, since second-order derivatives **amplify sharp intensity changes** more aggressively, including **fine details and noise**.
3. **Thin Line**:

    - Again, the **second derivative response is stronger**, further confirming its sensitivity to such features.
    - This type of pattern produces a roof edge in the intensity profile, characterized by a narrow peak or ridge surrounded by symmetric slopes.
    - The second derivative shows a distinct positive–negative–positive pattern around the peak, creating a double-edge effect that helps localize both sides of the line.

4. **Step Edge**:
    - For both **ramp** and **step edges**, the **second-order derivative exhibits opposite signs** as it enters and exits the edge region.
    - This **"double-edge" effect** is crucial in edge detection.
    - Additionally, the **sign of the second derivative** can be used to determine **edge polarity**:
        - A **negative second derivative** indicates a **transition from light to dark**.
        - A **positive second derivative** indicates a **transition from dark to light**.

The Step Edge

![alt text](/images/image52.png)

Key conclusions:

1. **First-order derivatives** generally result in **thicker edge profiles**.
2. **Second-order derivatives** respond more strongly to **fine details**, including noise, isolated points, and thin lines.
3. Second-order derivatives produce a **double-edge effect** at **ramp** and **step** intensity transitions.
4. The **sign of the second derivative** can help determine **whether an edge transition is from light to dark or vice versa**.

---

### Derivatives via Spatial Convolution

To compute **first** and **second derivatives** at **every pixel location** in an image, the standard method is **spatial convolution**.

For a $3 \times 3$ filter kernel, the convolution procedure is as follows:

At each pixel, compute the **sum of the products** of the kernel coefficients with the intensity values of the pixels in the region covered by the kernel. Mathematically, the response at the center pixel is given by:

$$
Z = \sum_{k=1}^{9} z_k w_k
$$

Where:

-   $z_k$ is the **intensity value** at the $k^\text{th}$ pixel under the kernel,
-   $w_k$ is the corresponding **kernel coefficient**.

This convolution-based approach applies to any linear filter used for computing local derivatives across the image.

---

### Detection of Isolated Points Using the Laplacian

**Point detection** should be based on the **second-order derivative**, which is implemented using the **Laplacian operator**:

$$
\nabla^2 f = \frac{\partial^2 f}{\partial x^2} + \frac{\partial^2 f}{\partial y^2}
$$

The partial derivatives are computed using the **second-order finite differences** from Eqs. (10-10) and (10-11). Substituting those into the Laplacian yields:

$$
\nabla^2 f(x, y) = f(x+1, y) + f(x-1, y) + f(x, y+1) + f(x, y-1) - 4f(x, y)
\text{ (Eq 10-14)}
$$

There are **two common versions** of the 2D Laplacian operator, based on how many neighbors they use:

---

### 1. **4-Connected Laplacian** (Only vertical and horizontal neighbors)

$\nabla^2 f(x, y) = f(x+1,y) + f(x-1,y) + f(x,y+1) + f(x,y-1) - 4f(x, y)$

Corresponding kernel:

$$
\begin{bmatrix}
0 & 1 & 0 \\
1 & -4 & 1 \\
0 & 1 & 0
\end{bmatrix}
$$

---

### 2. **8-Connected Laplacian** (Includes diagonals too)

$\nabla^2 f(x, y) = \sum_{\text{all 8 neighbors}} f(x+i, y+j) - 8f(x, y)$

Corresponding kernel:

$$
\begin{bmatrix}
1 & 1 & 1 \\
1 & -8 & 1 \\
1 & 1 & 1
\end{bmatrix}
$$

-   The **−4 version** is simpler and less sensitive to diagonal structures.
-   The **−8 version** gives a **stronger and more symmetrical response**, especially useful for detecting **points and lines** in all directions.

---

### Point Detection via Thresholding

We consider that a **point has been detected** at a location \( (x, y) \) if the **absolute value of the filter response** at that point exceeds a specified **non-negative threshold** \( T \). The output image is then binary, where detected points are labeled `1`, and all others `0`:

$$
g(x, y) =
\begin{cases}
1 & \text{if } |Z(x, y)| > T \\
0 & \text{otherwise}
\end{cases}
$$

Where:

-   \( g(x, y) \) is the binary output image,
-   \( Z(x, y) \) is the response computed using spatial convolution,
-   \( T \) is the threshold.

This procedure **measures weighted differences** between a pixel and its 8-neighbors. The intuition is that the **intensity of an isolated point** differs significantly from its neighborhood, making it detectable by this kernel. The kernel coefficients sum to zero, ensuring that **areas of constant intensity produce a zero response**, as is typical for derivative filters.

---

### Example: Detection of Isolated Points in an Image

![alt text](/images/image53.png)

-   **(a)** An X-ray image of a **turbine blade** from a jet engine.
-   The blade exhibits **porosity**, shown as a **single black pixel** in the upper-right quadrant.
-   **(b):** Result of filtering the image using the **Laplacian kernel**.
-   **(c):** Result of thresholding with:

    $${T = 0.9 \cdot \max\left(|Z(x, y)|\right)}$$

-   The **isolated pixel** is clearly visible at the **tip of the arrow** (enlarged for visibility).

This method is effective when **intensity changes occur abruptly** at **single-pixel locations**, surrounded by a **homogeneous background**. When this condition is not satisfied, **other edge or region detection methods** are more appropriate.

---

### Line Detection

**Second-order derivatives** provide a stronger response and yield **thinner line representations** than first-order derivatives. Therefore, the **Laplacian kernel** is also applicable for detecting lines. However, care must be taken to manage the **double-line effect** produced by second derivatives.

---

### Using the Laplacian for Line Detection

![alt text](/images/image55.png)

-   **(a)** shows a `486 × 486` binary portion of a **wire-bond mask** for an electronic circuit.
-   **(b)** presents its **Laplacian-transformed image**. Since the Laplacian output includes **negative values**, **scaling** is necessary for display.
    -   In this image:
        -   **Mid gray** denotes zero.
        -   **Darker shades** indicate negative values.
        -   **Lighter shades** indicate positive values.
    -   The **double-line effect** (one light stripe, one dark stripe) is evident in the magnified view of the image.

---

### Handling the Laplacian Output

A naive approach might suggest taking the **absolute value**
of the Laplacian image to mitigate the presence of negative values. However:

-   **(c)** : ∣Laplacian(x,y)∣ demonstrates that this approach **doubles the line thickness**, which is generally undesirable.
-   A **better approach** is to use **only the positive values** of the Laplacian image.
    -   In **noisy images**, apply a **positive threshold** to ignore minor fluctuations around zero.
-   **(d)** : if Laplacian(x,y)>T, keep it; else 0
    -   This method shows that this **selective positive filtering** yields **thinner and more accurate line detections**.

---

### Considerations on Line Width and Kernel Size

-   Observe that in **(b)–(d)**, wide lines (relative to the kernel size) exhibit a **zero “valley”** between the edges of the lines.
    -   This occurs because when a `3 × 3` kernel is centered on a line of **constant intensity** and **width of 5 pixels**, the filter response is **zero**, creating the observed effect.
    -   zero valley —> a flat region the Laplacian ignores because it has no local intensity change.
    -   Ex : ![alt text](/images/image56.png)

So, line detection works best when the kernel can "wrap around" the entire line:

-   **Assume lines are thin** relative to the detector's kernel.
-   If the line is too wide, like 3+ pixels, the center becomes flat — it looks like a region, not an edge.
-   **Wide lines** should instead be considered as **regions**, and processed using the **edge detection techniques**

---

### Directional Line Detection Using Kernels

The **Laplacian detector kernel** is **isotropic**, meaning its response is independent of direction—across vertical, horizontal, and diagonal orientations in a `3 × 3` neighborhood. However, in many applications, the goal is to **detect lines oriented in specific directions**.

---

### Directional Kernels

![alt text](/images/image57.png)

When an image with a constant background and embedded lines oriented at `0°`, `±45°`, and `90°` is filtered with each kernel, the **maximum response** occurs when a line of interest aligns with the **preferred orientation** of the kernel:

-   The **first kernel** responds maximally to **horizontal lines** (0°).
-   The **second kernel** to **+45° diagonal lines**.
-   The **third kernel** to **vertical lines** (90°).
-   The **fourth kernel** to **−45° diagonal lines**.

Each kernel emphasizes its intended direction by weighting the central axis with a **coefficient of 2**, while maintaining **zero sum** overall to ensure **no response in uniform-intensity regions**.

---

### Response and Selection

Let the outputs of the four directional kernels be denoted as:  
`Z₁`, `Z₂`, `Z₃`, and `Z₄` — corresponding to the four kernels. These outputs are computed using the spatial convolution defined as:

$$
Z = \sum_{k=1}^{9} w_k \cdot z_k
$$

At any point in the image:

-   If `Zₖ > Zⱼ` for all `j ≠ k`, then that point is most likely part of a **line oriented in direction `k`**.

For instance, if at a pixel, `Z₁` is the maximum among all four responses (|Z1| > |Zj| for j = 2,3,4), the point is associated with a **horizontal line**.

---

### Thresholding Directional Responses

To extract lines in a specific orientation (e.g., `+45°`), the following steps are followed:

1. **Convolve the image** with the corresponding directional kernel.
2. **Threshold** the **positive response**:

    $$
    g(x, y) = \begin{cases}
    1, & \text{if } Z(x, y) > T \\
    0, & \text{otherwise}
    \end{cases}
    $$

    where `T` is a positive threshold value selected based on the maximum observed response.

3. The output is a **binary image** in which `1` corresponds to points strongly aligned with the kernel direction.

---

### Example: Detecting Lines at `+45°`

We are interested in identifying **one-pixel-thick lines oriented at `+45°`**

-   **Kernel used**: Second directional kernel 
-   **(b)** shows the filtered result. The darker and lighter regions indicate negative and positive responses, respectively.

There are two significant segments oriented at `+45°`:

-   **Top-left corner**: Segment is not one pixel thick → yields weaker response.
-   **Bottom-right corner**: Segment is one pixel thick → yields stronger response .

#### Thresholding the Response

-   From **(e)**, we extract **positive values** of the Laplacian response.
-   Select threshold `T = 254` (one less than the maximum value).
-   **(f)** shows the binary output using the rule `g(x, y) > T`, with detected points marked in **white**.

Some **isolated points** also exceed the threshold. These result from local configurations that accidentally align with the kernel's preferred direction.

To address this:

-   Use the **Laplacian kernel** to detect and eliminate such isolated points, or
-   Apply **morphological operators** for cleanup.



<br>

![alt text](/images/image54.png)

---
