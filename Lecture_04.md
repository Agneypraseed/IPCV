## Spatial vs Frequency Domain: The Role of the Fourier Transform

The connection between spatial- and frequency-domain processing is established by the **Fourier transform**.

- To move from the **spatial domain** to the **frequency domain**, we use the **Fourier transform**.
- To return, we use the **inverse Fourier transform**.

---  
1. **Convolution in the spatial domain is equivalent to multiplication in the frequency domain**, and vice versa.
2. **An impulse of strength `A` in the spatial domain is a constant value `A` in the frequency domain**, and vice versa.

---

### Frequency Representation of Images

Any function (including an image) that satisfies certain mild conditions can be expressed as a **sum of sinusoids** with different frequencies and amplitudes.

- **Low-frequency components** describe regions with slow intensity changes (e.g., smooth walls).
- **High-frequency components** correspond to sharp transitions (e.g., edges).

Thus, modifying these frequency components changes the **appearance of the image**:

- Reducing high-frequency components leads to **blurring**.
- Enhancing high frequencies increases **sharpness**.

---

### Linear Filtering in Frequency Domain

In the **spatial domain**, linear filtering is done via **convolution**.

In the **frequency domain**, it is done via **multiplication with a transfer function** (a more intuitive approach). This duality makes it crucial to understand frequency-domain concepts when working with spatial filters.

---

### Example: 1-D Frequency Domain Filtering

Suppose we want to remove all frequency components **above a cutoff `u₀`** in a 1-D function (like an intensity scan line). This is achieved using a **lowpass filter**.

- The filter **passes** frequencies below `u₀` and **eliminates** those above it.
- The corresponding frequency-domain function is called a **filter transfer function**.

#### Ideal Lowpass Filter

An ideal lowpass filter has an **instantaneous transition** between passed and blocked frequencies.

- While not physically realizable, it is valuable for theoretical analysis.
- It causes **ringing artifacts** when implemented digitally.

---

### Filtering Procedure in Frequency Domain

1. Compute the **Fourier transform** of the signal.
2. Multiply the result by the **filter transfer function** to suppress frequencies above `u₀`.
3. Take the **inverse Fourier transform** of the result.
4. The outcome is a **blurred version** of the original signal.

---

### Spatial Domain Equivalent

Due to the spatial-frequency duality:

- The same effect can be achieved by **convolution** in the spatial domain.
- The **equivalent spatial filter kernel** is the **inverse Fourier transform** of the frequency-domain transfer function.

#### Example:

- If the filter transfer function is a rectangular function (ideal lowpass),
- Then the corresponding spatial filter kernel will show **ringing**.

This is shown in *Figure 3.32(b)* (not included here), where the kernel exhibits oscillations due to the abrupt frequency cutoff.

---

### Summary

- **Fourier transform** provides a bridge between spatial and frequency-domain processing.
- **Convolution ↔ Multiplication** and **Impulse ↔ Constant** are two central principles.
- Ideal filters, while not practical, serve as useful tools for understanding image filtering behavior.
- **Practical filter design** focuses on balancing frequency selectivity and spatial ringing.

