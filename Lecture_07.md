# ADVANCED TECHNIQUES FOR EDGE DETECTION

### Review of Second-Order Derivatives in Image Processing

##### The Laplacian Operator

The discrete Laplacian of an image function $f(x,y)$ is denoted as:

$\nabla^2 f(x,y)$

It is defined as the sum of the second-order partial derivatives in the horizontal and vertical directions:

$\nabla^2 f(x,y) = \frac{\partial^2 f}{\partial x^2} + \frac{\partial^2 f}{\partial y^2}$

The operator is not biased toward any direction — it gives equal weight to changes in x and y. That’s why it’s symmetric

A gradient points in a direction.

The Laplacian is scalar: it gives you a single value that says whether the intensity is peaking, dipping, or flat — but not which way. It doesn’t tell you the direction of change — only that there is a curvature

> -   The Laplacian operator is symmetric and non-directional.

---

### Discrete Approximations of Second Derivatives

-   The second derivative in the $x$-direction can be approximated by the discrete kernel:

$\frac{\partial^2 f}{\partial x^2} \approx [\,1, -2, 1\,]$

-   The second derivative in the $y$-direction can be approximated by the column kernel:

$\frac{\partial^2 f}{\partial y^2} \approx \begin{bmatrix} 1 \\ -2 \\ 1 \end{bmatrix}$

These kernels represent discrete second differences, obtained by convolving a first difference kernel with its inverse.

---

### Construction of Laplacian Kernels

By combining the two kernels, the Laplacian operator can be represented by convolution masks.

1. **4-Connectivity Laplacian Mask**  
   This mask considers only the immediate horizontal and vertical neighbors:

$\begin{bmatrix} 0 & 1 & 0 \\ 1 & -4 & 1 \\ 0 & 1 & 0 \end{bmatrix}$

2. **8-Connectivity Laplacian Mask**  
   This mask includes diagonal neighbors as well:

$\begin{bmatrix} 1 & 1 & 1 \\ 1 & -8 & 1 \\ 1 & 1 & 1 \end{bmatrix}$

---

### Interpretation and Applications

-   The **Laplacian** is a scalar operator measuring the sum of curvatures along the two principal axes.
-   It responds strongly at points of rapid intensity change, especially at edges, producing zero-crossings at edge locations.
-   In regions of constant intensity or constant slope (ramps), the Laplacian output is zero.

**Applications:**

-   **Edge Detection:** Locating edges by identifying zero-crossings in the Laplacian-filtered image.
-   **Image Sharpening:** Enhancing fine details by subtracting a scaled Laplacian from the original image, accentuating edges and textures.

Zero-crossings of the second-order derivatives correspond to the precise locations of edges in an image. However, derivative-based methods are highly sensitive to noise.

-   **Sensitivity to Noise:** Derivative operations amplify noise because they involve computing differences between neighboring pixels.

-   The second derivative is even more sensitive since it represents differences of differences.

---

# Using zero crossings for edge detection

## The Marr-Hildreth Edge Detector: Principle and Implementation

The edge-detection methods previously discussed rely on simple filtering operations using fixed-size convolution kernels. While computationally efficient, these basic methods do not account for edge characteristics or image noise.

The Marr-Hildreth method, introduced by Marr and Hildreth (1980), is one of the earliest attempts to embed a deeper understanding of visual perception into computational edge detection. The two central insights motivating their approach are:

1. **Scale Matters**: Intensity transitions are not independent of scale. Therefore, edge detection requires analyzing the image at different resolutions using filters of varying size.
2. **Zero-Crossings as Edges**: A sudden change in intensity manifests as a peak or trough in the first derivative, or equivalently, as a **zero-crossing** in the second derivative.

### Desired Properties of an Edge Detector

Marr and Hildreth argued that an effective edge-detection operator should:

-   Be a **differential operator** that approximates the first or second derivative at each image point.
-   Be **tunable in scale**, enabling the detection of both fine detail and broad, smooth transitions.

---

### The Laplacian of Gaussian (LoG) Operator

The Marr-Hildreth detector uses the **Laplacian of Gaussian (LoG)** operator, defined as:

$$
\text{LoG}(x, y) = ∇^2 G(x, y)
$$

where $∇^2$ is the Laplacian operator and $G(x, y)$ is a 2D Gaussian function:

$$
G(x, y) = e^{-\frac{x^2 + y^2}{2σ^2}}
$$

Applying the Laplacian to the Gaussian yields:

![alt text](/images/image83.png)

$$
∇^2 G(x, y) = \left( \frac{x^2 + y^2 - 2σ^2}{σ^4} \right) e^{-\frac{x^2 + y^2}{2σ^2}}
$$

This function is radially symmetric and isotropic.

### Visualization and Structure of the Laplacian of Gaussian (LoG) Operator

![alt text](/images/image84.png)

### (a) 3D Surface Plot of the LoG Function

This plot presents the continuous LoG function, denoted as $\nabla^2 G(x, y)$.

-   **"Mexican Hat" Shape**: A central positive peak surrounded by a negative ring, resembling a sombrero.
-   **Radial Zero-Crossings**: Occur at points where $x^2 + y^2 = 2\sigma^2$, forming a circle of radius $\sqrt{2}\sigma$.
-   **Decay to Zero**: The function approaches zero at distances far from the center.

### (b) Grayscale Image of the LoG

This is the LoG filter displayed as a grayscale image, where brightness corresponds to function amplitude.

**Interpretation:**

-   **White Center**: Represents the positive peak.
-   **Dark Ring**: Denotes the surrounding negative trough.
-   **Gray Background**: Indicates areas where the LoG value is approximately zero.

---

### (c) 1D Cross-Section (Profile View)

This subplot shows a 1D slice through the center of the 3D LoG surface.

**Observations:**

-   **Positive Central Peak**: The maximum of the function.
-   **Symmetric Negative Troughs**: Represent adjacent regions of intensity decrease.
-   **Zero-Crossings**: Critical points where the function transitions from positive to negative. These define potential edge locations and occur approximately at $x = \pm \sqrt{2}\sigma$.

---

## (d) 5×5 Discrete LoG Kernel Approximation

This kernel is a sampled and quantized approximation of the continuous LoG function, used for convolution in digital image processing.

The negative of this kernel would be used in practice. This is specifically for image sharpening

### Example Kernel:

```
[  0   0   1   0   0 ]
[  0   1   2   1   0 ]
[  1   2 -16   2   1 ]
[  0   1   2   1   0 ]
[  0   0   1   0   0 ]
```

**Properties:**

-   **Central Positive Value (16)**: Represents the peak of the LoG.
-   **Surrounding Negative Values**: Capture the negative trough.
-   **Zero Sum**: The kernel coefficients should sum to zero to suppress constant regions:

### Sharpening Formula

Let $f$ be the original image and $\nabla^2 f$ the Laplacian-filtered image.

The standard sharpening operation is:

$$
g_{\text{sharp}} = f - k \cdot (\nabla^2 f)
$$

If using the LoG filter, the sharpening becomes:

$$
g_{\text{sharp}} = f - k \cdot (\nabla^2 G * f)
$$

This can be expressed as a convolution:

$$
g_{\text{sharp}} = (\delta - k \cdot \nabla^2 G) * f
$$

Where $\delta$ is the impulse kernel (identity).

Equivalently:

$$
g_{\text{sharp}} = f + k \cdot (-\nabla^2 G * f)
$$

This final form clarifies the importance of using the **negative LoG**: the sharpening effect is achieved by **adding** the detail signal extracted by the **negative Laplacian of Gaussian** back to the original image.

---

### Properties and Implementation of the LoG

-   The **Gaussian** component smooths the image, attenuating high-frequency noise.
-   The **Laplacian** detects areas of rapid intensity change (edges).
-   Because the Laplacian is **isotropic**, it responds equally to edges in all directions.
-   The LoG kernel has **zero mean**, ensuring a zero response in constant-intensity regions.

To approximate the LoG in practice:

1. Sample the continuous LoG expression to form a discrete kernel of appropriate size.
2. Alternatively, compute it by convolving a Gaussian-smoothed image with a discrete Laplacian kernel (e.g., a $3 \times 3$ mask).

---

### Marr-Hildreth Edge Detection Algorithm

The algorithm proceeds in three main steps:

1. **Gaussian Smoothing**  
   Convolve the input image $f(x, y)$ with a Gaussian kernel of size $n \times n$, sampled from the expression for $G(x, y)$.

2. **Apply the Laplacian Operator**  
   Convolve the smoothed image with a Laplacian kernel (e.g., a $3 \times 3$ mask) to compute the second derivative.

3. **Detect Zero-Crossings**  
   For each pixel $p$, examine its $3 \times 3$ neighborhood in the filtered image. A zero-crossing occurs at $p$ if the signs of two opposing neighbors differ **and** their absolute difference exceeds a predefined threshold.

The full process can be expressed as:

$$
g(x, y) = ∇^2 [G(x, y) * f(x, y)]
$$

Or, equivalently:

$$
g(x, y) = [∇^2 G(x, y)] * f(x, y)
$$

---

### Kernel Size Selection

To determine an appropriate kernel size for the Gaussian filter:

-   Use a kernel of size $L \times L$, where $L = \lceil 6σ \rceil$
-   Ensure $L$ is an odd integer for symmetry.
-   This ensures that the tail of the Gaussian (beyond $3σ$) is negligible.

Using a kernel smaller than this will **truncate** the LoG and compromise edge detection performance.

---

### Thresholding for Edge Confirmation

To avoid false detections due to minor variations or noise:

-   Apply a **threshold** to the difference between positive and negative values across a zero-crossing.
-   Only mark a pixel as an edge if this difference exceeds the threshold.

This step helps suppress insignificant or noisy zero-crossings.

---

### Zero-Crossing Detection Procedure

For each pixel $p$ in the LoG-filtered image, we apply a **two-part test** using a $3 \times 3$ neighborhood centered on $p$:

### 1. **Sign Change Condition**

We test for the presence of a **zero-crossing** based on the sign of $\nabla^2 G(x, y)$ (the Laplacian of Gaussian).

**Procedure:**

-   Consider the **four pairs** of opposite neighbors around $p$ in the $3 \times 3$ mask:
    -   **Horizontal**: Left and right
    -   **Vertical**: Top and bottom
    -   **Diagonal (/)**: Top-right and bottom-left
    -   **Diagonal (\\)**: Top-left and bottom-right

**Condition:**

-   A **sign change** is detected if, in any of the four pairs:
    -   One pixel has a **positive** LoG value and the other has a **negative** value.

This indicates that the Laplacian function crosses zero at or near pixel $p$.

---

### 2. **Threshold Condition**

The sign change condition alone is too sensitive and may detect minor fluctuations due to **noise**. To suppress such false positives, we introduce a **threshold test**.

**Procedure:**

-   For any pair where a sign change is found:
    -   Compute the **absolute difference** between the pixel values:
        $$
        |\nabla^2 G(x_1, y_1) - \nabla^2 G(x_2, y_2)|
        $$
-   Compare this value to a user-defined **threshold**.

**Condition:**

-   The difference must exceed a **threshold $T$**, i.e.,
    $$
    |\text{value}_1 - \text{value}_2| > T
    $$
-   Only then is the sign change considered **significant**.

---

### 3. **Final Decision**

A pixel $p$ is marked as a **zero-crossing pixel** (i.e., part of an edge) **if and only if** both of the following conditions are satisfied:

-   There is a **sign change** in at least one of the four opposite pixel pairs.
-   The **absolute difference** of the pair exceeds the threshold.

This two-part test improves the **robustness** of the Marr-Hildreth detector by ensuring that only **meaningful zero-crossings** (likely corresponding to edges) are preserved.

---

### Example

![alt text](/images/image85.png)

Applying the Marr-Hildreth edge detection method to a grayscale building image.

-   **(a):** Original building image.
-   **(b):** Result of applying **Steps 1 and 2** of the Marr-Hildreth algorithm.
-   **(c):** Zero-crossing result with a **zero threshold**.
-   **(d):** Zero-crossing result with a **positive threshold** (4% of maximum LoG value).

#### Parameters

-   **$s = 4$:** Standard deviation of the Gaussian used for smoothing. This value corresponds to approximately 0.5% of the short dimension of the image.
-   **$n = 25$:** Size of the LoG mask used to ensure it adequately captures the Gaussian profile (as required by the size condition).

#### (b) Output of Steps 1 and 2

-   The Laplacian of Gaussian (LoG) is applied using the given $s$ and $n$ values.
-   Gray tones in the image result from **intensity scaling** for visualization.
-   This output is the **input** for zero-crossing detection.

#### (c) Zero-Crossings with Zero Threshold

-   **Threshold:** 0
-   **Method:** A $3 \times 3$ neighborhood-based approach is used to identify zero-crossings.
-   **Result:** Many **closed-loop edges** appear, a phenomenon known as the **"spaghetti effect"**.
    -   These loops often do **not correspond to real object boundaries**.
    -   This is a **major limitation** of using a zero threshold.

#### (d) Zero-Crossings with Threshold = 4% of Maximum LoG Value

-   **Threshold:** $0.04 \times \max(\text{LoG})$
-   **Result:**

    -   Major structural edges are preserved (e.g., outlines of windows, walls).
    -   **Irrelevant features** (e.g., bricks and tiles) are effectively **filtered out**.
    -   This improved performance is **difficult to achieve** with gradient-based edge detectors.

-   Another important consequence of using zero crossings for edge detection is that the resulting edges are
    1 pixel thick - **1-pixel-thick edges** simplify subsequent steps such as **edge linking** and **region segmentation**.

---

#### Huertas & Medioni's Subpixel Method

If the accuracy of the zero-crossing locations found using the Marr-Hildreth edge-detection method is inadequate in a particular application, then a technique proposed by Huertas and Medioni for finding zero crossings with subpixel accuracy can be employed.

-   Fits a smooth curve to better pinpoint the edge location
-   Best for applications needing very precise edge detection, like medical imaging

---

### Approximate the **Laplacian of Gaussian (LoG)** function by a **Difference of Gaussians (DoG)**:

$$
D(x, y) = \frac{1}{2\pi \sigma_1^2} e^{-\frac{x^2 + y^2}{2\sigma_1^2}}
\;-\;
\frac{1}{2\pi \sigma_2^2} e^{-\frac{x^2 + y^2}{2\sigma_2^2}}
$$

where $\sigma_2 > \sigma_1$.

---

## Relationship to Human Vision

Experimental results suggest that certain *channels* in the human vision system are selective for both **orientation** and **frequency**. These can be modeled using above equation with a **ratio of standard deviations** of:

- $1.75:1$ — matches physiological observations.
- $1.6:1$ — provides a closer *engineering approximation* to the LoG function (Marr and Hildreth, 1980).

---

## Matching Zero Crossings

To ensure that the LoG and DoG have the **same zero crossings**, the value of $\sigma$ for the LoG must satisfy:

$$
\sigma^2 =
\frac{\sigma_1^2 \sigma_2^2}{\sigma_2^2 - \sigma_1^2}
\ln\left(\frac{\sigma_2}{\sigma_1}\right)
$$

Although their **zero crossings** match when this equation is satisfied, their **amplitude scales** will differ. To make them comparable, both functions are scaled so that they have the same value at the origin.

---

## The Canny Edge Detector

The **Canny edge detector** (Canny, 1986) is a multi-stage algorithm designed to produce optimal edge detection results based on three primary objectives:

1. **Low error rate** — Detect all true edges and avoid spurious responses.
2. **Good localization** — Edge points should be as close as possible to the true edge position.
3. **Single response per edge** — Only one pixel should be marked for each true edge point.

Canny expressed these criteria mathematically and derived an optimal detector for **1-D step edges** corrupted by additive white Gaussian noise. The result showed that a good approximation is obtained by the **first derivative of a Gaussian**:

$$
\frac{d}{dx} \left( e^{-\frac{x^2}{2\sigma^2}} \right)
= -\frac{x}{\sigma^2} e^{-\frac{x^2}{2\sigma^2}}
$$

This function performs within approximately **20%** of the optimal numerical solution, a difference generally imperceptible in practical applications.

---

### Extension to 2-D

For 2-D images, the process is applied in the **direction of the edge normal**. Since this direction is not known in advance, the method approximates it by:

1. **Smoothing** the image with a 2-D Gaussian function:
   $$
   G(x, y) = e^{-\frac{x^2 + y^2}{2\sigma^2}}   
   $$
2. **Convolving** the image with $G(x, y)$:
   $$
   f_s(x, y) = f(x, y) * G(x, y)   
   $$
3. **Computing the gradient magnitude and angle**:
   $$
   M_s(x, y) = \sqrt{g_x^2(x, y) + g_y^2(x, y)}   
   $$
   $$
   \alpha(x, y) = \tan^{-1} \left( \frac{g_y(x, y)}{g_x(x, y)} \right)   
   $$
   where $g_x = \frac{\partial f_s}{\partial x}$ and $g_y = \frac{\partial f_s}{\partial y}$.

---

## Non-Maxima Suppression

The gradient magnitude image $M_s(x, y)$ typically contains **thick ridges**. Non-maxima suppression is applied to thin these ridges:

1. **Quantize** the gradient direction $\alpha(x, y)$ into four categories:
   - Horizontal ($0^\circ$)
   - $+45^\circ$
   - Vertical ($90^\circ$)
   - $-45^\circ$
2. For each pixel:
   - Compare $M_s(x, y)$ to its two neighbors along the quantized direction.
   - If $M_s(x, y)$ is not greater than both neighbors, set it to zero.

This produces a thinned **non-maxima suppressed image** $g_N(x, y)$.

---

### Double Thresholding and Edge Tracking

To reduce false edges:

1. Apply **two thresholds**:
   - **High threshold** $T_H$
   - **Low threshold** $T_L$
2. Classify:
   - Pixels with $M_s(x, y) \ge T_H$ → **strong edges**
   - Pixels with $T_L \le M_s(x, y) < T_H$ → **weak edges**
3. **Edge linking**:
   - Include weak edges that are **8-connected** to any strong edge.
   - Discard all other weak edges.

Experimental evidence suggests $T_H : T_L$ ratios in the range **2:1** to **3:1**.

---

### Summary of the Canny Algorithm

1. Smooth the input image with a Gaussian filter.
2. Compute gradient magnitude and direction.
3. Apply non-maxima suppression.
4. Perform double thresholding and edge linking.
5. (Optional) Apply final edge thinning for single-pixel-wide edges.

---

- The Gaussian kernel $G(x, y)$ is **separable** into the product of two 1-D Gaussians.  
  This allows Step 1 to be implemented using **two 1-D convolutions** instead of a single 2-D convolution.
- Gradient computations can also be implemented with **1-D derivative kernels**, further improving efficiency.

---

