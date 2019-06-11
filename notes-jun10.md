# Week of June 10

## What to do next

* Plot the peak wavelengths vs aperture on to the image using `APLPy`
* Get a supernova that has narrower lines (they should be there somewhere, Pat had them before)
* Interpolate the peaks using `spline` or something, compare it to the location of the SN.
  * Can use line of sight velocity from the shift in wavelength to determine the difference in speed.

## Starting off with SN2007K

*(070122)*

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

*(130217)*

This one isn't quite as long, but still gives a result:

![](/SN_Images/SN_2013W/SN_2013W.png)

![](/SN_Images/SN_2013W/SN_2013W_interp.png)

## 6/11

I think I've ruled out `060427` (galaxy too short), `061223` (???), and `101107` (SN2009 is jumbled, SN2010B is jumbled). Additionally, `051005` has no SN and both `070216` and `121015` have nothing in their `redux` folder. Have not ruled anything else out yet.

**Question** - Can I still do the SN analysis without worrying about calibrating to the standard star? I would imagine yes but I'm not sure.
