# BFILTER3

3D bilateral filtering for grayscale volumetric data.

## Description

`bifilter3` implements a three-dimensional bilateral filter for grayscale volumes.  
The bilateral filter smooths noise while preserving edges by combining:

- **Spatial weighting** (distance between voxels)
- **Intensity weighting** (difference in voxel values)

This implementation is suitable for volumetric medical imaging data such as:

- MRI
- DTI-derived maps
- CT
- Other 3D scalar volumes

Developed by Nguyen Ngoc My, Kyoto Institute of Technology, Japan, 2021.  
Based on 2D bilateral filtering by Douglas R. Lanman, Brown University.

---

## Function Signature

```matlab
B = bifilter3(A, w, sigma)
```

---

## Inputs

### `A`
3D input volume of type `double`.

Example:

```matlab
A = double(niftiread('brain.nii'));
```

---

### `w`
Half window size of the bilateral filter.

The effective kernel size becomes:

```text
(2w + 1) × (2w + 1) × (2w + 1)
```

Example:

```matlab
w = 5;
```

Default:

```matlab
w = 5
```

---

### `sigma`
Two-element vector controlling filter behavior:

```matlab
sigma = [sigma_d sigma_r]
```

Where:

| Parameter | Description |
|---|---|
| `sigma_d` | Spatial Gaussian standard deviation |
| `sigma_r` | Intensity/range Gaussian standard deviation |

Example:

```matlab
sigma = [3 0.1];
```

Default:

```matlab
sigma = [3 0.1]
```

---

## Output

### `B`
Filtered 3D volume.

Same dimensions as input volume `A`.

---

## Example

```matlab
A = double(niftiread('fa_map.nii'));

w = 3;
sigma = [2 0.05];

B = bifilter3(A, w, sigma);

imshow(B(:,:,40), []);
```

---

## Method

For each voxel:

1. Extract a local neighborhood
2. Compute:
   - spatial Gaussian weights
   - intensity Gaussian weights
3. Multiply both weights
4. Normalize weighted average

The bilateral filter response is:

```math
B(p) = \frac{1}{W_p} \sum_{q \in \Omega}
G_{\sigma_d}(||p-q||)
G_{\sigma_r}(|I(p)-I(q)|)
I(q)
```

where:

- \( p \): center voxel
- \( q \): neighboring voxel
- \( G_{\sigma_d} \): spatial Gaussian
- \( G_{\sigma_r} \): intensity Gaussian
- \( W_p \): normalization factor

---

## Notes

- Input volume must be of type `double`
- Computational cost increases rapidly with:
  - larger volume size
  - larger filter window `w`
- The implementation is voxel-wise and may be slow for large datasets

---

## Typical Parameters

| Application | `w` | `sigma_d` | `sigma_r` |
|---|---:|---:|---:|
| MRI denoising | 3–5 | 2–4 | 0.03–0.10 |
| FA maps | 2–4 | 1–3 | 0.01–0.05 |
| CT smoothing | 3–7 | 3–5 | 0.05–0.20 |

---

## File

```text
bifilter3.m
```

---

## License

Academic/research use.
