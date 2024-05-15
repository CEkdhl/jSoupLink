# jsoupLink to-do list

For jsoupLink version 1.0.1.

## Remove dependency on jsoup-\<version\>.jar file name

Near line #21, `jsoupLink/Kernel/jsoupLink.m` hardcodes the file name of the jsoup library. It would be nice if it could just load whatever is in the paclet, erroring out if there is more than one `jsoup-A.B.C.jar` file available.

## Complete documentation, with examples

Lord preserve us …
