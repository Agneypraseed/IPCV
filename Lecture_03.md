## Image Histogram

An **image histogram** is a fundamental tool used for analyzing and manipulating digital images. It provides a graphical representation of the distribution of pixel intensity values.

---

### Definitions

-   **Image Size (M, N)**:  
    An image is a grid of pixels with `M` rows (height) and `N` columns (width).

    -   Total number of pixels: `M × N`

-   **Grey Values (`r_k`, `L`)**:  
    Brightness is quantized into discrete levels.

    -   `L`: Number of grey levels (e.g., `L = 256` for 8-bit images)
    -   `r_k`: A grey level, where `r_0 = 0` (black), `r_{L-1} = 255` (white)

-   **Pixel Count (`n_k`)**:  
    The number of pixels in the image having grey level `r_k`.

    -   For example, `n_0` is the count of pixels with intensity 0.

-   **Unnormalized Histogram `h(r_k)`**:  
    The raw count of pixels for each grey level.  
    `h(r_k) = n_k`

    -   Depends on image size
    -   Not suitable for comparison across different image sizes

-   **Histogram Bins**:  
    Each grey level `r_k` is treated as a bin. The histogram distributes pixel values into these `L` bins.

-   **Normalized Histogram `p(r_k)`**:  
    The probability distribution of grey levels across the image:  
    `p(r_k) = n_k / (M × N)`
    -   Values lie in `[0, 1]`
    -   Independent of image size
    -   The sum of all `p(r_k)` is 1

---

### Numerical Example

A 4×4 grayscale image with \( L = 8 \) grey levels:

```
[ 2, 3, 3, 4 ]
[ 1, 2, 4, 5 ]
[ 0, 1, 5, 6 ]
[ 1, 2, 6, 7 ]
```

-   Rows: `M = 4`
-   Columns: `N = 4`
-   Total pixels: `M × N = 16`
-   Grey levels: `r_k ∈ {0, 1, ..., 7}`

#### Step 2: Unnormalized Histogram

| \( r_k \) | \( n_k \) |
| --------- | --------- |
| 0         | 1         |
| 1         | 3         |
| 2         | 3         |
| 3         | 2         |
| 4         | 2         |
| 5         | 2         |
| 6         | 2         |
| 7         | 1         |
| **Total** | **16**    |

#### Step 3: Normalized Histogram `p(r_k) = n_k / 16`

| Grey Value `r_k` | Count `n_k` | Normalized `p(r_k)` |
| ---------------- | ----------- | ------------------- |
| 0                | 1           | `1/16 = 0.0625`     |
| 1                | 3           | `3/16 = 0.1875`     |
| 2                | 3           | `3/16 = 0.1875`     |
| 3                | 2           | `2/16 = 0.125`      |
| 4                | 2           | `2/16 = 0.125`      |
| 5                | 2           | `2/16 = 0.125`      |
| 6                | 2           | `2/16 = 0.125`      |
| 7                | 1           | `1/16 = 0.0625`     |
| **Total**        | **16**      | **1.0**             |

---

## Histogram Equalization

The goal of histogram equalization is to find a transformation function s = T(r) that takes an input image with any kind of histogram and produces an output image whose histogram is perfectly flat (uniform).

We treat intensity values `r ∈ [0, L−1]` as continuous variables, and define a transformation:

```
s = T(r)
```

---

### Key Requirement

-   `T(r)` must be **monotonically increasing** to preserve intensity ordering.
-   **Strict monotonicity** ensures a one-to-one mapping from input to output values.

---

### Transformation Function

$$
s = T(r) = (L − 1) ∫₀ʳ p_r(α) dα
$$

Where:

-   `p_r(r)` is the **probability density function (PDF)** of the original image (i.e., the normalized histogram).
-   The integral accumulates the probability from 0 to `r`, which is the **cumulative distribution function (CDF)**.
-   The output `s` lies within `[0, L−1]`.

![alt text](/images/image15.png)

---

### Result

After applying the transformation `T(r)`, the new intensity distribution `p_s(s)` becomes uniform:

```
p_s(s) = 1    for 0 ≤ s ≤ L − 1
```

This implies that the histogram of the output image is flattened (equalized), leading to enhanced contrast.

p_r(r) is the histogram (or PDF) of the original image with intensities r.

p_s(s) is the histogram (or PDF) of the new, transformed image with intensities s.

---

### Inverse Transformation

In some formulations, we apply the inverse transformation:

```
r = T⁻¹(s)    for 0 ≤ s ≤ L − 1
```

To ensure this works properly, we modify the conditions:

-   `T(r)` must be a **strictly monotonic increasing function** in the interval `0 ≤ r ≤ L − 1`

---

### Monotonic vs. Strictly Monotonic Transformations

-   `T(r)` is **monotonic increasing**, meaning:
    ```
    T(r₂) ≥ T(r₁) for r₂ > r₁
    ```
-   However, multiple input values may map to a single output value (i.e., many-to-one mapping).
-   This is acceptable for forward mapping (`r → s`) but problematic for **inverse mapping**, as not all output values `s` can uniquely determine their input `r`.

By requiring that `T(r)` be **strictly monotonic**:

-   Strict monotonicity:
    ```
    T(r₂) > T(r₁) for r₂ > r₁
    ```
-   Guarantees a **one-to-one mapping** both forward and backward (`r ↔ s`).

---

Let:

-   `r` and `s` be random variables representing input and output intensity values.
-   `p_r(r)` and `p_s(s)` be the **probability density functions (PDFs)** for `r` and `s`.

If:

-   `T(r)` is continuous and differentiable,
-   and we know `p_r(r)` and `T(r)`,

Then the output PDF `p_s(s)` is given by:

$$
p_s(s) = p_r(r) * (dr/ds)
$$

---

Deriving `p_s(s)` from `p_r(r)`

Using Leibniz's Rule:

```
ds/dr = dT(r)/dr = (L − 1) * p_r(r)
```

Therefore:

```
dr/ds = 1 / [(L − 1) * p_r(r)]
```

```
Substitute into
p_s(s) = p_r(r) * (dr/ds)
```

![alt text](/images/image16.png)

---

![alt text](/images/image17.png)

---

## Spatial Filtering

Spatial filtering involves modifying an image by applying a function to the neighborhood of each pixel. The process replaces each pixel’s value with a new value computed from its surroundings.

The term _filter_ originates from frequency domain processing, where filtering refers to the process of **passing, modifying, or suppressing** specific frequency components within an image. For instance:

-   A **lowpass filter** allows low-frequency components to pass while attenuating high frequencies.
-   The visual result of applying a lowpass filter is image **smoothing**, commonly perceived as **blurring**.

Although frequency domain techniques are a powerful tool for image processing, similar effects—such as smoothing—can also be achieved directly in the **spatial domain** using spatial filters.

The key idea is to apply a kernel (or mask) over the image, processing each pixel in context with its neighborhood to achieve the desired transformation—such as enhancement, noise reduction, or edge detection.

---

## Linear Spatial Filtering

A **linear spatial filter** operates by performing a **sum-of-products** between an image `f(x, y)` and a **filter kernel** `w(s, t)`. The kernel—also referred to as a _mask_, _template_, or _window_—is a small matrix that defines both:

-   The **neighborhood** over which the filtering is applied, and
-   The **coefficients** that determine the nature of the filtering operation (e.g., smoothing, sharpening).

---

### Basic Operation

The mechanics of linear spatial filtering are illustrated using a `3 × 3` kernel. For any pixel at location `(x, y)` in the input image, the filtered response `g(x, y)` is given by the **sum of products** between the kernel coefficients and the corresponding pixels in the neighborhood:

```
g(x, y) =
    w(-1, -1)·f(x-1, y-1) + w(-1, 0)·f(x-1, y) + w(-1, 1)·f(x-1, y+1) +
    w( 0, -1)·f(x,   y-1) + w( 0, 0)·f(x,   y) + w( 0, 1)·f(x,   y+1) +
    w( 1, -1)·f(x+1, y-1) + w( 1, 0)·f(x+1, y) + w( 1, 1)·f(x+1, y+1)
```

This represents a typical convolution-like filtering operation.

---

### General Form

To generalize for a kernel of size `m × n`, where `m = 2a + 1` and `n = 2b + 1` (ensuring odd dimensions), the linear filtering operation is expressed as:

$$
g(x, y) = Σ_{s=-a}^{a} Σ_{t=-b}^{b} w(s, t) · f(x + s, y + t)
$$

This defines the **linear filtering** process over the entire image.

-   `f(x + s, y + t)` are the neighboring pixels of the input image,
-   `w(s, t)` are the kernel weights, and
-   `g(x, y)` is the corresponding output pixel in the filtered image.

As the coordinates `(x, y)` are varied across the image, the kernel slides across each pixel location, computing a new filtered value at every position.

The use of **odd-sized kernels** ensures a well-defined center.

---


