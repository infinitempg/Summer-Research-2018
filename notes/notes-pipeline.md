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

After all those, I get this:

`[Errno 2] No such file or directory: 'rtd35494_Bf.fits'`

which I think is a result of none of the above commands working.

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
