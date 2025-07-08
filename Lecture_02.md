**Image formation** involves capturing a scene via a camera, influenced by:

-   **Illumination:** The light source and its intensity.
-   **Scene:** The objects being photographed.
-   **Imaging system:** The optics (lens, sensor) that project the scene onto a 2D image plane.

This projection results in a continuous image function $ f(x, y) $, where $ f $ gives a grey value (or intensity) at each point.

The grey value $ f(x, y) $ is thus a product of how much light there is and how reflective the object is at that point.

A monochrome image is described by a function:

$$ f(x, y) = i(x, y) \cdot r(x, y) $$

where:

-   $ i(x, y) $: Illumination function.
-   $ r(x, y) $: Reflectance function.
-   $ f(x, y) $: Resultant grey value.

Digital grey values are usually within the range $ [0, 255] $ or normalized to $ [0, 1] $.

**Sampling and Quantization**

-   **Sampling:** Converts continuous spatial domain into a discrete grid.
-   **Quantization:** Converts continuous intensity values into discrete levels.

<hr>

A digital image is a 2D matrix $ f(x, y) $ of size $ M \times N $ (rows × columns).

Each element $ f(i, j) $ is a pixel at position $ (i, j) $.

**Coordinate system:**

-   Origin at the top-left: $ (0, 0) $
-   Image center: $ (x_c, y_c) = (\lfloor M/2 \rfloor, \lfloor N/2 \rfloor) $

![alt text](/images/image.png)

<hr>

**Linear Indexing**

Digital images are typically represented as 2D arrays (matrices) where each element corresponds to a pixel intensity. However, in many programming environments or memory storage systems, especially in lower-level languages like C or when interfacing with image files, these 2D arrays are stored in a linear (1D) format in memory.

Computers don't have 2D memory; they store data in a single, long, one-dimensional line. To store an image, we must "flatten" its 2D grid of pixels into a 1D list. This process is called linear indexing ()

_Column-wise scan (column-major order) :_

![alt text](/images/image1.png)

    Imagine reading the pixels of the image not like a book (left-to-right, then top-to-bottom), but by going down each column completely before moving to the next column on the right.
    As the diagram shows, you start at the top of the first column (y=0) and go all the way down. Then you move to the top of the second column (y=1) and go all the way down, and so on

To convert a 2D coordinate $ (x, y) $ to a 1D index $ \alpha $, use the following formula:

$$ \alpha = M \cdot y + x $$

Where:

-   $ M $ is the number of columns in the image (or matrix).
-   $ x $ is the horizontal coordinate (column index).
-   $ y $ is the vertical coordinate (row index).

**Recovery:** (From 1D index back to 2D coordinates)

-   $$ x = \alpha \mod M $$
-   $$ y = \frac{\alpha - x}{M} $$

<hr>

**Column-major order indexing:**  
This is the order used by Fortran, MATLAB, and OpenCV in some contexts:

$$ \alpha = M \cdot y + x $$

Where:

-   $ M $ is the number of rows in the matrix.
-   $ x $ is the horizontal coordinate (column index).
-   $ y $ is the vertical coordinate (row index).

→

**Row-major order indexing:**  
This is used in C, C++, Python (NumPy):

$$ \alpha = N \cdot x + y $$

Where:

-   $ N $ is the number of columns in the matrix.
-   $ x $ is the horizontal coordinate (column index).
-   $ y $ is the vertical coordinate (row index).

<hr>

**Intensity Resolution**  
 `Intensity resolution refers to the number of distinct gray levels (or intensity levels) that can be represented in an image.`

Bit depth defines the number of bits used to represent the intensity value of each pixel in a digital image. It determines the total number of available tonal (gray) levels that can be encoded.

A $ b $-bit image has $ 2^b $ grey levels.

-   **8-bit** → 256 levels (0–255).

When you use too few intensity levels for an image that has smooth gradients (like a photo of a clear sky or a curved surface), you get an artefact called posterization or false contouring.

Progressive degradation in quality observed as intensity levels reduce from 256 to 2.

Low resolution (2–16 levels) reveals unintended contours and artefacts.

![alt text](/images/image2.png)

<hr>

**Arithmetic Operations Between Images**  
Applicable only if the images are of the same size:

| Operation      | Formula                             | Meaning                                 |
| -------------- | ----------------------------------- | --------------------------------------- |
| **Sum**        | $ s(x, y) = f(x, y) + g(x, y) $     | Brightens image or combines intensities |
| **Difference** | $ d(x, y) = f(x, y) - g(x, y) $     | Highlights differences (e.g., motion)   |
| **Product**    | $ p(x, y) = f(x, y) \cdot g(x, y) $ | Blending, masking                       |
| **Quotient**   | $ q(x, y) = f(x, y) / g(x, y) $     | Contrast normalization                  |

**Note:** Results can go outside $ [0, 255] $ and require post-processing:

-   Clipping (force into range),
-   Scaling, or
-   Rounding to integers.

<hr>

### Noise reduction by image addition

![alt text](/images/image3.png)

The core idea of this technique is: If you take many photos of the exact same (static) scene, you can average them together to cancel out the random noise, revealing the clean underlying image.

Suppose that $ g(x, y) $ is a corrupted image formed by addition of noise $ \eta(x, y) $ to an uncorrupted image $ f(x, y) $:

$$ g(x, y) = f(x, y) + \eta(x, y) $$

We assume that:

1. Noise and image values are uncorrelated. (This means the amount of noise at a pixel isn't related to the pixel's true brightness. A dark part of the image is just as likely to get a certain random noise value as a bright part.)
2. The noise has zero mean value. ( For any given pixel, the random noise $ \eta $ is equally likely to be positive (making the pixel brighter) as it is to be negative (making it darker). Over many images, the average of all the random noise values at that one pixel location will be zero.
   $$ E[\eta(x, y)] = 0. $$
   )

**Averaging $ K $ noisy images:**

$$ \frac{1}{K} \sum\_{i=1}^{K} g_i(x, y) \approx f(x, y) $$

<hr>

### Caution with Differences

_Subtraction used to detect changes._

![alt text](/images/image4.png)

$$ g(x, y) = f(x, y) - h(x, y) $$

This operation can give negative values.

**Problems:**

-   Negative values (which cannot be directly displayed)
-   Visual indistinguishability unless processed.

When the difference is small, $ g(x, y) $ is close to 0, which appears dark or black.  
When the difference is large and positive, $ g(x, y) $ is a large value, appearing bright or white.

To handle negative results and see the magnitude of the difference, we take the absolute value:

$$ |g(x, y)| $$

Now, small differences are still dark (near 0) and large differences are bright.

To improve visual interpretability, particularly when differences are small, we invert the image:

$$ \tilde{g}(x, y) = L - 1 - |g(x, y)| $$

Where:

-   $ L $ is the intensity resolution, typically 256, so $ L - 1 = 255 $.

**The effect:**

-   Smaller differences (i.e., areas where the two images are similar) appear brighter (closer to 255).
-   Larger differences appear darker.

**Example**  
Let us assume:

-   $ f(x, y) $: original image.
-   $ h(x, y) $: same image but with a small object removed (e.g., a person erased).

**Compute:**

$$
g(x, y) = f(x, y) - h(x, y)


$$

|g(x, y)| : gives zero everywhere except where the object was removed.

$$

 \tilde{g}(x, y) = 255 - |g(x, y)|


$$

**Results:**

-   All unchanged areas → $ \tilde{g} = 255 $ (white).
-   Changed area (object removed) → darker pixels (as intensity difference is non-zero).

**Visualization:**

The resulting image $ \tilde{g} $ will appear white everywhere, with dark pixels exactly where the image was altered. This makes the change easily detectable by the human eye.
