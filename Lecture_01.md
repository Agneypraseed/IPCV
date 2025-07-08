**Discretization** refers to the process of transforming a continuous domain into a discrete one by representing it with a finite set of elements. In the context of images, this typically involves converting a continuous spatial domain $ \Omega \subset \mathbb{R}^2 $ (where an image is defined as a continuous function over space) into a finite grid of samples (**pixels**).

**Sampling (Spatial Discretization) ** refers to discretizing the continuous spatial domain $ \Omega $. We choose specific points $ ( x_i, y_j ) $, turning $ f(x,y) $ into a grid of values:

$$ f\_{i,j} = f(x_i, y_j) $$

A cameraman image at resolution 256×256: 256 samples along each axis → 65,536 pixels.

Reducing to 64×64 or 32×32 leads to fewer pixels and loss of detail.

<hr>

The tonal domain (also known as the intensity domain or range) refers to the set of all possible intensity or color values that a pixel in an image can take

**Quantization (Intensity Discretization)**

**Definition:**  
Quantization discretizes the range of grey levels, mapping the continuous intensity $ f(x, y) $ to a finite set, typically integers.

Discretization of the tonal domain, i.e., mapping the continuous range of intensity values to a finite set of discrete levels.

**Most common:**

-   **8-bit quantization** → values in $ \{ 0, 1, \dots, 255 \} $

    -   0: black, 255: white

**Binary Images:**  
Co-domain: $ \{ 0, 1 \} $, often rescaled to $ \{ 0, 255 \} $

        Result from thresholding operations, used in document scanning, object detection, etc.

<h3>
To convert a continous image into a digital format we use Sampling and Quantization
</h3>

**Image:**

-   **Original grayscale image** → 256×256, 8-bit depth.

**Downsampling:**

-   **Sample to 64×64** → detail is coarser, but general structure remains.
-   **Sample to 32×32** → fine detail lost; image appears blocky.

**Quantization/Binarization:**

-   Apply a threshold (e.g., 128). All pixel values $ \geq 128 $ → white (255), others → black (0).

**Final result:** a binary image highlighting edges or structures.

![alt text](/images/image-1.png)

![alt text](/images/samplingandquantization.png)

<hr>

A color image is defined as a function:

$$ f(x, y): \Omega \rightarrow \mathbb{R}^3 $$

**RGB Color Model**

Standard color model for display systems(monitors, cameras).

Each channel is typically 8 bits → $ [0, 255] $ per channel.

A pixel like:

-   $ [255, 0, 0] $ → red;
-   $ [0, 255, 0] $ → green;
-   $ [255, 255, 255] $ → white.

**HSV color space with three channels**  
HSV stands for:

-   **Hue (H):** Color type (e.g., red, green, blue).
-   **Saturation (S):** Intensity/purity of the color.
-   **Value (V):** Brightness level.

**Advantages:**  
HSV is perceptually uniform, i.e., it better separates color properties important for human vision.

**Useful in:**

-   Object tracking
-   Image segmentation
-   Thresholding in color spaces

| RGB (R,G,B)     | HSV (H,S,V)               |
| --------------- | ------------------------- |
| [255, 0, 0]     | [0°, 1, 1] (pure red)     |
| [0, 255, 0]     | [120°, 1, 1] (pure green) |
| [0, 0, 255]     | [240°, 1, 1] (pure blue)  |
| [128, 128, 128] | [0°, 0, 0.5] (grey)       |

<hr>


