# UniLight HDR-VDP3 Python package

Pinned JPEG AI HDR-VDP3.0.7 Python port from https://gitlab.com/wg1/jpeg-ai/jpeg-ai-qaf.git,
branch `feature/HDR-VDP2.2/main`, commit `0628a6b925e6513aff6fabfe81480648cfe40334`.

The import is `from VDP3.hdrvdp3 import hdrvdp3`. This is a CPU NumPy/SciPy implementation.
The `VDP3` package includes its spectral CSV/TXT data and upstream revision metadata.

Local compatibility changes use SciPy cumulative_trapezoid/trapezoid instead of removed APIs,
replace an ndarray-versus-empty-list test with a size check, and correct a boolean warning guard.
Long documentation/comments in edited modules are preserved in UPSTREAM_NOTICES.md, alongside copyright notices.
The quality model, calibration parameters, and Q_JOD mapping are unchanged.

Validated with Python 3.12.4, NumPy 2.4.2, and SciPy 1.17.0.
This packaging release has not been numerically validated against MATLAB/Octave.

## Installation

```bash
python -m pip install 'git+https://github.com/zzt76/HDR-VDP-3.git@v3.0.7.post1'
```

Use an exact Git commit for reproducible experiments. NumPy and SciPy are the only runtime dependencies.

## Example

```python
from VDP3.hdrvdp3 import hdrvdp3

result = hdrvdp3('quality', test_rgb_nits, reference_rgb_nits, 'rgb-bt.709', 30, {})
score = result['Q_JOD']
```

Inputs are matching HWC arrays in absolute linear photometric units, with known RGB primaries.
They are not clipped sRGB or arbitrarily normalized radiance values. Higher Q_JOD means better quality;
identical images score 10. Specify the same viewing geometry for all comparisons.

## Build distributions

```bash
python -m pip install build
python -m build
```

The wheel bundles all spectral calibration data. Preserved upstream notices are included in distributions.
