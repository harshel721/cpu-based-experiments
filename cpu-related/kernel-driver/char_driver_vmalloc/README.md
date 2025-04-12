## **What This Driver Does (High-Level Summary)**

This is a **character device driver** that:
1. Allocates two large memory buffers using `vmalloc()`:
   - `kernel_buffer`: for receiving data from user space.
   - `output_buffer`: for further use, possibly after some processing.
2. Supports standard file operations like `read`, `write`, `open`, and `release`.
3. Implements an `mmap()` function so user space can **directly map the kernel buffers into its own address space**.
4. Uses a static memory size of 1920×1080×3 (RGB data) + 4096 bytes padding.

---

## **Breakdown by Component**

### **Memory Allocation**

```c
kernel_buffer = vmalloc(mem_size);
output_buffer = vmalloc(mem_size);
```

- I have used `vmalloc()` instead of `kmalloc()` because the memory size is large and physically contiguous memory isn't guaranteed with `kmalloc()` for such sizes.
- `vmalloc()` allocates **virtually contiguous memory**, suitable for buffers like frame data.

---

### **Device File Operations**

```c
static struct file_operations fops = {
    .owner   = THIS_MODULE,
    .read    = etx_read,
    .write   = etx_write,
    .open    = etx_open,
    .release = etx_release,
    .mmap    = etx_mmap,
};
```

- `read()` → Copies `kernel_buffer` data to user space.
- `write()` → Receives data from user space into `kernel_buffer` and copies it into `output_buffer`.
- `mmap()` → Allows user space to map either `kernel_buffer` or `output_buffer`.
- `open()` / `release()` → Just logs operations.

---

### **The mmap Implementation**

```c
uint8_t *buffer = (vma->vm_pgoff == 0) ? kernel_buffer : output_buffer;
```

  - I am using `vma->vm_pgoff` to let user space choose which buffer to map.
  - Offset 0 → map `kernel_buffer`
  - Offset 1 → map `output_buffer`

Then I loop through pages:

```c
page = vmalloc_to_page(buffer + buffer_offset);
remap_pfn_range(vma, virt_addr, page_to_pfn(page), PAGE_SIZE, vma->vm_page_prot);
```

- `vmalloc_to_page()` translates the virtual address from `vmalloc()` to a `struct page *`, which is needed to use `remap_pfn_range()`.
- This enables **zero-copy** access from user space — great for performance.

---

### **Logics in read() and write()**

#### `read()`

```c
copy_to_user(buf, kernel_buffer, mem_size);
```

- Always returns the full buffer (`mem_size`), no partial reads.

#### `write()`

```c
copy_from_user(kernel_buffer, buf, len);
memcpy(output_buffer, kernel_buffer, mem_size);
```

- Receives data into `kernel_buffer`, then copies to `output_buffer`.

---

## **Driver Initialization and Cleanup**

- Dynamically allocates a major number using `alloc_chrdev_region`.
- Registers the character device using `cdev_add`.
- Creates a class and device node, accessible via `/dev/etx_device`.
- Allocates both buffers with `vmalloc()`.
- Cleans up all resources on module exit (`vfree`, `device_destroy`, etc.).

---
