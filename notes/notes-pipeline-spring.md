# Pipeline Notes - Spring 2020

## Instructions for running the Pipeline

Make sure you're running on the `PyRAF` profile - which runs `. /usr/local/CarPy/Setup.bash && source activate iraf27 && cd Desktop/Deimos-HDD/CarPy/` right away.

If you're downloading new data from the [Keck Archive](https://koa.ipac.caltech.edu/cgi-bin/KOA/nph-KOAlogin), use the `test.sh` script to download, extract, and set up the directory.

### Preliminary

1. Delete all items in the redux folder, and then add `deimos_badmask_3.fits`, `deimos_linelist.dat`, and `skylines.txt`.
    - If you don't clear out the `redux` folder, PyRAF will complain about not specifying FITS extensions or the file already existing


### Headers

1. Run `read_headers.py` to get all the headers for all the `*.fits` files in `rawdata`.
2. Imported them to Google Sheets to get a better look. Here are the key things I took away (after looking at a few of these) for importing to `uvir_lowres.py`:
    - the `science` images are fairly obvious - the standard stars and SN will have those names listed in the headers
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

### Analysis

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
