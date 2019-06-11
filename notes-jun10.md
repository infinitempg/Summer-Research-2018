# Week of June 10

## What to do next

* Plot the peak wavelengths vs aperture on to the image using `APLPy`
* Get a supernova that has narrower lines (they should be there somewhere, Pat had them before)
* Interpolate the peaks using `spline` or something, compare it to the location of the SN.
  * Can use line of sight velocity from the shift in wavelength to determine the difference in speed.

## Starting off with SN2007K

So obviously this one isn't perfect (since the line is still kind of broad) but I'm going to try it anyways since I already have it.

Line of sight velocity is calculated by:

$$v = \frac{\Delta\lambda}{\lambda_0} c$$

Here is the image:

![](/SN_Images/SN2007K/SN2007K.png)

![](/SN_Images/SN2007K/SN2007K_interp.png)

I can make this a **lot** more efficient but that would be wasting time at this point - later I will make a function to plot both right away without having to double change everything.

I'm not totally convinced that the maximum one is a good measurement but I don't know.

## Looking for other SNs to use?

### SN_2013W

This one isn't quite as long, but still gives a result:

![](/SN_Images/SN_2013W/SN_2013W.png)

![](/SN_Images/SN_2013W/SN_2013W_interp.png)

## 6/11
