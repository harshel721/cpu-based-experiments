### **What the Code Does:**

This C program benchmarks a basic **image scaler** by performing repeated read-scale-write operations:

- ✅ Initializes a source image buffer with random pixel data (RGBA format).
- ✅ Scales it to a new resolution using **nearest-neighbour interpolation**.
- ✅ Writes the scaled result to another memory buffer.
- ✅ Repeats this process for `MAX_ITERATIONS` times (e.g., 100 iterations).
- ✅ Measures the **total time** taken for these operations using OpenMP (`omp_get_wtime()`).
