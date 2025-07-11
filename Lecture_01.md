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
