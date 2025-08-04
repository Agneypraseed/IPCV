## Principles of Rank-Order Filtering

-   Consider a subimage `Sₓ,ᵧ` of `f` centered at pixel `(x, y)`.
-   The subimage `Sₓ,ᵧ` acts as a **sliding window**, where the center visits every pixel `(x, y)` in `f`.
-   At each visited `(x, y)`, we consider the gray values in the subimage `Sₓ,ᵧ`.
-   We **rank** the gray values of `Sₓ,ᵧ` and sort them in ascending order (from smallest to largest).
-   The gray value at a specified **rank** is taken as the gray value of the filtered image `g(x, y)`.

Examples of ranks:

-   **Maximum** (Max Filter)
-   **Minimum** (Min Filter)
-   **Median** (Median Filter)

---

## Explanation of the Principles

This section explains the algorithmic steps behind rank-order filtering, a class of **non-linear spatial filters** that, unlike linear filters (which use weighted sums), operate on the **order** of pixel values.

### 1. Sliding Window (`Sₓ,ᵧ`)

-   A subimage (e.g., 3×3 window) slides over the original image `f`.
-   At each position `(x, y)`, the window is centered and gathers surrounding pixel values.

### 2. Gather the Pixel Values

-   Collect all intensity values from within `Sₓ,ᵧ`.
-   For example, in a `3×3` window, you gather `9` values.

### 3. Rank and Sort

-   Sort the gathered values in ascending order (ranking).
-   The sorted list provides the basis for selecting a new pixel value.

### 4. Pick a Value Based on Rank

-   The filtered value `g(x, y)` is chosen based on a specific rank.

#### Examples:

-   **Min Filter**: Pick the smallest value (first in the sorted list).  
    _Effect_: Darkens the image and expands dark regions.

-   **Max Filter**: Pick the largest value (last in the sorted list).  
    _Effect_: Brightens the image and expands bright regions.

-   **Median Filter**: Pick the middle value.  
    _Effect_: Highly effective at removing _salt-and-pepper_ noise while preserving edges.  
    _Example_: In a 3×3 window, select the 5th value out of 9.

This process is simple yet powerful for noise reduction and edge preservation in digital image processing.

---

## Median Filtering: Example

The subimage `Sₓ,ᵧ` often has an odd number of pixels, e.g., 3×3, 5×5.

As an example, we consider a 3×3 subimage `Sₓ,ᵧ` with gray values:

```
[ 1  4  7 ]
[ 7  2  3 ]
[ 4  6  6 ]
```

-   The gray values in `Sₓ,ᵧ` are put into an n-tuple (row-wise):  
    `[1, 4, 7, 7, 2, 3, 4, 6, 6]`

-   We sort these values in ascending order:  
    `[1, 2, 3, 4, 4, 6, 6, 7, 7]`

-   The median is the value at the center of the sorted tuple.  
    For 9 values, the **5th** value is the median: `4`

-   Final sorted list with the median highlighted:  
    `[1, 2, 3, 4, **4**, 6, 6, 7, 7]`

Thus, the center pixel in the filtered image `g(x, y)` will be **4**, replacing the original value `2`.

---

## Why Median Filtering Is Effective for Denoising

-   If the original center pixel were a noise spike (e.g., salt noise with value 255), that extreme value would be at one end of the sorted list.
-   The median, being in the center, **ignores** such outliers.
-   As a result, median filtering is highly effective against **impulse noise** (salt-and-pepper), while still preserving **edges** and image structure.

![alt text](/images/image73.png)

---

## Median Filtering: Elementary Properties

### Example Tuples

Consider gray values in the tuple:

```
(30, 30, 30, 30, 30, 30, 30, 30, 30)
```

-   This represents a flat, noise-free region.
-   The **median** is: `30`
-   **Result:** The image remains unchanged—no distortion or blur.

Now, adding **Gaussian noise** might yield:

```
(21, 25, 26, 27, 27, 28, 33, 35, 43)
```

-   This represents the same flat area after some moderate Gaussian noise has been added. The values are now all slightly different, scattered around the original value of 30.
-   The sorted version:  
    `(21, 25, 26, 27, 27, 28, 33, 35, 43)`
-   The **median** is: `27`  
    (This differs from the original value `30`)
-   **Result:** Slight deviation from true value (`30`), but **no blurring** as in averaging filters.
-   **Observation:** Median filter chooses a real value from the data, avoiding synthetic interpolation.

Now consider **salt-and-pepper noise** (outliers):

```
(0, 0, 30, 30, 30, 30, 255, 255, 255)
```

-   This represents the same flat area, but now several pixels have been corrupted with extreme "salt" (255) and "pepper" (0) noise. These are called **outliers**.
-   The sorted version:  
    `(0, 0, 30, 30, 30, 30, 255, 255, 255)`
-   The **median** is: `30`  
    (Identical to the original value)
-   **Result:** Accurately recovers the original value.
-   **Observation:** Extreme values are pushed to the ends of the sorted list and ignored. The median filter perfectly rejects the outlier noise and recovers the original, true value. This is its greatest strength.

---

Strength (vs. Outliers): The median filter is excellent at removing salt-and-pepper noise and other impulse-like outliers. This is because the outliers are ranked at the extremes of the sorted list and are ignored when the middle value is chosen.

Weakness (vs. Gaussian Noise): The median filter is not as effective as an averaging (box) filter at removing fine-grained Gaussian noise. It will reduce the noise but may not produce as smooth a result, and it will slightly shift the true value of the pixels. Its key advantage is that it avoids the blurring that averaging filters introduce, making it much better at preserving sharp edges.

---

An example of The median filter's ability to **preserve sharp edges**.

We consider the following 4×4 grayscale image `f`:

```
[  0   0   0   0 ]
[  0  90  90  90 ]
[  0  90  90  90 ]
[  0  90  90  90 ]
```

This image contains a clear vertical edge separating regions of intensity `0` and `90`.

---

### First 3×3 Subimage (Centered on Edge)

Extracted subimage:

```
[  0  90  90 ]
[  0  90  90 ]
[  0  90  90 ]
```

-   Corresponding 9-tuple:  
    `(0, 90, 90, 0, 90, 90, 0, 90, 90)`

-   Sorted:  
    `(0, 0, 0, 90, **90**, 90, 90, 90, 90)`

-   **Median** (5th value): `90`

-   **Output pixel value**: `90`

---

### Second 3×3 Subimage (Also Centered on Edge)

Extracted subimage:

```
[  0   0   0 ]
[ 90  90  90 ]
[ 90  90  90 ]
```

-   Corresponding 9-tuple:  
    `(0, 0, 0, 90, 90, 90, 90, 90, 90)`

-   Sorted:  
    `(0, 0, 0, 90, **90**, 90, 90, 90, 90)`

-   **Median** (5th value): `90`

-   **Output pixel value**: `90`

This example illustrates what happens when a median filter window crosses a **sharp intensity edge**.

-   In both subimages, the filter's output is **either 0 or 90**. When the filter's window contains more "90" pixels than "0" pixels, the median will be 90. When it contains more "0" pixels, the median will be 0.
-   The median filter **does not produce intermediate values** (like 30 or 60), which would cause blurring in linear filters.
-   The median chooses an actual value from the window based on rank, not arithmetic averaging.
-   **Edge preservation** results from the filter's tendency to snap to the majority intensity in the neighborhood. This behavior is what allows it to preserve sharp edges without blurring them. It maintains the step-like transition from one region to the next

---

## Median Filtering: Handling Image Borders (Padding Strategies)

### Example Image

Consider the following 4×4 grayscale image `f`:

```
[  0   0   0   0 ]
[  0  90  90  90 ]
[  0  90  90  90 ]
[  0  90  90  90 ]
```

Our goal is for the median filter to **preserve edges near the boundary**.

---

### The Problem: Incomplete Filter Window Near Boundary

When applying a 3×3 median filter window centered on a pixel in the last row, part of the window lies outside the image:

```
[  0   90  90 ]
[  0   90  90 ]
[  ?    ?   ? ]  ← Unknown padding values
```

-   The corresponding 9-tuple is:  
    `(0, 90, 90, 0, 90, 90, ?, ?, ?)`  
    where `?` are undefined.

-   To correctly compute the median, all 9 values must be known.

---

### Desired Outcome

-   To preserve the **apparent edge**, it is desirable that the median value remains `90`.
-   Currently, we have four known values equal to `90` and two zeros.
-   To have a median of `90` in a 9-element list, **at least five values must be 90 or greater**.
-   Therefore, **at least one** of the `?` values must be `90`.

---

### Padding Strategy: Duplication (Replication)

A common and effective approach is **duplicating border pixels**:

-   Fill the unknown padding by copying the nearest valid pixels inside the image.
-   For this example, duplicate the last valid row:

```
[  0   90  90 ]
[  0   90  90 ]
[  0   90  90 ]  ← Duplicated row used for padding
```

-   The full tuple becomes:  
    `(0, 90, 90, 0, 90, 90, 0, 90, 90)`

-   Sorted tuple:  
    `(0, 0, 0, 90, **90**, 90, 90, 90, 90)`

-   Median (5th value): `90`

This ensures the median filter output preserves the edge near the boundary.

---

### Other Common Padding Methods

-   **Zero Padding:** Fill unknowns with zeros.
    -   In this example, would reduce median to 0, undesirably weakening the edge.
-   **Mirror Padding:** Reflect pixel values across the border.
    -   Can also preserve edge information but with a slightly different effect.
        ![alt text](/images/image74.png)

---

### Elementary Properties — Corner Effect

Consider the 4×4 grayscale image `f`:

```
[  0   0   0   0 ]
[  0  90  90  90 ]
[  0  90  90  90 ]
[  0  90  90  90 ]
```

---

### The 3×3 Subimage at a Corner

Focus on the 3×3 subimage centered on the top-left corner pixel of the gray square:

```
[  0   0   0 ]
[  0  90  90 ]
[  0  90  90 ]
```

From this subimage, we form the 9-tuple of pixel values (row-wise):

```
(0, 0, 0, 0, 90, 90, 0, 90, 90)
```

-   Count of values: five `0`s and four `90`s.
-   Sorting these values:

```
(0, 0, 0, 0, <u>0</u>, 90, 90, 90, 90)
```

-   The median is the 5th value (middle element), which is **0**.

-   **Original center pixel value:** 90
-   **Filtered pixel value after median filtering:** 0

This indicates that the corner pixel is replaced by a lower value, effectively "rounding off" or "clipping" the sharp corner of the gray square.

-   While the median filter is excellent at preserving **straight edges** without blurring, it has a known side effect near **sharp convex corners**.
-   Specifically, it tends to **round off corners**, smoothing or slightly distorting them.
-   This effect occurs because the median filter selects the middle value from a sorted neighborhood, which can shift the corner pixel's intensity toward the background level if more background pixels surround the corner.

---
### Example of a 3×3 median filter to a 4×4 image, incorporating mirror padding

### Original 4×4 Image `f`

```
[  0   0   0   0 ]
[  0  90  90  90 ]
[  0  90  90  90 ]
[  0  90  90  90 ]
```

---

### Step 1: Mirror Padding (6×6 Image)

To apply the median filter at all positions—including borders—we pad the image by **reflecting the boundary pixels**. This creates a 6×6 padded version of the original 4×4 image:

```
[ 0  0  0  0  0  0 ]
[ 0  0  0  0  0  0 ]
[ 0  0 90 90 90 90 ]
[ 0  0 90 90 90 90 ]
[ 0  0 90 90 90 90 ]
[ 0  0 90 90 90 90 ]
```

Mirror padding reflects both rows and columns outward from the edges.

---

### Step 2: Apply 3×3 Median Filter

We now apply a 3×3 median filter centered on each pixel of the original 4×4 image (i.e., positions `[1,1]` to `[4,4]` in the padded image). For each position, the median of the corresponding 3×3 window is calculated.

#### Calculating Output at (1,1) — the First 90° Corner

The pixel at position (1,1) in the original 4×4 image corresponds to position (2,2) in the padded image.

3×3 window centered at (2,2):

```
[  0   0   0 ]
[  0  90  90 ]
[  0  90  90 ]
```

Flattened values:
```
(0, 0, 0, 0, 90, 90, 0, 90, 90)
```

Sorted:
```
(0, 0, 0, 0, **0**, 90, 90, 90, 90)
```

**Median** = 0

Thus, **the corner pixel is rounded off**, confirming the median filter’s natural smoothing effect at sharp transitions when using accurate mirror padding.

---

### Final Correct Filtered Output

```
[  0   0   0   0 ]
[  0   0  90  90 ]
[  0  90  90  90 ]
[  0  90  90  90 ]
```

The upper-left corner (originally a sharp 90° transition) has now been smoothed.

---

## Example for 3x3 Median Filtering: Thin Image Structures

We consider now as an example the 4x4 image `f` as follows:

```plaintext
[  0  90   0   0 ]
[  0  90   0   0 ]
[  0  90   0   0 ]
[  0  90   0   0 ]
```

**Mirror padding** of our example image results in:

```
[  0  90   0   0   0   0 ]
[  0  90   0   0   0   0 ]
[  0  90   0   0   0   0 ]
[  0  90   0   0   0   0 ]
[  0  90   0   0   0   0 ]
[  0  90   0   0   0   0 ]
```

**Filtering result:**

```
[  0   0   0   0 ]
[  0   0   0   0 ]
[  0   0   0   0 ]
[  0   0   0   0 ]
```

This example demonstrates another critical property of the median filter: its effect on small, thin details.

The image contains a thin vertical line of gray pixels (value 90) on a black background (value 0). The line is only one pixel wide.

- Any pixel on the vertical line will have 3 gray pixels (90) and 6 black pixels (0) in its neighborhood.
- Since 0s outnumber 90s, **the median filter will output 0** at every such location.
- Thus, the vertical line is **entirely removed**.

> The median filter removes structures smaller in size than half the size of the filtering window.

- For a 3×3 window, the size is 9 pixels.
- Half of this is 4.5.
- The vertical line has only 3 pixels in any such window → **less than 4.5** → **removed**.

This property makes the median filter excellent at eliminating isolated noise specks but also makes it **destructive to fine, thin image structures**.

---

