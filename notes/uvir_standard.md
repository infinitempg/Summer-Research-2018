# How to Guide for UVIR_lowres
*2018 Summer Project, Mike Makmur*

The goal of this project is to determine the "kick speed" of a star which was in a binary system prior to a supernova explosion. We do this by determining the offset in speed between the supernova's HII emissions and the galaxy's HII regions. *(I think)*

## Getting to the files
1. Open XQuartz and open a new xgterm - `xgterm -sb -bg black -fg green` (This gives a dark color scheme as well)

**Update:** this is now aliased as `xgterm`. See [here](https://iraf.net/forum/viewtopic.php?showtopic=1469888&fromblock=yes) for details. **Alternatively, you could just skip this and run everything in terminal.**

2. Once in xgterm, you need to `cd` to the correct location - in this case the files are all in `/Desktop/Deimos_HDD/CarPy/`. Folders with relevant Type IIn SNe are appended with `_IIn`.

## Editing files
I prefer doing this in Atom, but you could use `vim` or some other text editor inside terminal. Anyways, go to the folder of the object in the `CarPy` directory and open up `uvir_lowres.py`, `deimos_carpy.py`, and `iqutils.py` - all three need to be edited.

Inside `uvir_lowres`:
* Change `standard1` to the standard star we are calibrating to (this can be found in the `redux` folder, it typically starts with HD or BD)
* Set the first code block to `False`
* Set the second code block (`deimos_standard`) to `True`
  * Add `os.chdir('redux')` to the beginning of the code block
* Set the third code block (`deimos_extract`) to `True`
  * Add (and then comment for now) `os.chdir('redux')` to the beginning of the code block
  * Set `science` to be the SN you are studying
    * You can find this by looking in the `redux` folder as well.

Inside `deimos_carpy`:
* Change `import pyfits` to `from astropy.io import fits as pyfits`
* In the beginning of the file, change all `os.environ['keck']` to `os.environ['HOME'] + '/Desktop'`
* In `deimos_standard`: set all `if` statements to `True`

Inside `iqutils`:
* Change `import pyfits` to `from astropy.io import fits as pyfits`

Additionally, you need to make sure that there is a `login.cl` file in the directory. You can get one from the `CarPy` directory.

## PyRAF

**Note:** For best practices, close out PyRAF using `Ctrl-D`, not `Ctrl-Z`.

Now that we are in the correct directory, we can start up PyRAF:
```
source activate iraf27
pyraf --ipython
```

**Note:** If PyRAF complains about `chkupdate` not working, you need to go to your IRAF directory (`~/iraf`) and comment out `chkupdate` in the `login.cl` file.

This opens up PyRAF in an iPython console. Now we import our file:
```python
import uvir_lowres
```
It can now begin to run through `deimos_standard` and `deimos_extract` with the appropriate files. Now we walkthrough those procedures.

### Standard Star

**Aperture**

Assuming you correctly set `standard1`, you should get a prompt asking to recenter the aperture for the blue portion of the spectrum. Choose `yes`.

Next, you will be asked if you want to resize the apertures. Choose `yes`.

Lastly, you will be asked if you want to edit the apertures. Choose `yes`.

This should now pop open a PyRAF interactive window displaying a gaussian curve. You can zoom in on certain areas of the window using `w-e` on the bottom left of your zoomed in region and then `e` at the top right. (`w-a` will return you back to the default view.)

![aperture image](/notes/images/1_aperture.png)


You can now delete the previous aperture (if you want) by pressing `d`, and then create a new one by pressing `n` when your cursor is lined up with the peak of the curve. Be sure to change your upper and lower bounds by typing `:upper ##` and `:lower -##`, and make sure they are the same for both colors.

We now want to set the background by pressing `b` to move to that frame. Delete the previous background by pressing `t` and then set new ones to the left and right of the curve with `s-s` on each side. Press `f` to ensure the new background level is set correctly, then press `q` to escape.

![background image](/notes/images/2_background.png)

If everything is done correctly, you can `q` out again. iPython will ask you if you want to trace apertures, choose `yes`. It will then ask if you would like to fit the traced positions interactively, choose `yes`. Last, it will ask if you want to fit the curve to the aperture interactively, choose `yes`.

Now we can create a fit for this curve by typing `:order 4` (the number can change depending on how high an order you need to fit it well). Press `f` to visually inspect the fit, and if it is good, press `q` to escape the window. PyRAF will then ask if you would like to write the apertures to the database, choose `yes`. It will then ask if you want to extract the spectra, choose `yes`. Lastly, it will ask if you want to clobber the existing output image, select `yes`.

![fitting image](/notes/images/3_fitting.png)

This portion needs to be completed twice, once for the blue portion and once for the red portion.

**Removing Stellar Absorption Features**

After finishing with the apertures, PyRAF will now let us remove absorption features in the spectrum. Press `ENTER` to plot the image. You can again zoom in on the regions with `w-e-e` and remove absorption lines by pressing `x` on each side, and then `r` to force a re-draw.

![stellar features](/notes/images/4_absorption.png)

When you have removed all of the major absorption lines, press `i` to save the image. Be sure to save this image as `a%s.ms.fits`, where `%s` is the name of the FITS file you are working with (e.g. `aBD19445_01_B.ms.fits`). **NOTE:** If you screw this up, you will have to start over.

Use `q` to escape, and then hit `ENTER` to plot the absorption lines alone. We want to remove anything that isn't a telluric line using `x-x`. Listed below are wavelengths where you need to delete lines.

* 3420 → 5500
* 6050 → 6250
* 6360 → 6450
* 6530 → 6840
* 7410 → 7560
* 8410 → 8800

![removing lines](/notes/images/5_removelines.png)

When you have finished removing the stellar features, press `i` to save the image as `telluric.B/R.%s.fits` as color appropriate. You may need to overwrite, choose `yes`. Press `q` to escape.

Repeat this for both colors.

**Bandpasses**

After finishing with the absorption features, PyRAF will ask if you want to edit the bandpasses. Choose `yes`, and it will open up a window. We want to use `d` to delete any boxes that are below the line. When finished, press `q` to save and escape.

![bandpasses](/notes/images/6_bandpasses.png)

Repeat this for both colors.

**Sensitivity Function**

Now PyRAF moves us on to fitting the apertures. Choose `yes` to open the window. We will see two graphs, one for Sensitivity vs Wavelength on top and one for Sensitivity Residuals vs Wavelength on the bottom. Hover your cursor to the bottom graph and start deleting outlying points with `d-p`. It should automatically move your cursor to the next nearest point when you press `d`, but you may need to move it when you have finished with a region. Press `g` to re-fit and re-draw the top graph. If you make a mistake, you can use `u-p` to undelete a point.

![sensitivity](/notes/images/7_sensitivity.png)

When you are finished, press `q` to save and escape. Repeat this for both colors.

### Science Object
Now we move on to our science object. If you have already completed the process for the standard star, make sure to go into `uvir_lowres` and change the second code block to `False`, as well as going into the third code block and adding `os.chdir('redux')` to the front.

First, we need to set our apertures again. This process is identical to the one for the standard star, so I'm not going to rewrite that.

The program then does a lot of heavy lifting until it prompts you to search the telluric function interactively. Choose `yes`, and PyRAF will open a window displaying the telluric function.

**Telluric Function**

We want to minimize the lines on the top graph to as close to flat as possible in the regions selected. Use `x` to shift the features and `y` to scale them until you get them as close to flat as possible. (You can also use `:scale #` and `:shift #` to manually set them.) When finished, press `q` to close out.

![telluric function](/notes/images/8_telluric.png)

**Plot**

PyRAF will pop out a plot of the spectrum. You may need to zoom in a bit as well as connect the two colors together, but in general it should line up nicely. H Alpha should appear between 6500 A and 7000 A. Zoom in to the peak, and determine the highest point by either eyeballing it or using `k` on either side to create a Gaussian. Make sure to mark this down! Then, hit `q` to escape.

![plot](/notes/images/9_plot.png)

### Repeating for other peaks

Before you start anything, open up `uvir_lowres.py` and change the second comment block to `False` and add `os.chdir('redux')` to the top of the third comment block. Additionally, make a copy of your generated science image (`*.ms.fits`) and save it in another folder, so you will have it in case.

Now, go back out to xgterm and enter `ds9 redux/%sci_01_B.fits`, where `%sci` is the name of your science image. This will open up that image into DS9, where you will need to change the scale to something more reasonable (such as -10 to 90 in this case) that way you can look for the H Alpha emissions (should be near top right). Hover over one of the bright blobs and find its X coordinate on the image. Mark this down, close DS9, and reopen PyRAF.

![DS9](/notes/images/10_ds9.png)

Once you're in, hit `ENTER` three times to edit the aperture. Then, type `:line ####`, where `####` is the X coordinate you marked down earlier. This should change the spectrum a bit - zoom in to any of the peaks and begin the same process with each of them. Each of these peaks is redshifted/blueshifted, and thus can help us determine a speed. **Note:** the red band should already be done, so you do not have to re-edit the apertures, but you do have to go through the process.

Again, mark down the peak wavelength, and then close it out and duplicate the `ms.fits` file so it is backed up. Repeat this for all of the peaks.
