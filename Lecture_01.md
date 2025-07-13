**Discretization** refers to the process of transforming a continuous domain into a discrete one by representing it with a finite set of elements.  
In the context of images, this typically involves converting a continuous spatial domain `Ω ⊂ ℝ²` (where an image is defined as a continuous function over space) into a finite grid of samples (**pixels**).

---

### Sampling (Spatial Discretization)

Sampling refers to discretizing the continuous spatial domain `Ω`.  
We choose specific points `(xᵢ, yⱼ)`, turning `f(x, y)` into a grid of values:

```
fᵢⱼ = f(xᵢ, yⱼ)
```

Example:

-   Cameraman image at resolution 256×256 → 256 samples along each axis → 65,536 pixels.
-   Reducing to 64×64 or 32×32 leads to fewer pixels and loss of detail.

---

### Tonal Domain and Quantization (Intensity Discretization)

The **tonal domain** (also known as the intensity domain or range) refers to the set of all possible intensity or color values a pixel in an image can take.

**Quantization** is the process of discretizing the range of grey levels, mapping the continuous intensity `f(x, y)` to a finite set of values, typically integers.

Common case:

-   **8-bit quantization** → values in `{ 0, 1, ..., 255 }`
    -   `0`: black, `255`: white

**Binary images**:

-   Co-domain: `{ 0, 1 }`, often rescaled to `{ 0, 255 }`
-   Result from thresholding operations, useful in document scanning, object detection, etc.

---

### Digital Image Formation Summary

#### To convert a continuous image into digital format:

-   Use **Sampling** (for space)
-   Use **Quantization** (for intensity)

**Example pipeline:**

-   **Original grayscale image** → 256×256, 8-bit depth
-   **Downsampling**:
    -   Sample to 64×64 → coarse detail
    -   Sample to 32×32 → fine detail lost; image appears blocky
-   **Quantization/Binarization**:
    -   Apply threshold (e.g., 128)
        -   Pixel ≥ 128 → white (255)
        -   Pixel < 128 → black (0)
-   **Result**: binary image highlighting structure or edges

![alt text](/images/image-1.png)

![alt text](/images/samplingandquantization.png)

---

### Color Images

A color image is defined as a function:

```
f(x, y): Ω → ℝ³
```

#### RGB Color Model

Standard model for display systems (monitors, cameras).  
Each channel is typically 8 bits → values in `[0, 255]` per channel.

Examples:

-   `[255, 0, 0]` → red
-   `[0, 255, 0]` → green
-   `[255, 255, 255]` → white

---

### HSV Color Space

HSV stands for:

-   **Hue (H):** Color type (e.g., red, green)
-   **Saturation (S):** Color purity/intensity
-   **Value (V):** Brightness

**Advantages**:  
Perceptually uniform – better aligns with how humans perceive color, making it useful in:

-   Object tracking
-   Image segmentation
-   Thresholding in color space

| RGB (R,G,B)     | HSV (H,S,V)               |
| --------------- | ------------------------- |
| [255, 0, 0]     | [0°, 1, 1] (pure red)     |
| [0, 255, 0]     | [120°, 1, 1] (pure green) |
| [0, 0, 255]     | [240°, 1, 1] (pure blue)  |
| [128, 128, 128] | [0°, 0, 0.5] (grey)       |

---

### Spatial Domain Processing

The **spatial domain** refers to the image plane itself.  
Image processing methods in this category are based on **direct manipulation of pixels**.

There are two principal categories of spatial domain processing:

1. **Intensity Transformations**  
   Operate on **individual pixels** of an image.  
   Used for tasks such as:

    - Contrast manipulation
    - Image thresholding

2. **Spatial Filtering**  
   Operates on the **neighborhood of every pixel**.  
   Common examples:
    - Image smoothing
    - Image sharpening

> Spatial domain techniques operate directly on the pixel values of an image,  
> in contrast to **frequency domain techniques**, which operate on the **Fourier transform** of the image.

---

**General Formulation of Intensity Transformations:**

```
g(x, y) = T(f(x, y))
```

Let `f(x, y)` be the input image and `g(x, y)` the output image.  
Let `T` be an operator defined over a neighborhood of the point `(x, y)`.

The operator `T` can be:

-   Applied to the pixels of a **single image** or
-   Applied to a **set of images**, such as computing the element-wise sum across multiple images (e.g., for noise reduction).

---

### Example: Local Averaging

Suppose the neighborhood is a square of size `3 × 3`, and the operator `T` is defined as:

> **Compute the average intensity of all pixels in the neighborhood.**

At an arbitrary pixel location, say `(100, 150)`, the result at that location in the output image `g(100, 150)` is computed as:

```
g(100, 150) = (1/9) × [sum of f(100, 150) and its 8 neighbors]
```

The center of the neighborhood is then moved to the next pixel, and the operation is repeated to compute the full output image `g(x, y)`.

---

### Special Case: 1×1 Neighborhood

The smallest possible neighborhood is `1 × 1`. In this case, the output `g(x, y)` depends **only on the value** of the input image `f(x, y)` at the same location.  
The transformation becomes a **pointwise intensity transformation**, expressed as:

```
s = T(r)
```

Where:

-   `r` is the intensity of the input image `f(x, y)`
-   `s` is the intensity of the output image `g(x, y)`

This transformation maps each intensity value `r` in the input to a new intensity value `s` in the output.

### Point Operations (Intensity Transformations)

These are also known as **point processing operations**, since each output pixel value depends only on the corresponding input pixel value (i.e., a 1×1 neighborhood).

If `T` depends **only on the pixel itself**, it is called a **point operation**.

---

### Example: Contrast Stretching and Thresholding

![alt text](/images/image5.png)
If `T(r)` has the shape shown in a typical contrast-stretching curve (Fig (a)):

-   Intensities **below** a threshold `k` are **darkened**
-   Intensities **above** `k` are **brightened**

This enhances image contrast by expanding the range of intensity values.

In the **limiting case** (as Fig(b)), `T(r)` becomes a **binary threshold function**, producing a two-level (black-and-white) image.

Such a transformation is known as a **thresholding function**.

---

**Neighborhood operations** apply a function `T` over a local region of the image.

-   **Point operations** apply `T` directly to each pixel without considering neighbors.

<hr>

### Image Enhancement

**Definition:**  
Enhancement is the process of manipulating an image so that the result is more suitable than the original for a specific application.

> _Example:_  
> A method effective for enhancing **X-ray images** may not be ideal for enhancing **infrared images**. The choice of enhancement technique depends on the nature of the data and the intended use.

---

### Intensity Transformations

Intensity transformations are **pointwise operations**, meaning they apply a transformation function to individual pixels, independent of their neighbors.

We use the following notation:

-   `r`: Original intensity value of a pixel (input)
-   `s`: Transformed intensity value (output)

Both `r` and `s` range from `0` to `L − 1`, where `L` is the number of intensity levels.  
For an 8-bit grayscale image, `L = 256` → values in `[0, 255]`.

---

### Transformation Function: `T(r)`

Let `T(r)` be the transformation function applied to each pixel:

```
s = T(r)
```

Where `T` defines how the input value `r` is mapped to output `s`.

---

### Implementation via Lookup Tables (LUT)

Rather than computing `T(r)` for each pixel in real-time (which is computationally inefficient), intensity transformations are commonly implemented using **lookup tables** (LUTs).

#### Steps:

1. **Precompute** the values of `T(r)` for all `r` in the range `[0, 255]`.
2. **Store** these values in a lookup table (an array of 256 entries).
3. For each pixel in the image:
    - Retrieve the original value `r`.
    - Replace it with the transformed value `T(r)` using the LUT.

> This method is both **fast** and **efficient**, especially for large images or real-time applications.

---

### Image Negatives (Negative Transformation)

**Definition:**  
The **negative transformation** inverts the intensity values of an image by mapping each pixel's brightness to its opposite within the intensity range.

The transformation function is:

```
T(r) = L − 1 − r
```

Where:

-   `r` is the original intensity value (input pixel),
-   `T(r)` is the transformed intensity (output),
-   `L` is the total number of intensity levels (e.g., 256 for 8-bit images).

---

### Effect:

-   **Black becomes white**, and **white becomes black**.
-   Mid-gray values remain relatively unchanged.
-   The dynamic range is reversed: the brightest parts become the darkest, and vice versa.

---

### Applications:

-   **Enhancing dark details** in images with poor lighting.
-   Commonly used in:

    -   **Medical imaging**, such as X-rays or mammograms,
    -   **Astronomical images** to highlight faint stars or nebulae.

    ![alt text](/images/negative_function.png)

---

### Example:

Original intensity value: `r = 50`  
If `L = 256`, then:

```
T(50) = 255 − 50 = 205
```

The pixel becomes much brighter in the negative image.

---

### Logarithmic Transformation

**Definition:**  
The **logarithmic transformation** enhances the lower intensity values in an image, thereby expanding dark regions and compressing bright ones. It is mathematically defined as:

```
T(r) = c · log(1 + r)
```

Where:

-   `r` is the input pixel intensity (0 ≤ r ≤ L − 1),
-   `T(r)` is the output transformed intensity,
-   `c` is a positive constant that scales the result,
-   `log` denotes the natural logarithm (or logarithm to base 10, depending on implementation).

---

### Purpose and Effect:

-   **Expands** low-intensity values: useful for revealing detail in dark regions.
-   **Compresses** high-intensity values: bright areas are mapped to a smaller range.
-   Particularly effective when the image’s histogram is skewed toward lower intensities.

---

### Mapping Characteristics:

-   Input levels in the range `[0, L/4]` are stretched across a wider output range, say `[0, 3L/4]`.
-   Conversely, input levels in `[L/4, L − 1]` are compressed into a narrower output range.

This means the transformation emphasizes differences in dark tones while reducing contrast in bright regions.

---

### Constant `c`:

-   Chosen such that the output remains within the allowable intensity range `[0, L − 1]`.
-   Typically computed as:

```
c = (L − 1) / log(1 + R_max)
```

Where `R_max` is the maximum possible input intensity value (e.g., 255 for 8-bit images).

---

### Inverse Transformation (Exponential):

To achieve the opposite effect (i.e., compressing dark values and expanding bright values), an **exponential transformation** is used:

```
T(r) = c · (e^r − 1)
```

This is useful when bright regions contain the most important information.

---

The logarithmic function is valuable in image processing because it **compresses the dynamic range** of pixel intensity values.

---

_Dynamic Range_

Dynamic range refers to the ratio between the **maximum** and **minimum** values in a dataset.  
In the context of images, it is the range between the **darkest pixel** and the **brightest pixel**.

Example:

-   An image of a **Fourier spectrum** might contain intensity values ranging from 0 to 1,000,000 or more.
-   However, most displays (e.g., monitors) and the human visual system can only handle **8-bit** intensity levels, i.e., **256 discrete values** ranging from 0 to 255.

If you use a simple **linear mapping** to display such high-dynamic-range images:

-   The **maximum value** (e.g., 1,000,000) is mapped to **255** (white).
-   A moderately high value like 100,000 gets mapped to **~25**.
-   A value of 1,000 is mapped to **~0** (black).

This leads to a **loss of detail** in most of the image, as many values collapse into the darkest levels (black).

The logarithmic transformation scales intensity values according to:

```
T(r) = c · log(1 + r)
```

This transformation maps large input values into a compressed output range:

-   `log(1,000,000) ≈ 6`
-   `log(1,000) ≈ 3`
-   `log(10) ≈ 1`

So, instead of mapping from 0–1,000,000 linearly into 0–255, the log function reduces the **effective range**, preserving **relative differences** even in high-intensity regions.

### Effect:

-   Maintains **visual detail** in bright regions.
-   Makes the output image more **interpretable** and displayable.
-   Especially useful in **scientific imaging**, e.g., Fourier spectra, astronomy, or medical imaging.

### The Fourier Spectrum Example:

Without log transform: You see a few bright white dots against a completely black background. All the important but less intense structural detail is invisible.

With log transform: The intensely bright values are "toned down" significantly, while the fainter (but still important) values are brightened relative to them. Suddenly, you can see all the intricate patterns of the spectrum that were previously hidden.

![alt text](/images/log_transformation.png)

---

### Power-Law (Gamma) Transformations

Power-law transformations are a class of nonlinear intensity transformations commonly used for image enhancement and display correction. They are defined by the following general formula:

\[
T(r) = c \cdot r^{\gamma}
\]

Where:
- \( r \): the input pixel intensity (normalized between 0 and 1 or from 0 to \( L-1 \))
- \( s = T(r) \): the output (transformed) intensity
- \( c \): a constant scaling factor
- \( \gamma \): the gamma value (power exponent)

---

### Interpretation of Gamma (\( \gamma \)):

- **\( \gamma < 1 \)**  
  - Expands **dark** tones.
  - Compresses **bright** tones.
  - Enhances details in dark regions.

- **\( \gamma > 1 \)**  
  - Expands **bright** tones.
  - Compresses **dark** tones.
  - Useful when bright regions need enhancement.

This behavior is similar in principle to logarithmic transformations, but with more flexibility depending on the value of \( \gamma \).

---

### Special Use Case: Gamma Correction

Power-law transformations are widely used for **gamma correction** in imaging systems:

- **Display devices** (monitors, TVs, projectors) have a nonlinear relationship between the input signal and displayed brightness.
- Gamma correction adjusts the image to **compensate** for this nonlinearity, ensuring that the perceived brightness is consistent with the original scene.

---

### Alternative Form:

Sometimes, the transformation is written with an added offset:

\[
s = c \cdot (r + \varepsilon)^\gamma
\]

- \( \varepsilon \): a small offset added to account for display or sensor calibration (used to avoid a zero output for zero input).
- In most practical applications, this offset is negligible and often ignored.

---

### Visual Effect:

- **Fractional gamma values** (\( \gamma < 1 \)):  
  Map a **narrow range of dark input levels** into a **wider output range** (contrast stretching in shadows).

- **Gamma values \( \gamma > 1 \)**:  
  Stretch **bright areas** and compress **dark areas**.

---

### Summary:

| Gamma Value | Effect on Image                | Use Case                          |
|-------------|-------------------------------|-----------------------------------|
| \( \gamma < 1 \) | Dark regions become brighter     | Reveal shadow details              |
| \( \gamma = 1 \) | No change (linear)               | No correction needed               |
| \( \gamma > 1 \) | Bright regions become brighter   | Enhance highlights, display tuning |

Power-law transformations offer flexibility for **dynamic range manipulation**, **perceptual enhancement**, and **device-specific correction**.



### Extended Form

In some contexts, the transformation may be expressed as:

\[
s = c \cdot (r + \epsilon)^{\gamma}
\]

Where:

- \( \epsilon \) is a small **offset** to handle cases where \( r = 0 \),
- This form is occasionally used in hardware or calibration models.

**Note:** In most image processing applications, this offset is negligible and is omitted for simplicity.

---

### Comparison with Logarithmic Transformation

- Like the **log transformation**, power-law transformations with \( \gamma < 1 \) **expand dark intensity values**.
- Conversely, power-law with \( \gamma > 1 \) compresses the dark tones and expands the bright regions.

---

### Summary

| Gamma Value | Effect                          | Use Case                               |
|-------------|----------------------------------|----------------------------------------|
| \( \gamma < 1 \) | Brightens dark areas              | Revealing shadow details in images     |
| \( \gamma = 1 \) | Linear mapping (no change)       | Ideal behavior for calibrated systems  |
| \( \gamma > 1 \) | Darkens the image, expands highlights | Suppressing low-intensity noise        |

Power-law transformations provide flexible, non-linear contrast adjustments and are essential for **correct visual representation** of image data across devices.

