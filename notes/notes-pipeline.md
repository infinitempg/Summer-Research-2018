# Pipeline notes

Most of this is written down so I'll copy it to here somewhat sloppily :)

Working on a copy of `070122_IIn` and trying to replicate everything.

## Current errors:

```
sh: copyrect: command not found
sh: copyslit: command not found
sh: flat2d: command not found
sh: copyrect: command not found
sh: waverect: command not found
/bin/sh: skyrect: command not found
```

My `iraf\iraf\bin` directory is an alias to `iraf\iraf\bin.generic`, which is completely empty. `iraf\iraf\bin.macintel` does have a bunch of stuff in it, though.

After all those, I get this:

`[Errno 2] No such file or directory: 'rtd35494_Bf.fits'`

which I think is a result of none of the above commands working. There is a line in the wiki that says: `problem is in creating the *f.fits file ?? what is that? seems to be a bad pixel column` but I don't know what that means.

## Steps I took

### Preliminary

1. Delete all items in the redux folder, and then add `deimos_badmask_3.fits`.
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

For `deimos_carpy.py`:
1. Set `MASK = "deimos_badmask"`. This is why we copied the file to `redux`.

and that's where I am currently.

***

CarPy environment:
`. /usr/local/CarPy/Setup.bash`

Run that *first* and then run `source activate iraf27` and that should solve the commands missing problem.

***

Okay anyways now we're at an error with the `focr.fits` files. I have the error below for the next one.

This one is just really weird all the way back to `f.fits` - only the original `B` one looks okay.

Maybe try a different directory and see what happens???

***

Trying with `060427` now.

Same error :)

```
unrect -wdist -xdist -ydist rtd54452_Bfoc.fits
unrect -wdist -xdist -ydist rtd54452_Bfoc.fits
Traceback (most recent call last):
  File "/usr/local/CarPy/dist/bin_oldnumeric/unrect", line 53, in <module>
    dum1,dum2,wsol,dum3,dum4=readsol(image,"w"+str(islit)+"xfm")
  File "/usr/local/CarPy/dist/lib_oldnumeric/Expector.py", line 76, in readsol
    efn = eval(f[keyroot+"n"])
  File "<string>", line 1, in <module>
NameError: name 'N' is not defined
finished
Traceback (most recent call last):
  File "/usr/local/CarPy/dist/bin_oldnumeric/unrect", line 53, in <module>
    dum1,dum2,wsol,dum3,dum4=readsol(image,"w"+str(islit)+"xfm")
  File "/usr/local/CarPy/dist/lib_oldnumeric/Expector.py", line 76, in readsol
    efn = eval(f[keyroot+"n"])
  File "<string>", line 1, in <module>
NameError: name 'N' is not defined
unrect -wdist -xdist -ydist rtd54452_Rfoc.fits
unrect -wdist -xdist -ydist rtd54452_Rfoc.fits
Traceback (most recent call last):
  File "/usr/local/CarPy/dist/bin_oldnumeric/unrect", line 53, in <module>
    dum1,dum2,wsol,dum3,dum4=readsol(image,"w"+str(islit)+"xfm")
  File "/usr/local/CarPy/dist/lib_oldnumeric/Expector.py", line 76, in readsol
    efn = eval(f[keyroot+"n"])
  File "<string>", line 1, in <module>
NameError: name 'N' is not defined
finished
Traceback (most recent call last):
  File "/usr/local/CarPy/dist/bin_oldnumeric/unrect", line 53, in <module>
    dum1,dum2,wsol,dum3,dum4=readsol(image,"w"+str(islit)+"xfm")
  File "/usr/local/CarPy/dist/lib_oldnumeric/Expector.py", line 76, in readsol
    efn = eval(f[keyroot+"n"])
  File "<string>", line 1, in <module>
NameError: name 'N' is not defined
```

Seems to be based on the `unrect` command. Which might be coming from something before that. Lets look at the fits files.

`Bfoc.fits` is missing the top slit in the new copy. Same with `Bfo.fits` and `Bf.fits`. The regular `rtd54452_B.fits` does have all the slits.

So where does this come into play? We have a `flat2d`, an `efits`, a `copyrect` which isn't tabbed right in line 255 (maybe that's it? it's only for `B` but the `R` is missing the top slit too), and a `waverect` command that all output (?) a `%sf.fits` file.

***

Let's try another :D `130217` this time.

same error.

missing the top 2 of 5 slits. is it only doing 3 slits for these??? This goes all the way back as well. It's got the right number of slits in the `deimos_carpy`.

It's missing `deimos_linelist.dat`, or at least calling from the other directory. Probably have to change location in `deimos_carpy.py`.


***

okay it ran through the whole thing but produced a blank file for the SN? let's look for errors.

Lots of header updating errors, eg
```
Error updating header of DE.20130217.09999.fits
```
It seems to be on all the fits files.

This error also seems to keep appearing:
```
Error reading keyword SKYSUB from DE.20130217.17785.fits
Error reading keyword CRREJ from DE.20130217.17785.fits
```

Here's another error that occurs after a wavelength calibration:
```
waverect -wdist -ydist rtd18650_Bf.fits -ll skylines.txt -zero 1 -skyamp
waverect -wdist -ydist rtd18650_Bf.fits -ll skylines.txt -zero 1 -skyamp
finished
Starting wavelength calibration for 1
Transforming.
Going into wavelength calibration
Traceback (most recent call last):
  File "/usr/local/CarPy/dist/bin_oldnumeric/waverect", line 148, in <module>
    cpd=cpd,pgapop=pgapop,pgamit=pgamit,bth=bth,wzero=wzero,wtol=1.0,verb=verb,wide=wide)
  File "/usr/local/CarPy/dist/lib_oldnumeric/Expector.py", line 35, in wcalib
    tempfits=tempfits,temp2fits=temp2fits,wide=wide)
  File "/usr/local/CarPy/dist/lib_oldnumeric/LineIdentify.py", line 1082, in wavecalib
    tck = splrep(wo,s,s=0,k=1)
  File "/usr/local/CarPy/builds/Darwin.10.14.x86_64/Python.framework/Versions/2.7/lib/python2.7/site-packages/multipack/fitpack.py", line 214, in _curfit
    t,c,o = _fitpack._curfit(x,y,w,xb,xe,k,task,s,t,nest,wrk,iwrk,per)
SystemError: error return without exception set
waverect -wdist -ydist rtd18650_Rf.fits -ll skylines.txt -zero 1 -skyamp
waverect -wdist -ydist rtd18650_Rf.fits -ll skylines.txt -zero 1 -skyamp
finished
```
here's another one which produces a value error
```
unrect -wdist -xdist -ydist rtd18650_Bfoc.fits
unrect -wdist -xdist -ydist rtd18650_Bfoc.fits
finished
Using solution: 9 0 5 2 2
Creating rtd18650_Bfocr.fits from rtd18650_Bfoc.fits
Slit =   1  -- Rows    4 to   50:
Couldn't splrep!
Traceback (most recent call last):
  File "/usr/local/CarPy/dist/bin_oldnumeric/unrect", line 196, in <module>
    mud,good = wavemapping(mud,wxfm)
ValueError: too many values to unpack
```

this seems to repeat for a lot of Files

this is a curious one
```
Starting wavelength calibration for 3
Transforming.
Going into wavelength calibration
Dumping temp2_10863.fits [4096]
Dumping temp_10863.fits [4096]
Searching for lines with 5.0 10.0

Found 1 good lines
 2824.19 (  5.39),
Using FWHM= 5.39280765469 0.0

 2824.19 (  5.39),


amp
Read 45 lines
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
[]
```

this also appears a few times

*****
appears when updating header
```
/Users/mike/anaconda3/envs/iraf27/lib/python2.7/site-packages/pyraf/irafimport.py:116: RuntimeWarning: numpy.dtype size changed, may indicate binary incompatibility. Expected 96, got 88
  retval = _originalImport(name, globals, locals, fromlist, level)
```

doing the headers manually rather than using `header.update` seems to have fixed that problem?
```
inf[extn].header[keys[i]] = vals[i]
```
there's a `focr` problem. i think i ahve to redefine the directory
yup

***

okay now moving on to the one that didn't have pipeline something words are hard

`121015_IIn`

doesn't seem to get rid of the sky lines??

some errors:

```
waverect -wdist -ydist rtd21914_Bf.fits -ll skylines.txt -zero 1 -skyamp
waverect -wdist -ydist rtd21914_Bf.fits -ll skylines.txt -zero 1 -skyamp
finished
Starting wavelength calibration for 1
Transforming.
Going into wavelength calibration
Dumping temp2_24765.fits [4096]
Dumping temp_24765.fits [4096]
Searching for lines with 5.0 10.0

Found 1 good lines
 2721.38 (  4.34),
Using FWHM= 4.33500780028 0.0

 2721.38 (  4.34),


amp
Read 45 lines
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

appears twice - for wavelength calibration for 1 and 3

There's also an error to update the header for the final science files? I'm gonna change the `try` to `if True` and see what happens.

***


edit DGRATINGS bslits rslits with the slit limits using rtd

use keck archive and SN archive to find new SN and find missing rawdata

`sne.space` nope

`https://koa.ipac.caltech.edu/cgi-bin/KOA/nph-KOAlogin`

check `SNHunt` folder


***

Downloading tar balls:
```
mkdir DEIMOS_new_foo
cd DEIMOS_new_foo
mkdir redux rawdata
wget -O wget_foobar.sh *url*
chmod 755 wget_foobar.sh
./wget_foobar.sh
```

Extracting tar balls:
```
ls DEIMOS_*.tar |xargs -n1 tar -xvf
mv KOA_foobar/DEIMOS/raw/*/*.fits.gz ./rawdata
gunzip rawdata/DE.*.fits.gz
```

this is now in a script `test.sh` in the `CarPy` folder


***

ok for `update_head` in `uvir_lowres`, standards do not need the `"STDNAME"` while the science object does.

no 1200ZD? only 600ZD?

ok so `20050211` is giving me that old focr error but i can't figure out why?

`20130408` is complaining about 830 G not being found?

***

okay doing uvirlores science on 20121115 but the wavelengths are not matching? weird. there's no `physical` value showing up in ds9.
