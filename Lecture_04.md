## Spatial vs Frequency Domain: The Role of the Fourier Transform

The connection between spatial- and frequency-domain processing is established by the **Fourier transform**.

-   To move from the **spatial domain** to the **frequency domain**, we use the **Fourier transform**.
-   To return, we use the **inverse Fourier transform**.

---

1. **Convolution in the spatial domain is equivalent to multiplication in the frequency domain**, and vice versa.
2. **An impulse of strength `A` in the spatial domain is a constant value `A` in the frequency domain**, and vice versa.

```
    δ(x, y) = {
                      A   if x = x₀ and y = y₀
                      0   otherwise
    }

    The 2D **Fourier Transform** of a shifted impulse `δ(x − x₀, y − y₀)` results in a complex exponential. However, when the impulse is **centered at the origin** `(x₀, y₀) = (0, 0)`, the transform is:

                        F{δ(x, y)} = A

```

![alt text](/images/image27.png)
![alt text](/images/image28.png)

**Interpretation**:  
In the **frequency domain**, all frequencies are present **equally** with a **constant amplitude** `A`.

**Duality Summary**:

-   A single impulse in space ⟷ a constant signal in frequency
    ![alt text](/images/image30.png)

-   A constant signal in space ⟷ a single impulse in frequency
    ![alt text](/images/image29.png)

---

### Frequency Representation of Images

Any function (including an image) that satisfies certain mild conditions can be expressed as a **sum of sinusoids** with different frequencies and amplitudes.

-   **Low-frequency components** describe regions with slow intensity changes (e.g., smooth walls).
-   **High-frequency components** correspond to sharp transitions (e.g., edges).

Thus, modifying these frequency components changes the **appearance of the image**:

-   Reducing high-frequency components leads to **blurring**.
-   Enhancing high frequencies increases **sharpness**.

---

### Linear Filtering in Frequency Domain

In the **spatial domain**, linear filtering is done via **convolution**.

In the **frequency domain**, it is done via **multiplication with a transfer function**.

---

### Example: 1-D Frequency Domain Filtering

Suppose we want to remove all frequency components **above a cutoff `u₀`** in a 1-D function (like an intensity scan line). This is achieved using a **lowpass filter**.

-   The filter **passes** frequencies below `u₀` and **eliminates** those above it.
-   The corresponding frequency-domain function is called a **filter transfer function**.

#### Ideal Lowpass Filter

![alt text](/images/image31.png)

An ideal lowpass filter has an **instantaneous transition** between passed and blocked frequencies.

-   While not physically realizable, it is valuable for theoretical analysis.
-   It causes **ringing artifacts** when implemented digitally.

---

### Filtering Procedure in Frequency Domain

1. Compute the **Fourier transform** of the signal.
2. Multiply the result by the **filter transfer function** to suppress frequencies above `u₀`.
3. Take the **inverse Fourier transform** of the result.
4. The outcome is a **blurred version** of the original signal.

---

### Spatial Domain Equivalent

Due to the spatial-frequency duality:

-   The same effect can be achieved by **convolution** in the spatial domain.
-   The **equivalent spatial filter kernel** is the **inverse Fourier transform** of the frequency-domain transfer function.

#### Example:

-   If the filter transfer function is a rectangular function (ideal lowpass),
-   Then the corresponding spatial filter kernel will show **ringing**.

![alt text](/images/image32.png)

Here the kernel exhibits oscillations due to the abrupt frequency cutoff.

---

## Approaches to Constructing Spatial Filters

There are **three fundamental approaches** for constructing spatial filters in image processing:

---

### 🔹 1. Mathematical Property-Based Filters

This approach relies on **mathematical operations** to define filter behavior:

-   **Averaging filters** compute the mean of neighboring pixel values.  
    → These tend to **blur** the image.  
    → Mathematically, this is analogous to **integration** over the neighborhood.

-   **Derivative filters** compute local gradients (changes in intensity).  
    → These tend to **sharpen** the image.  
    → Mathematically, this is analogous to **differentiation**.

This method provides a direct connection between filter behavior and basic mathematical operations.

---

### 🔹 2. Sampling from 2D Spatial Functions

In this approach, a **2D spatial function** is selected for its desirable characteristics, and a spatial filter is constructed by **sampling** from it.

-   **Example**: Sampling from a **Gaussian function** to create a **weighted-average (lowpass)** filter.

-   Some of these spatial functions are derived from the **inverse Fourier transform** of frequency-domain filters.

This method allows for building filters with **known shape profiles**, such as smooth, symmetric, or isotropic characteristics.

---

### 🔹 3. Frequency Response-Based Filter Design

This approach begins by specifying a **desired frequency response**, then designs a spatial filter to approximate that response.

-   Typically done through **digital filter design techniques**.
-   A **1D filter** with the target frequency response is designed (often with software tools).
-   The 1D filter values are then:

    -   Expressed as a **vector `v`**, and a **2D separable kernel** is obtained using:

        ```text
        w = v · vᵀ
        ```

    -   Or, **rotated about its center** to form a **circularly symmetric 2D kernel**.

This method ensures that the spatial filter adheres closely to frequency-domain specifications, providing precise control over how different frequencies are attenuated or preserved.

---

## Smoothing (Lowpass) Spatial Filters

**Smoothing**, also known as **averaging**, is a fundamental operation in image processing used to reduce sharp transitions in intensity. Since **random noise** typically manifests as high-frequency intensity fluctuations, smoothing is a natural tool for **noise reduction**.

Other applications include:

-   **Pre-processing** before image resampling (to reduce aliasing),
-   **Reducing irrelevant detail**, where "irrelevant" refers to structures smaller than the filter kernel,
-   **Smoothing false contours** caused by insufficient intensity levels,
-   **Enhancing images** when used with techniques like histogram equalization and unsharp masking.

---

### 🔹 Linear Smoothing Filters

**Linear spatial filtering** involves **convolution** of the image with a filter kernel. For smoothing, this convolution process effectively **blurs the image**. The **degree of blurring** is controlled by:

-   The **size** of the kernel
-   The **values of its coefficients**

Lowpass filters are foundational, as many other types—such as **highpass (sharpening)**, **bandpass**, and **bandreject filters**—are often **derived from lowpass filters**.

---

## Box Filter Kernels

The **box filter** is the simplest form of lowpass, separable kernel. Its coefficients all have equal values (typically `1`), forming a flat-top surface when visualized in 3D—hence the name "box."

-   A general `m × n` box filter consists of an array of `1`s:

    ```text
    Box Filter = (1 / (m·n)) × [matrix of all 1s]
    ```

-   The **normalization constant** is:

    ```text
    1 / (m·n)
    ```

    This ensures:

    1. The output value over a constant region remains equal to that region's intensity.
    2. The filter does **not introduce bias**—the total sum of pixel values in the image remains unchanged after filtering.

-   Since all rows and columns are identical, the **rank** of a box kernel is `1`, making it **separable**.

---

### 🧪 Example : Lowpass Filtering with Box Kernels

A test image of size `1024 × 1024` pixels was filtered using box kernels of size:

-   `m = 3`
-   `m = 11`
-   `m = 21`

![alt text](/images/image33.png)

#### Observations:

-   **For m = 3:**

    -   Slight overall blurring.
    -   Fine image details (e.g., thin lines and noise) were noticeably affected.
    -   A **thin gray border** appeared due to **zero-padding** at the boundaries.

-   **For m = 11:**

    -   Increased blurring across the image.
    -   The **dark border** due to padding became more pronounced.

-   **For m = 21:**
    -   **Significant loss of detail** across all image components.
    -   Smaller structures, such as the small square at the top-left and a character at the bottom-left, lost their shape.
    -   The **padding artifact** expanded, creating a visibly thicker dark border.

---

#### Note on Zero Padding

**Zero padding** extends image boundaries by adding rows and columns of zero (black) pixels. While it enables convolution at the edges, it also introduces **artifacts**, such as **dark borders**. These are particularly visible when averaging includes zero-valued pixels near the image boundary.

Alternative padding strategies can help eliminate such border artifacts.

---

## Limitations of Box Filters

Due to their computational simplicity, **box filters** are well-suited for rapid prototyping and **quick experimentation**. In many cases, they produce **visually acceptable smoothing** results, particularly when minimal processing time is desired. They are also beneficial when the goal is to **reduce the impact of smoothing on image edges**.

However, box filters exhibit several important limitations that make them **unsuitable for precision or perceptually accurate applications**.

---

### 🔹 Limitation 1: Poor Approximation of Lens Blur

In imaging systems, a **defocused lens** behaves as a **lowpass filter**. However, box filters fail to replicate the **blurring characteristics** observed in such optical scenarios.

> **Problem 3.33:**  
> A single point of light can be modeled by a digital image consisting of all `0`s, with a single `1` located at the position of the light point.  
> When viewed through a **defocused lens**, this point appears as a **fuzzy blob**, the size of which increases with the degree of defocus.
>
> Although it might seem intuitive to model this blur using a **box kernel**, such a filter performs poorly in replicating the smooth, radial falloff of light characteristic of real optical blur.
>
> A **Gaussian kernel** is a better approximation because its structure mimics the **circular symmetry and smooth decay** of a defocused point source.
>
> Thus:
>
> -   Box filters: Provide uniform weighting and sharp edges → **unnatural, blocky blur**
> -   Gaussian filters: Provide smooth, radially symmetric weighting → **natural, lens-like blur**

---

### 🔹 Limitation 2: Directional Bias

Box filters inherently favor **blurring along perpendicular directions**—horizontal and vertical—due to their **rectangular, separable structure**.

This directional preference can lead to **anisotropic blurring**, which is problematic in applications involving:

-   High-detail imagery
-   Strong geometric features (e.g., architectural images, technical drawings)

In such cases, the filter may:

-   Produce **unnatural smoothing patterns**
-   **Degrade key visual elements** (e.g., lines, edges, and corners)

The **axis-aligned nature of the box kernel** produces **non-uniform smoothing artifacts**.

---

## Circularly Symmetric (Isotropic) Kernels: Gaussian Filters

In many image processing applications where isotropy (orientation independence) is desired, the preferred kernels are **circularly symmetric** (also called isotropic). This means that the filter response depends only on the distance from the kernel center, not on direction.

### Gaussian Kernels

Gaussian kernels of the form

$$
w(s,t) = G(s,t) = K e^{-\frac{s^2 + t^2}{2\sigma^2}}
$$

are uniquely notable because they are the **only circularly symmetric kernels that are also separable**

-   Here, \( s \) and \( t \) represent spatial coordinates (typically discrete integers for digital images).
-   \( K \) is a normalizing constant.
-   \{sigma} controls the spread (standard deviation) of the Gaussian.

Because Gaussian kernels are separable, they share the **computational efficiency advantages** of box filters, yet exhibit many additional properties that make them especially well-suited for image processing tasks. These properties will be detailed in the following sections.

### Radial Form of the Gaussian Kernel

By defining the radial distance from the center as

$$
r = \sqrt{s^2 + t^2}
$$

we can rewrite the Gaussian function as a function of \( r \) alone:

$$
G(r) = K e^{-\frac{r^2}{2\sigma^2}}
$$


This form emphasizes the **circular symmetry** of the Gaussian function: its value depends solely on the distance \( r \) from the kernel center, regardless of direction.

---

### Kernel Size and Distance Calculation

For typical odd-sized kernels (e.g., $m \times m$), the kernel center lies at an integer coordinate, and the squared distance values $r^2$ at each kernel position are also integers.

Specifically, the squared distance to a corner point of the kernel is given by:

$$
r_{\text{max}}^2 = \left(\frac{m - 1}{2}\right)^2 + \left(\frac{m - 1}{2}\right)^2 = 2 \left(\frac{m - 1}{2}\right)^2
$$

This relation helps in defining the extent of the kernel and in normalizing the Gaussian weights.

---

### Kernel Construction by Sampling

The Gaussian kernel shown above was obtained by **sampling** with parameters $K = 1$ and $\sigma = 1$. The process involves:

1. Specifying discrete values of $s$ and $t$ corresponding to kernel coordinates.
2. Evaluating the Gaussian function at each coordinate to obtain kernel coefficients.
3. Normalizing the kernel by dividing each coefficient by the sum of all coefficients, ensuring that the filter preserves the average intensity of uniform regions (similar to box kernel normalization).

---

### Separable Implementation

Because Gaussian kernels are separable, the 2-D kernel can be constructed efficiently by:

- Taking samples along a 1-D cross-section through the kernel center to form a vector $\mathbf{v}$.
- Using this vector to generate the 2-D kernel via:

$$
\mathbf{K} = \mathbf{v} \mathbf{v}^T
$$

This separability significantly reduces computational complexity, making Gaussian filtering practical for many real-time and high-resolution image processing tasks.

