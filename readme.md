# How to Guide for UVIR_lowres
*2018 Summer Project, Mike Makmur*

The goal of this project is to determine the "kick speed" of a star which was in a binary system prior to a supernova explosion. We do this by determining the offset in speed between the supernova's HII emissions and the galaxy's HII regions. *(I think)*

## Getting to the files
1. Open XQuartz and open a new xgterm - `xgterm -sb -bg black -fg green` (This gives a dark color scheme as well)
2. Once in xgterm, you need to `cd` to the correct location - in this case the files are all in `/Desktop/Deimos_HDD/CarPy/`. Folders with relevant Type IIn SNe are appended with `_IIn`.

## Editing files
I prefer doing this in Atom, but you could use `vim` or some other text editor inside terminal. Anyways, open up `uvir_lowres.py`, `deimos_carpy.py`, and `iqutils.py` - all three need to be edited.

Inside `uvir_lowres`:
* Change `standard1` to the standard star we are calibrating to (typically starts with HD or BD)
* Set the first code block to `False`
* Set the second code block (`deimos_standard`) to `True`
  * Add `os.chdir('redux')` to the beginning of the code block
* Set the third code block (`deimos_extract`) to `True`
  * Add (and then comment for now) `os.chdir('redux')` to the beginning of the code block
  * Set `science` to be the SN you are studying
    * You can find the name by looking in the first code block.

Inside `deimos_carpy`:
* Change `import pyfits` to `from astropy.io import fits as pyfits`
* In `deimos_standard`: set all `if` statements to `True`

*I will copy the functions `deimos_standard` and `deimos_extract` below, as I've included more print functions so that I know where I am.*

Inside `iqutils`:
* Change `import pyfits` to `from astropy.io import fits as pyfits`

## PyRAF

Now that we are in the correct directory, we can start up PyRAF:
```
source activate iraf27
pyraf --ipython
```
This opens up PyRAF in an iPython console. Now we import our file:
```python
import uvir_lowres
```
It can now begin to run through `deimos_standard` and `deimos_extract` with the appropriate files. Now we walkthrough those procedures.

### Standard Star

####Aperture

Assuming you correctly set `standard1`, you should get a prompt asking to recenter the aperture for the blue portion of the spectrum. Choose `yes`.

Next, you will be asked if you want to resize the apertures. Choose `yes`.

Lastly, you will be asked if you want to edit the apertures. Choose `yes`.

This should now pop open a PyRAF interactive window displaying a gaussian curve. You can zoom in on certain areas of the window using `w-e` on the bottom left of your zoomed in region and then `e` at the top right.

You can now delete the previous aperture (if you want) by pressing `d`, and then create a new one by pressing `n` when your cursor is lined up with the peak of the curve. Be sure to change your upper and lower bounds by typing `:upper 10` and `:lower -10`.

We now want to set the background by pressing `b` to move to that frame. Delete the previous background by pressing `t` and then set new ones to the left and right of the curve with `s-s`. Press `q` to escape this window and then `b` again to ensure the new background level is set correctly.

If everything is done correctly, you can `q` out again. iPython will ask you if you want to trace apertures, choose `yes`. It will then ask if you would like to fit the traced positions interactively, choose `yes`. Last, it will ask if you want to fit the curve to the aperture interactively, choose `yes`. Now we 
