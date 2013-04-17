MyTardis Bioformats Image App
=============================

This app provides a Mytardis Filter that performs DM3 image to PNG translation
using the LOCI Bioformats tools.

While only DM3 files are supported for now, Bioformats supports 
[many formats](http://loci.wisc.edu/bio-formats/formats) so potentially 
this app could as well, without to much effort._

Here's an example:

![Screenshot](https://dl.dropbox.com/u/172498/screenshots_host/bioformats-dm3.png)

## Todo
 - Support more files than just dm3
 - Error handling if the dm3 file fails for whatever reason
 - More detailed metadata parameters than simple text output

## Requirements
 - Java Runtime Environment
 - [MyTardis 3.0 Branch](https://github.com/mytardis/mytardis/branches/3.0)
 - http://cvs.openmicroscopy.org.uk/snapshots/bioformats/4.4.6/bftools.zip
 - http://hudson.openmicroscopy.org.uk/job/BIOFORMATS-trunk/lastSuccessfulBuild/artifact/artifacts/loci_tools.jar

## Installation

 - Unzip bftools.zip into some directory on the MyTardis server.  (Lets call it `.../bftools`)
 - Place loci_tools in the `.../bftools` directory
 - Make sure the MyTardis web server user can read the `.../bftools` directory contents

Git clone this repository:
    
       git clone git@github.com:steveandroulakis/mytardis-app-bioformatsimage.git
Symlink the clone into your MyTardis tree in the `tardis/apps` directory

       ln -s .../mytardis-app-bioformatsimage .../mytardis/tardis/apps/bioformatsimage

Add the following to your MyTardis settings file eg. `.../mytardis/tardis/settings.py`

```
MIDDLEWARE_CLASSES = MIDDLEWARE_CLASSES + ('tardis.tardis_portal.filters.FilterInitMiddleware',)

FILTER_MIDDLEWARE = (("tardis.tardis_portal.filters", "FilterInitMiddleware"),)
```

The above enables the filter middleware for all actions.

Then add the definition for this filter.

```
POST_SAVE_FILTERS = [
   ("tardis.tardis_portal.filters.bioformatsimage.bioformatsimage.make_filter",
   ["BIOFORMATS", "http://tardis.edu.au/schemas/bioformats/1",
    ".../bftools/bfconvert",
     ".../bftools/showinf"]),
   ]
```

`cd .../mytardis` and load the parameter schema into the MyTardis database:

```
bin/django loaddata tardis/apps/bioformatsimage/bioformats.json
```

Restart MyTardis. From now on, all dm3 files loaded will have preview images and metadata extracted and stored alongside the file itself.
