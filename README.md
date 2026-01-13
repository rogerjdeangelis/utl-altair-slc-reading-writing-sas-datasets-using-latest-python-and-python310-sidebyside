# utl-altair-slc-reading-writing-sas-datasets-using-latest-python-and-python310-sidebyside
Altair slc reading writing sas datasets using latest python and python310 sidebyside
    %let pgm=utl-altair-slc-reading-writing-sas-datasets-using-latest-python-and-python310-sidebyside;

    %stop_submission;

    Altair slc reading writing sas datasets using latest python and python310 sidebyside

    Too long to post here, see github

    github
    https://github.com/rogerjdeangelis/utl-altair-slc-reading-writing-sas-datasets-using-latest-python-and-python310-sidebyside


    PROBLEM
    --------

      All python versions after 310 cannot read or write sas datasets?
      This post provides a way to read and write sas datasets with the latest python.
      The user should consider installing only the packages needed to create sas datasets in python310.
      Only need these packages in python310?
          import pyarrow
          import pandas as pd
      in python310
      You may get a note when installing pyarrow, that the path is not set
      ignore the note, we do not want python310 to be in the path.


    INSTALLING PYTHON314 AND PYTHON310 SIDE BY SIDE
    -----------------------------------------------

     1  Install latest python(currently 314) to read sas datasets using python pyreadstat packsge.
        Reading sas datssets inside pyhton is more flexible then reading sas datassets outside python
     2  Install python310 to write sas datasets created by the latest python


    HOW TO INSTALL PYHTON310 & LATEST PYTHON314 WITHOUT CONFLICTS
    -------------------------------------------------------------

     1  python314: Install latest python version (python324 at the current time)

         a  Create folder d:/py314 for latest version)
         b  https://www.python.org/downloads/release/python-3142/
            do not click on the yellow boxes, go down to 'Windows installer (64-bit)'
         d  download and click on the download 'python-3.14.2-amd64.exe'
         e  choose  'customize installation'
         f  choose next
         g  click on browse
            select d:/py314
         h  install
         g  change directory 'cd d:/py314/scripts'
            pip install "pyreadstat"
            pip install "pandas'
            pip install "numpy'
            pip install 'pyarrow'

     2  pytho310: Install python310 with options to elimianate conflicts

         a  Create folder d:/py310 for latest version)
         b  https://www.python.org/downloads/release/python-3100/  /*-- last python version that supported writing sas datasets ---*/
            do not click on the yellow boxes, go down to 'Windows installer (64-bit)'
         c  download and click on the download 'python-3.10.0-amd64.exe'
         d  choose  'customize installation'
         e  Do i customized install
            Uncheck 'Asscociate files with python'
            Uncheck 'Create shortcuts fro installed applications'
         f  When it asks for a path
            Browse and select d:/py314 click ok
            Next to install
         g  change directory 'cd d:/py310/scripts'
            pip install "numpy==1.26.4"
            pip install "pandas==1.5.3"
            pip install "pyarrow"


    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    data workx.zipcode;
      set sashelp.zipcode(obs=10 keep= zip x y statename);
    run;quit;

    /*---
    WORKX.ZIPCODE total obs=10

    Altair SLC

    Obs      ZIP              Y              X     STATENAME

      1    00501      40.813078     -73.046388    New York
      2    00544      40.813223     -73.049288    New York
      3    00601      18.165950     -66.723627    Puerto Rico
      4    00602      18.383005     -67.186553    Puerto Rico
      5    00603      18.433236     -67.151954    Puerto Rico
      6    00604      18.505289     -67.135899    Puerto Rico
      7    00605      18.436149     -67.151346    Puerto Rico
      8    00606      18.185616     -66.977377    Puerto Rico
      9    00610      18.285948     -67.144161    Puerto Rico
     10    00611      18.287716     -66.797578    Puerto Rico
    ---*/

    /*
     _ __  _ __ ___   ___ ___  ___ ___
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    */

    /*--- PY314 -- READ SAS DATASET AND DO YOUR PROCESSING    ---*/

    options set=PYTHONHOME "D:\py314";
    proc python;
    submit;
    import pyreadstat as ps
    import pyarrow
    import pandas as pd
    zipcode,meta = ps.read_sas7bdat('d:/wpswrkx/zipcode.sas7bdat')
    zipcode.to_parquet('d:/wpswrkx/zipcode.parquet', engine='pyarrow')
    endsubmit;
    run;quit;

    /*--- PY310 CREATE SAS DATASET FROM THE ZIPCODE DATAFRAME ---*/

    options set=PYTHONHOME "D:\py310";
    proc python;
    submit;
    import pyarrow
    import pandas as pd
    zip_loaded = pd.read_parquet('d:/wpswrkx/zipcode.parquet', engine='pyarrow')
    print(zip_loaded)
    endsubmit;
    import python=zip_loaded data=workx.zip_loaded;
    run;quit;

    proc print data=workx.zip_loaded;
    title "From python310";
    run;quit;

    /*           _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    */

    Altair SLC

    LIST: 9:22:39

    The PYTHON Procedure

         ZIP          Y          X    STATENAME
    0  501.0  40.813078 -73.046388     New York
    1  544.0  40.813223 -73.049288     New York
    2  601.0  18.165950 -66.723627  Puerto Rico
    3  602.0  18.383005 -67.186553  Puerto Rico
    4  603.0  18.433236 -67.151954  Puerto Rico
    5  604.0  18.505289 -67.135899  Puerto Rico
    6  605.0  18.436149 -67.151346  Puerto Rico
    7  606.0  18.185616 -66.977377  Puerto Rico
    8  610.0  18.285948 -67.144161  Puerto Rico
    9  611.0  18.287716 -66.797578  Puerto Rico


    ALTAIR SLC FROM PYTHON310

    Obs    ZIP       Y          X         STATENAME

      1    501    40.8131    -73.0464    New York
      2    544    40.8132    -73.0493    New York
      3    601    18.1660    -66.7236    Puerto Rico
      4    602    18.3830    -67.1866    Puerto Rico
      5    603    18.4332    -67.1520    Puerto Rico
      6    604    18.5053    -67.1359    Puerto Rico
      7    605    18.4361    -67.1513    Puerto Rico
      8    606    18.1856    -66.9774    Puerto Rico
      9    610    18.2859    -67.1442    Puerto Rico
     10    611    18.2877    -66.7976    Puerto Rico

    /*
    | | ___   __ _
    | |/ _ \ / _` |
    | | (_) | (_| |
    |_|\___/ \__, |
             |___/
    */

    1                                          Altair SLC       09:36 Tuesday, January 13, 2026

    NOTE: Copyright 2002-2025 World Programming, an Altair Company
    NOTE: Altair SLC 2026 (05.26.01.00.000758)
          Licensed to Roger DeAngelis
    NOTE: This session is executing on the X64_WIN11PRO platform and is running in 64 bit mode

    NOTE: AUTOEXEC processing beginning; file is C:\wpsoto\autoexec.sas
    NOTE: AUTOEXEC source line
    1       +  ï»¿ods _all_ close;
               ^
    ERROR: Expected a statement keyword : found "?"
    NOTE: Library workx assigned as follows:
          Engine:        SAS7BDAT
          Physical Name: d:\wpswrkx

    NOTE: Library slchelp assigned as follows:
          Engine:        WPD
          Physical Name: C:\Progra~1\Altair\SLC\2026\sashelp


    LOG:  9:36:05
    NOTE: 1 record was written to file PRINT

    NOTE: The data step took :
          real time : 0.033
          cpu time  : 0.000


    NOTE: AUTOEXEC processing completed

    1         /*--- PY314 -- READ SAS DATASET AND DO YOUR PROCESSING    ---*/
    2
    3         options set=PYTHONHOME "D:\py314";
    4         proc python;
    5         submit;
    6         import pyreadstat as ps
    7         import pyarrow
    8         import pandas as pd
    9         zipcode,meta = ps.read_sas7bdat('d:/wpswrkx/zipcode.sas7bdat')
    10        zipcode.to_parquet('d:/wpswrkx/zipcode.parquet', engine='pyarrow')
    11        endsubmit;

    NOTE: Submitting statements to Python:


    12        run;quit;
    NOTE: Procedure python step took :
          real time : 0.868
          cpu time  : 0.000


    13
    14        /*--- PY310 CREATE SAS DATASET FROM THE ZIPCODE DATAFRAME ---*/
    15
    16        options set=PYTHONHOME "D:\py310";
    17        proc python;
    18        submit;
    19        import pyarrow
    20        import pandas as pd
    21        zip_loaded = pd.read_parquet('d:/wpswrkx/zipcode.parquet', engine='pyarrow')
    22        print(zip_loaded)
    23        endsubmit;

    NOTE: Submitting statements to Python:

    2                                                                                                                         Altair SLC



    24        import python=zip_loaded data=workx.zip_loaded;
    NOTE: Creating data set 'WORKX.zip_loaded' from Python data frame 'zip_loaded'
    NOTE: Data set "WORKX.zip_loaded" has 10 observation(s) and 4 variable(s)

    25        run;quit;
    NOTE: Procedure python step took :
          real time : 0.924
          cpu time  : 0.031


    26
    27        proc print data=workx.zip_loaded;
    28        title "From python310";
    29        run;quit;
    NOTE: 10 observations were read from "WORKX.zip_loaded"
    NOTE: Procedure print step took :
          real time : 0.010
          cpu time  : 0.000


    ERROR: Error printed on page 1

    NOTE: Submitted statements took :
          real time : 1.884
          cpu time  : 0.109

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
