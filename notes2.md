# Notes (v2)

## Edits to make to the `README`

* Define:
  * Aperture (the `y` pixel on the `*.fits` file)
  * Line (the `x` pixel on the fits file)
* Goal for SN object:
  1. Set the line to be aligned to the center of the H $\alpha$ emission line at different apertures on the line, then center the aperture.
  2. Remove the background with `b` and then `t`.
  3. **DO NOT TRACE APERTURE** after centering the aperture.
  4. If anything else fails, that's okay. Heck, you don't even need the `R` image, probably. You need the `s(SN)_B.ms.fits` file. Open that in `splot`.
  5. Measure both the maximum point of the H $\alpha$ line as well as the Gaussian fit maximum point (using `k`).
  6. Try to backup the files after making the spectrum.

## 6/6

Working from home today. VNC is a bit laggy on the windows computer but it will do.

### `DEIMOS_070122_IIn` - SN 2007K

Doing apertures between $800$ and $940$ with $\pm 10 \text{px}$. Skipping over apertures centered at $860$ and $880$ as they overlap with the continuum.

## 6/7

I think I'm having a problem because they're all exactly the same. Probably need to make sure I'm clobbering.

Yup I wasn't clobbering. Ok here we go:

![](/images/sn07gauss.png)![](/images/sn07max.png)

~~I think instead of doing it linearly across the image, should just do the big peaks.~~ This didn't really make a difference:

![](/images/sn07gauss2.png)![](/images/sn07max2.png)

## What to do next

* Plot the peak wavelengths vs aperture on to the image using `APLPy`
* Get a supernova that has narrower lines (they should be there somewhere, Pat had them before)
* Interpolate the peaks using `spline` or something, compare it to the location of the SN.
  * Can use line of sight velocity from the shift in wavelength to determine the difference in speed.
