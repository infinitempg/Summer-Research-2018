# Pipeline Notes - Spring 2020

## Instructions for running the Pipeline

Make sure you're running on the `PyRAF` profile - which runs `. /usr/local/CarPy/Setup.bash && source activate iraf27 && cd Desktop/Deimos-HDD/CarPy/` right away.

If you're downloading new data from the [Keck Archive](https://koa.ipac.caltech.edu/cgi-bin/KOA/nph-KOAlogin), use the `test.sh` script to download, extract, and set up the directory.

### Preliminary

1. Delete all items in the redux folder, and then add `deimos_badmask_3.fits`, `deimos_linelist.dat`, and `skylines.txt`.
    - If you don't clear out the `redux` folder, PyRAF will complain about not specifying FITS extensions or the file already existing
2. Make sure `login.cl` is in the directory!

### Headers

1. Run `read_headers.py` to get all the headers for all the `*.fits` files in `rawdata`.
2. Imported them to Google Sheets to get a better look. Here are the key things I took away (after looking at a few of these) for importing to `uvir_lowres.py`:
    - the `science` images are fairly obvious - the standard stars and SN will have those names listed in the headers (make sure standard is first???)
    - the `flats` should be listed as `LVM` or `LongViewMirror`.
    - the `arc` is generally also listed as `arc`, though some have multiple so it's kind of a guessing game

### Editing Files

For `uvir_lowres.py`:
1. Set first code block to `True` and the rest to `False`.
2. Change `date` to the appropriate date, as listed in the folder title or the files in `rawdata`.
3. List the `flats`, `arc`, and `science` images as appropriate. (`arc_copy` is apparently not needed.) Also change the `update_head` commands to the appropriate science names.
    - For the `science` image you need to have the attributes as `[*name*, 1, 1, 'low.*blue/red*.uvir']`.

For `deimos_carpy.py`:

1. Set `DARCLIST = 'deimos_linelist.dat'`.
2. Set `DSKYLIST = 'skylines.txt'`.
3. Set `MASK = "deimos_badmask"`.

For `iqutils.py`:
1. In the `update_head` function, both the `if` and `else` in the nested `for` to

    ```inf[extn].header[keys[i]] = vals[i]```

2. In the `delete_head` function, change the `for key in keys` loop to
    ```python
    for key in keys:
      try:
        if inf[0].header[key]:
          inf[0].header.remove(key)
      except KeyError:
        print('no key')
    ```

### Analysis

To run the pipeline, you first need to start up PyRAF by entering `pyraf --ipython` into the PyRAF terminal, and then running `import Tee` and `import uvir_lowres`.

So you will annoyingly need to run the pipeline at least once to get to this point. Open up the `FITS` files (should be listed as `d*id*.fits`) for the standard and the SN and look at where the slits are located. The **physical** locations should all match up between these files.

Enter in the slit locations into `deimos_carpy.py` in `DGRATINGS` under the appropriate color (`bslits` or `rslits`). The first number in this list is the number of slits, and the remaining are the beginning and end of each corresponding slit.

Running the pipeline again should get you to where I am currently. I will keep this updated as I figure things out!

## Current State as of 1/21/20

Okay so when we last left off the pipeline was run completely and we ended up with good looking FITS file that didn't have *physical* wavelengths. According to the [wiki](https://github.com/patkel/deimos/wiki/Trouble-Shooting-Notes), I will most likely have to manually play with the `waverect` command:
> Error #4 "Lack of Features"
>
> Brad says: You can play around with the wavelength solution and threshold (-bth, I think).
>
>Also: Sorry for the slow reply, but I don’t have too much to add here. I often have to tweak either the nominal dispersion, or the signal to noise of the lines chosen, or the default wavelength range, to get things to work. Maybe one day will work on making it more robust. Just something to add to the list …
>
>Also: Removing the fainter lines, and throwing out elements in not lamp

So in `uvir_lowres`, the pipeline is in the first code block and it runs `deimos_pipe`, starting with `docalib = True` before then setting `doscience = True`. I think the first step to this is figuring out which step is having the problem.

The `waverect` command is run 4 times, twice in `deimos_docalib` and twice in `deimos_doscience`.

In `deimos_docalib`:
```
run("waverect -xdist -ydist %sf.fits -o 3 -w1 %.2f -w2 %.2f -d0 %.2f -ll %s -arcamp -fwhm %.1f" % (barc.split(".")[0], cwlen - blen * d0, cwlen, d0, DARCLIST, FWHM))
run("waverect -xdist -ydist %sf.fits -o 3 -w1 %.2f -w2 %.2f -d0 %.2f -ll %s -arcamp -fwhm %.1f" % (rarc.split(".")[0], cwlen, cwlen + rlen * d0, d0, DARCLIST, FWHM))
```

In `deimos_doscience`:
```
# Tweak based on sky lines (if possible)
run("waverect -wdist -ydist %sf.fits -ll %s -zero 1 -skyamp" % (bsci.split(".")[0], DSKYLIST))
run("waverect -wdist -ydist %sf.fits -ll %s -zero 1 -skyamp" % (rsci.split(".")[0], DSKYLIST))
```

So according to the [Notes on Dan Kelson's Spectroscopy Software](http://astro.dur.ac.uk/~ams/dan/), the `waverect` command must be run **twice** in order to first calibrate the *lamp* frame (or flat) and then again to calibrate the *science* frame (adjusting the zero-points of the wavelength solutions). It also notes that the `waverect` will fail if the dispersion direction is wrong:
```
note: dispersion direction should be blue on left (can flip with -Fx [-Fy]) if necessary
(otherwise waverect will fail):
overscan ccd019.fits flata1.fits -t LDSS2 -s "(0,1700)"
[-s takes subregion -- can take 2 or 4 positions]
-f for fourier filtering of the median
-m for median filtering
```
I don't seem to see anything regarding this at all in `deimos_carpy` though.

***

You know what, let's just run the pipeline again and check for errors first.

I'll be running it on the `DEIMOS_new_2012115` one. I should probably test it out on one of the old ones as well now that I think about it, but this one is already running.

### Errors for `DEIMOS_new_2012115`

```
HD84937_01_B.fits
Error updating header of HD84937_01_B.fits
HD84937_01_R.fits
Error updating header of HD84937_01_R.fits
```

```
SN_2010jl_01_B.fits
Error updating header of SN_2010jl_01_B.fits
SN_2010jl_01_R.fits
Error updating header of SN_2010jl_01_R.fits
```

So I should probably check the `update_head` function for the standard and science images.

```
waverect -wdist -ydist rtd56993_Bf.fits -ll skylines.txt -zero 1 -skyamp
waverect -wdist -ydist rtd56993_Bf.fits -ll skylines.txt -zero 1 -skyamp
finished
Starting wavelength calibration for 1
Transforming.
Going into wavelength calibration
Dumping temp2_67721.fits [4096]
Dumping temp_67721.fits [4096]
Searching for lines with 5.0 10.0

Found 11 good lines
 1865.86 (  4.86),   3004.55 (  4.95),   3841.81 (  0.00),   3854.94 (  0.00),   3862.11 (  0.00),   3873.93 (  0.00),   3882.00 (  0.00),   3910.15 (  8.59),   3942.04 (  2.36),   3948.59 (  0.74),   3971.20 (  1.88),
[ 5.05213012 -3.78119818]
Cut down to 3 lines
Using FWHM= 4.9469579556 0.0586626132818

 1865.86 (  4.86),   3004.55 (  4.95),   3942.04 (  2.36),


amp
Read 45 lines

Trimming iterations= 1
Solution =
[  5.69539067e+03   1.29870678e+03   6.79348759e+00   9.49590244e-01]
Using   2 features.
Using minamp=  5.000
   Range (fwhm, pix and A) =   4.000  19.788  12.551
UNWT BWT (pix and A) =   0.000   0.000
UNWT RMS (pix and A) =   0.719   0.456
  WT RMS (pix and A) =   0.705   0.447
Traceback (most recent call last):
  File "/usr/local/CarPy/dist/bin_oldnumeric/waverect", line 148, in <module>
    cpd=cpd,pgapop=pgapop,pgamit=pgamit,bth=bth,wzero=wzero,wtol=1.0,verb=verb,wide=wide)
  File "/usr/local/CarPy/dist/lib_oldnumeric/Expector.py", line 35, in wcalib
    tempfits=tempfits,temp2fits=temp2fits,wide=wide)
  File "/usr/local/CarPy/dist/lib_oldnumeric/LineIdentify.py", line 1235, in wavecalib
    res,sol = dispsol(sol,basis,x,p,f,q,lw,ln,lh,lt,lu,wr,fwhm,mamp,wzero,ltype,usep2=usep2,useqr=useqr,verb=verb)
  File "/usr/local/CarPy/dist/lib_oldnumeric/LineIdentify.py", line 558, in dispsol
    raise "LackOfFeaturesError"
TypeError: exceptions must be old-style classes or derived from BaseException, not str
```

This is coming after a `waverect` command. There's multiple ones of these it seems.

The no-physical-coordinate problem has reproduced here as well.

Before I do anything else let's check this on one that has worked in the past.

### Errors for `DEIMOS_060427_IIn`

`IOError: [Errno 2] No such file or directory: 'rtd54452_Bfocr.fits'`

So I didn't change `DARCLIST` and `DSKYLIST` - let's see if that fixes it

Okay so that did fix it. Physical coordinates still not showing up. I did notice that it did show up for the `Flat-R.fits` file but not the `Flat-B.fits` file.

Okay, so the SN image is `54038`. Let's look at the files:

* `d54038`: `B` and `R` both have Physical coordinates (though they don't seem to actually match anything). `PANE` coordinates match the original files (but not the real coordinates?)
* `rtd54038`: `B` is missing Physical coordinates, `R` still has them.
* `rtd50438_*f(m,o,oc)`: Both seem to cut out the top slit. All other properties seem the same. Worth noting, there are a lot of extra lines in the new one compared to the old one.
* `rtd50438_*focr`: The `PANE` coordinates switch to what appear to be the correct wavelengths.

Soooooo I forgot to fix the `update_head` function. Let's see now...

Okay it pumped everything out. Let's see if we can actually analyze it. Still getting a Lack of Features error. And (presumably because of that) the listed wavelengths are wrong. So still on the same page.

Trying with another one to see what's going on:

### Errors for `DEIMOS_061223_IIn`

So this one produced a really nice H-alpha line, and I wanted to try and reproduce it again. In doing so, I took a closer look at the logs and realized that they are using different FWHM for the `waverect`(?)

The original:
```
DE.20061223.09999.fits[3] -> d09999_B.fits
600ZD {'blvmreg': '[295:1390,*]', 'rslits': [4, 172, 334, 380, 532, 596, 760, 810, 973], 'dispersion': 0.65, 'rlvmreg': '[686:1775,*]', 'bslits': [4, 145, 305, 351, 511, 573, 731, 785, 943], 'FWHM': 6.0, 'tiltkey': 'G4TLTWAV'}
```

The attempted reproduction:
```
DE.20061223.09999.fits[3] -> d09999_B.fits
600ZD {'blvmreg': '[295:1390,*]', 'rslits': [4, 172, 334, 380, 532, 596, 760, 810, 973], 'dispersion': 0.65, 'rlvmreg': '[686:1775,*]', 'bslits': [4, 145, 305, 351, 511, 573, 731, 785, 943], 'FWHM': 6.5, 'tiltkey': 'G4TLTWAV'}
```

This apparent change in FWHM leads to the original finding 35 good lines (cut down to 34) while the reproduction only finds 32 good lines (cut down to 31). Consequently, The original uses **5** trimming iterations (what does that mean?) and **5** features, while the attempted reproduction only uses 1 for each - leading to the `LackOfFeaturesError`.

But what's **weird** is that in the `DGRATINGS` the FWHM is 6.5 for both the new and the old??? Where is it changing?

Looking at `DEIMOS_new_2012115` - it's also using only 1 trimming iteration.

Looking at `DEIMOS_060427_IIn` - it only finds 1 good line for some and then for others uses 1 or 3 trimming iterations


***

carpy!

~10s km/s




***

Okay I think it has to do with the `unrect` command instead of the `waverect` command since that's the only place I see *major* discrepancies between the original files and the newly generated ones - specifically in the `*.focr` files, which the `unrect` command generates. I shouldn't have to flag `unrect` with a `-w1` or `-w2` like I see in the online notes since the originals don't, but I do see a change in the `CRVAL1` header:
```
original HD84937_01_B:
CRVAL1  =     4764.99268860881                                                  

new HD84937_01_B:
CRVAL1  =     4397.04377162202
```

Looking in the actual `unrect` command, I see that the `dw` is influenced by `w1`, `w2`, and `NAXIS1`, which are also different (4096 and 4098 respectively). `NAXIS2` is also different (1096 and 1101 respectively).

Maybe I'm using the wrong arc? The original appears to use `DE.20121115.07874.fits` as the arc, I've been using `10250`...

Okay now the value for `CRVAL1 = 4760.65072001206`, which is definitely closer. The `NAXIS1` is 4109 now though??? They look lined up but again there's no physical coordinates listed (though again those physical coordinates don't match up to what the wavelengths are).

I think I need to look at a different one again just to see if the coordinates match up there as well.

***

OH MY GOD IT WAS JUST DELETE HEAD BEING DUMB AHHHHHHHHHHHHHHHHHHHHH
