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

![](/images/SN2006bo.png)

Second peak:

![](/images/SN2006bo_b.png)

### `DEIMOS_101107_IIn`

Two SN here: `SN_2010jl` and `SN_2009ip`.

The standard star has a really weird aperture... though it's the same as the one for `DEIMOS_060427_IIn`? (`BD174708`)

![](/images/BD174708_weird_aperture.png)

I'm just gonna work with the peak of the graph.

I got the `k` Gaussian to work for a bit. Now it's broken again????

The results aren't terribly interesting either?

![](/images/SN2010jl.png)

`SN_2009ip` is not a nice spectrum. ![](/images/SN09weird.png)

### `DEIMOS_061223_IIn`

~~Double peaked SN (kind of?)~~ Other lines don't have double peak

**note:** used +/- 10 for limits

Again not very interesting...

![](/images/SN2005kd.png)

Okay it looks like the world might end out there so I'd better go home now...

## 6/5

### `DEIMOS_050211_IIn`

No standard star - can't do anything? Do I have to do the first code block in `uvir_lowres` to make it?

### `DEIMOS_051005_IIn`

No standard star or SN fits file in the `redux` folder.

### `DEIMOS_070216_IIn`

Nothing in the `redux` folder.

### `DEIMOS_110801_IIn`

No SN fits file in the `redux` folder, no `uvir_lowres.py` file (though I'm sure I could duplicate one from a different folder).

### `DEIMOS_110929_IIn`

Running into error after tellurics - `ERROR (741, "Cannot open file (home$extinct/maunakeaextinct.dat)")`

I can't see any difference in the code between ones that worked and this one - they all have `extinction='home$extinct/maunakeaextinct.dat'`.

Seems like they're defining home differently. In iPython setting `cd home` brings me to `/Users/mike/iraf` for this directory but to `../CarPy` in the other directory.

Looks like I have to go into `login.cl` (or at least copy it to this directory) - that seems to have worked.

It has some funny looking residuals -
![](/images/110929_weird_residuals.png)

I might want to re-do this just in case.

~~Ran into a `Can't get image SN2010jp.ms.fits` error, so that's off the table. That file is not in the `redux` folder. It's a shame - the `SN_2010jp_01_B.fits` file looks interesting. But it's only trying to remove it - maybe I can just comment that out?~~ jk am dum dum and forgot the `_`

Weird R aperture for line 2050: ![](/images/SN10weird.png)

This is kind of similar to one of the ones from yesterday. Maybe try shifting the line?

Seems like this is coming from the actual fits file:

![SN10Rweird](/images/SN10Rweird.png)

### `DEIMOS_120923_IIn`

`SN_2008en` has the same problem with the apertures.

`SN_2009ip` returned a "segmentation violation" when it tries to do the telluric. Not sure why it's doing this, but it's repeated.

### `DEIMOS_121015_IIn`

Nothing in `redux`.

### `DEIMOS_121115_IIn`

All of the SN files are not useful. For some of them, the `R` or `B` is the only one that's useful, while the other isn't - perhaps I could just ignore those that aren't useful


### `DEIMOS_130217_IIn`

Two standards? `HD19445` and `HD84937`.

Not very interesting again.

![](/images/SN_2013W.png)
