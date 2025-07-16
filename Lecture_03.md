## Image Histogram

An **image histogram** is a fundamental tool used for analyzing and manipulating digital images. It provides a graphical representation of the distribution of pixel intensity values.

---

### Definitions

- **Image Size (M, N)**:  
  An image is a grid of pixels with `M` rows (height) and `N` columns (width).  
  - Total number of pixels: `M × N`

- **Grey Values (`r_k`, `L`)**:  
  Brightness is quantized into discrete levels.  
  - `L`: Number of grey levels (e.g., `L = 256` for 8-bit images)  
  - `r_k`: A grey level, where `r_0 = 0` (black), `r_{L-1} = 255` (white)

- **Pixel Count (`n_k`)**:  
  The number of pixels in the image having grey level `r_k`.  
  - For example, `n_0` is the count of pixels with intensity 0.

- **Unnormalized Histogram `h(r_k)`**:  
  The raw count of pixels for each grey level.  
  `h(r_k) = n_k`  
  - Depends on image size  
  - Not suitable for comparison across different image sizes

- **Histogram Bins**:  
  Each grey level `r_k` is treated as a bin. The histogram distributes pixel values into these `L` bins.

- **Normalized Histogram `p(r_k)`**:  
  The probability distribution of grey levels across the image:  
  `p(r_k) = n_k / (M × N)`  
  - Values lie in `[0, 1]`  
  - Independent of image size  
  - The sum of all `p(r_k)` is 1

---

---

### Numerical Example

A 4×4 grayscale image with \( L = 8 \) grey levels:

```
[ 2, 3, 3, 4 ]
[ 1, 2, 4, 5 ]
[ 0, 1, 5, 6 ]
[ 1, 2, 6, 7 ]
```

- Rows: `M = 4`  
- Columns: `N = 4`  
- Total pixels: `M × N = 16`  
- Grey levels: `r_k ∈ {0, 1, ..., 7}`

#### Step 2: Unnormalized Histogram

| \( r_k \) | \( n_k \) |
|----------|-----------|
| 0        | 1         |
| 1        | 3         |
| 2        | 3         |
| 3        | 2         |
| 4        | 2         |
| 5        | 2         |
| 6        | 2         |
| 7        | 1         |
| **Total**| **16**    |

#### Step 3: Normalized Histogram `p(r_k) = n_k / 16`

| Grey Value `r_k` | Count `n_k` | Normalized `p(r_k)` |
|------------------|-------------|----------------------|
| 0                | 1           | `1/16 = 0.0625`      |
| 1                | 3           | `3/16 = 0.1875`      |
| 2                | 3           | `3/16 = 0.1875`      |
| 3                | 2           | `2/16 = 0.125`       |
| 4                | 2           | `2/16 = 0.125`       |
| 5                | 2           | `2/16 = 0.125`       |
| 6                | 2           | `2/16 = 0.125`       |
| 7                | 1           | `1/16 = 0.0625`      |
| **Total**        | **16**      | **1.0**              |

---



---
