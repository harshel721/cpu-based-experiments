### **What the Code Does:**

This C program benchmarks a basic **image scaler** by performing repeated read-scale-write operations:

- ✅ Initializes a source image buffer with random pixel data (RGBA format).
- ✅ Scales it to a new resolution using **nearest-neighbour interpolation**.
- ✅ Writes the scaled result to another memory buffer.
- ✅ Repeats this process for `MAX_ITERATIONS` times (e.g., 100 iterations).
- ✅ Measures the **total time** taken for these operations using OpenMP (`omp_get_wtime()`).
---

### User-Space C Implementation Results

| Scaling Algorithm | Input Resolution | Output Resolution | Raspberry Pi 3 Time (s) | RPi 3 FPS | i.MX 8M Mini Time (s) | i.MX 8M Mini FPS |
|-------------------|------------------|-------------------|--------------------------|-----------|------------------------|------------------|
| Basic Read/Write  | 1920×1080        | 1920×1080         | 1.04142                  | 10.4142   | 0.245302               | 407.66           |
| Nearest Neighbour | 1024×768         | 1336×768          | 1.331526                 | 75.10     | 0.422762               | 236.54           |
|                   | 1024×768         | 1440×1080         | 2.11829                  | 47.21     | 0.624549               | 160.12           |
|                   | 1024×768         | 1728×1080         | 2.495488                 | 40.07     | 0.771619               | 129.60           |
|                   | 1024×768         | 1920×1080         | 2.725358                 | 36.69     | 0.88435                | 113.08           |
| Bilinear          | 1024×768         | 1336×768          | 8.730259                 | 11.45     | 3.111139               | 32.14            |
|                   | 1024×768         | 1440×1080         | 13.302471                | 7.52      | 4.619544               | 21.65            |
|                   | 1024×768         | 1728×1080         | 15.846381                | 6.31      | 5.525804               | 18.10            |
|                   | 1024×768         | 1920×1080         | 17.52119                 | 5.71      | 6.127758               | 16.32            |
| Bicubic           | 1024×768         | 1336×768          | 184.244333               | 0.54      | 64.351306              | 1.55             |
|                   | 1024×768         | 1440×1080         | 294.087379               | 0.34      | 100.042497             | 1.00             |
|                   | 1024×768         | 1728×1080         | 351.597996               | 0.28      | 119.356341             | 0.84             |
|                   | 1024×768         | 1920×1080         | 389.936601               | 0.26      | 132.679374             | 0.75             |

---

### Kernel Module (CPU) – `dma_alloc_coherent()`

| Input Resolution | Output Resolution | Raspberry Pi 3 Time (s) | RPi 3 FPS |
|------------------|-------------------|--------------------------|-----------|
| 640×480          | 1920×1080         | 8.772348                 | 11.40     |
| 800×600          | 1920×1080         | 9.45311                  | 10.58     |
| 1024×768         | 1920×1080         | 9.787781                 | 10.22     |
| 1152×864         | 1920×1080         | 9.226945                 | 10.84     |
| 1366×768         | 1920×1080         | 10.077721                | 9.92      |
| 1280×800         | 1920×1080         | 10.253357                | 9.75      |
| 1280×1024        | 1920×1080         | 9.736067                 | 10.27     |
| 1440×900         | 1920×1080         | 9.797006                 | 10.21     |
| 1600×1200        | 1920×1080         | 9.968937                 | 10.03     |
| 1920×1080        | 1920×1080         | 10.418996                | 9.60      |

---

### Kernel Module (CPU) – `vmalloc()`

| Input Resolution | Output Resolution | Raspberry Pi 3 Time (s) | RPi 3 FPS |
|------------------|-------------------|--------------------------|-----------|
| 640×480          | 1920×1080         | 0.720607                 | 138.77    |
| 800×600          | 1920×1080         | 0.745894                 | 134.07    |
| 1024×768         | 1920×1080         | 0.78468                  | 127.44    |
| 1152×864         | 1920×1080         | 0.798402                 | 125.25    |
| 1366×768         | 1920×1080         | 0.814797                 | 122.73    |
| 1280×800         | 1920×1080         | 0.816395                 | 122.49    |
| 1280×1024        | 1920×1080         | 0.86249                  | 115.94    |
| 1440×900         | 1920×1080         | 0.837435                 | 119.41    |
| 1600×1200        | 1920×1080         | 0.925934                 | 108.00    |
| 1920×1080        | 1920×1080         | 0.977059                 | 102.35    |

You can find individual  README explaining the code in the respective directories.

```text 
.
├── cpu-related
│   ├── kernel-driver
│   │   ├── char_driver_vmalloc
│   │   │   ├── char_app.c
│   │   │   ├── char_driver.c
│   │   │   ├── Makefile
│   │   │   └── README.md
│   │   ├── plat_driver_dma
│   │   │   ├── Makefile
│   │   │   ├── plat_app.c
│   │   │   ├── plat_driver.c
│   │   │   └── README.md
│   │   └── README.md
│   └── simple-c-codes
│       ├── basic-read-write.c
│       ├── bicubic-interpolation.c
│       ├── bilinear-interpolation.c
│       ├── Makefile
│       └── nearest-neighbour.c
```
