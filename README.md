nclrun
======

By _[Guidi Zhou](http://zhouguidi.github.io)_

Run [NCL](http://www.ncl.ucar.edu) scripts in a better way

NCLRUN allows you to write and run NCL scripts in a better, easier way.

#Features:
1. Allows # started comments.
2. Use [...] as array constructor, instead of the awkward Fortran-style (/.../).
3. Use relative path in `load` statements if loading scripts in the searching path, thus no need to write the very long pathname every time.  
4. A new `with` block to easily set many resources of a same object.
5. Easily pass command line arguments to the script.
6. Run the NCL script like a shell script.

#Usage:
1. Download the `nclrun` script and put it anywhere in your shell's `$PATH`.
2. Put the following line to the begining of your NCL script:
    ```bash
    #!/usr/bin/env nclrun
    ```
3. Add execuate permission to your script:
    ```bash
    $ chmod +x plot.ncl
    ```
4. Run the script the standard way:
    ```bash
    $ ./plot.ncl arg1=val1 arg2:=val2
    ```
   In the script, use `arg1` and `arg2` as normal variables.

   Two ways of passing arguments are supported: `arg=val` and `arg:=val`. Using the first syntax, the `val` will be surrounded by a pair of quotation marks, making `arg` a string. The second way doesn't do this, passing `val` verbosely to `arg`. The first form is good to pass string arguments, and the second should be used to pass integer or floating-point numbers.

#Example:
Write the following script and name it "exp.ncl"
```
#!/usr/bin/env nclrun
# loading scripts in the standard $NCARG_ROOT path is very easy.
load "time_axis_labels"
                                          
# two variables are made usable by command line arguments,
# a string named "fn", and an integer named "id"
f = addfile(fn + sprinti("%i", id), "r")

res = True
# using a "with ... end with" block to easily set resources
with res
    gsnFrame = False
    gsnDraw = False
    gsnAddCyclic = False
    gsnStringFontHeightF = 14
    gsnCenterString = ""
    gsnLeftString = ""
    gsnRightString = ""
    tiMainString = ""
    cnFillOn = True
    mpMaxLatF = 60
    mpMaxLonF = 260
    mpMinLatF = 20
    mpMinLonF = 120
    mpCenterLonF = 190
    mpFillOn = False
    cnLinesOn = False
    cnLevelSelectionMode = "ExplicitLevels"
    cnLevels = [0.5, 1]                  # [...] array constructor
end with

resWk = "eps"
wks = gsn_open_wks(resWk, "fig" + sprinti("%i", id))
gsn_define_colormap(wks, "ncl_default")

var = f->sst(0, :, :)

pl= gsn_csm_contour_map(wks, var, res)
draw(pl)
```

Now run it:
```bash
$ chmod +x exp.ncl
$ ./exp.ncl fn=sstdata id:=1
```
