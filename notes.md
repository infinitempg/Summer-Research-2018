# Notes

## 6/3

### `DEIMOS_070122_IIn`
* Producing error `Couldn't open input file: BD19445_01_B.fits` - causes fatal error.

  Here's the full error:
  ```
  /Users/mike/Desktop/Deimos-HDD/CarPy/DEIMOS_070122_IIn
  BD19445_01_B BD19445_01_R
  Couldn't open input file: BD19445_01_B.fits
  Error reading header of BD19445_01_B.fits
  Find apertures for BD19445_01_B? ('yes'):
  Number of apertures to be found automatically (1):
  Warning: Cannot open image (BD19445_01_B)
  Find apertures for BD19445_01_R? ('yes'):
  Number of apertures to be found automatically (1):
  Warning: Cannot open image (BD19445_01_R)
  rm: aBD19445_01_B.ms.fits: No such file or directory
  Remove telluric and absorption features, save output as a%s.ms.fits (by hitting "i")
  Can't get image BD19445_01_B.ms
  BD19445_01_B.ms / aBD19445_01_B.ms
  rm: telluric.B.BD19445_01_B.fits: No such file or directory
  Warning: Cannot open image (BD19445_01_B.ms)
  Warning: No spectra selected
  Killing IRAF task `imreplace'

  ---------------------------------------------------------------------------
  IrafError                                 Traceback (most recent call last)
  <ipython-input-1-5f57245a2984> in <module>()
  ----> 1 import uvir_lowres

  /Users/mike/anaconda3/envs/iraf27/lib/python2.7/site-packages/pyraf/irafimport.pyc in _irafImport(name, globals, locals, fromlist, level)
      114               str(level))
      115     if _importHasLvlArg:
  --> 116         retval = _originalImport(name, globals, locals, fromlist, level)
      117     else:
      118         # we could assert here that level is default, but it's safe to assume

  /Users/mike/Desktop/Deimos-HDD/CarPy/DEIMOS_070122_IIn/uvir_lowres.py in <module>()
       80     #standards = [["../redux/BD262606_01_B.fits", "low.blue.uvir"], ["../redux/BD284211_02_B.fits", "hi.blue.uvir"], ["../redux/BD262606_01_R.fits", "low.red.uvir"], ["../redux/BD174708_02_R.fits", "hi.red.uvir"]]
       81     # standards = [["../" + redux + "/BD284211_01_B.fits", "low.blue.uvir"], ["../" + redux + "/BD174708_01_R.fits", "low.red.uvir"]]
  ---> 82     deimos_carpy.deimos_standard(standard1)
       83     pyraf.iraffunctions.chdir("../redux")
       84

  /Users/mike/Desktop/Deimos-HDD/CarPy/DEIMOS_070122_IIn/deimos_carpy.pyc in deimos_standard(standard)
      599
      600         iraf.sarith("%s.ms" % bstd, "/", "a%s.ms.fits" % bstd, "telluric.B.%s.fits" % bstd, clobber=True)
  --> 601         iraf.imreplace("telluric.B.%s.fits" % bstd, 1.0, lower=0.0, upper=0.0)
      602
      603

  /Users/mike/anaconda3/envs/iraf27/lib/python2.7/site-packages/pyraf/iraftask.pyc in __call__(self, *args, **kw)
      765
      766     def __call__(self,*args,**kw):
  --> 767         return self.run(*args, **kw)
      768
      769     def __repr__(self):

  /Users/mike/anaconda3/envs/iraf27/lib/python2.7/site-packages/pyraf/iraftask.pyc in run(self, *args, **kw)
      357             if executionMonitor:
      358                 executionMonitor(self)
  --> 359             self._run(redirKW, specialKW)
      360             self._updateParList(save)
      361             if Verbose>1:

  /Users/mike/anaconda3/envs/iraf27/lib/python2.7/site-packages/pyraf/iraftask.pyc in _run(self, redirKW, specialKW)
      809         except irafexecute.IrafProcessError, value:
      810             raise IrafError("Error running IRAF task "+self._name+
  --> 811                             "\n"+str(value))
      812
      813     def _updateParList(self, save=0):

  IrafError: Error running IRAF task imreplace
  IRAF task terminated abnormally
  ERROR (827, "Cannot open image (telluric.B.BD19445_01_B.fits)")
  ```
  I'm going to move on from this folder for now, just wanted to leave a note of it.

### `DEIMOS_060427_IIn`

* ~~error with tellurics?~~ jk i'm dum dum
* apparent problem with creating Gaussian (using `k`) at the very last spectrum?

## 6/4

### `DEIMOS_060427_IIn`

Okay now time to finish out `DEIMOS_060427_IIn`. Other peak is at `x = 3674`. I'm going to try and start again from the beginning though. I'm sticking all of the data in a Google Sheet for now.

Still getting a problem using `k` at the very end for the spectrum.

Not sure what to make of the results - doesn't seem to change much???

### `DEIMOS_101107_IIn`

Two SN here: `SN_2010jl` and `SN_2009ip`.

The standard star has a really weird aperture...

![](/images/1_aperture.png)
