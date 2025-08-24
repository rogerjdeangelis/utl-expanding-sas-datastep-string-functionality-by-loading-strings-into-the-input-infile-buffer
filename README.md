# utl-expanding-sas-datastep-string-functionality-by-loading-strings-into-the-input-infile-buffer
Expanding sas datastep string functionality by loading strings into the input infile buffer
    %let pgm=utl-expanding-sas-datastep-string-functionality-by-loading-strings-into-the-input-infile-buffer;

    %stop_submission;

    Expanding sas datastep string functionality by loading strings into the input infile buffer

    SOAPBOX ON
     Barts macro expands string processing in the datastep.
    SOAPBOX OFF


    PROBLEM
    =======
      Given a string  below.
      We want the first string after "given" and then search the remaining text for the string after "family".

      [{"name": [{"given": ["TESTG1 "], "given": ["TESTG2 "], "family": "TESTF1", "family": "TESTF2"}]
      we want               ======                                      =======

      OUTPUT
      ------

      WANT

      AFTER     AFTER
      GIVEN     FAMILY

      TESTG1    TESTF1

    CONTENTS

        1 Barts improved loadinfileB macro
          Bartosz Jablonski
          yabwon@gmail.com
          https://github.com/yabwon

        2 The loadinfileB macro

    github
    https://tinyurl.com/2aj6w6w4
    https://github.com/rogerjdeangelis/utl-expanding-sas-datastep-string-functionality-by-loading-strings-into-the-input-infile-buffer


    Related
    communities.sas
    https://tinyurl.com/32n9webd
    https://communities.sas.com/t5/SAS-Programming/Extract-words-between-strings-characters/m-p/760130#M240335
    original infile trick;
    https://tinyurl.com/yjzm497y
    https://communities.sas.com/t5/SAS-Programming/How-to-delimit-large-dataset-28-Million-rows-into-700-variables/m-p/487676

    github
    https://tinyurl.com/3j3npc6s
    https://github.com/rogerjdeangelis/utl-expanding-sas-string-functionality-by-loading-strings-into-the-infile-buffer

    stackoverflow r
    https://tinyurl.com/23t8r326
    https://stackoverflow.com/questions/79447615/splitting-one-column-to-three-columns-for-uneven-characters-in-r


    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    data have;

    A='[{"name": [{"given": ["TESTG1 "], "given": ["TESTG2 "], "family": "TESTF1", "family": "TESTF2"}], "identifier": [{"value": "TESTNO", "system": "TESTSTRING"}]
    output;

    A='[{"name": [{"given": ["TESTG "], "family": "TESTF "}], "identifier": [{"value": "TESTNO", "system": "TESTSTRING"}]}]';
    output;

    run;quit;

    /*
     _ __  _ __ ___   ___ ___  ___ ___
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    */

    data want ;

      set have;

      * load _infile_ buffer with variable str;
      %loadinfileB(a  , lrecl=255);

      input @'given' +5 aft_given $6. @'family' +4 aft_family $6. @;
      output;

      * restart the buffer;
      input @1 @@;
    run;quit;

    /*           _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    */

    /**************************************************************************************************************************/
    /*    AFT_      AFT_                                                                                                      */
    /*  GIVEN     FAMILY                                                                                                      */
    /*                                                                                                                        */
    /*  TESTG1    TESTF1                                                                                                      */
    /*  TESTG     TESTF                                                                                                       */
    /**************************************************************************************************************************/

    /*                 _ _        __ _ _      _
    | | ___   __ _  __| (_)_ __  / _(_) | ___| |__
    | |/ _ \ / _` |/ _` | | `_ \| |_| | |/ _ \ `_ \
    | | (_) | (_| | (_| | | | | |  _| | |  __/ |_) |
    |_|\___/ \__,_|\__,_|_|_| |_|_| |_|_|\___|_.__/

    */

    filename ft15f001 "c:/oto/loadinfileb.sas";
    parmcards4;
    %macro loadinfileB(str,lrecl=32767)
      /des="load string into the infile buffer";
     %local rc ff filrf fid;
     %let ff = %sysfunc(datetime());
     %let rc=%sysfunc(filename(filrf
       ,%sysfunc(pathname(WORK))/empty&ff..txt));
     %let fid=%sysfunc(fopen(&filrf., a));
     %if &fid. > 0 %then
       %do;
          %let rc=%sysfunc(fwrite(&fid));
          %let rc=%sysfunc(fclose(&fid));
          infile
            "%sysfunc(pathname(WORK))/empty&ff..txt"
             lrecl=&lrecl.;
          input @; _infile_ = &str;
       %end;
     %else
        %do;
          putLOG "ERROR:&sysmacroname. Something";
          putlog "went wrong with the temp file...";
          stop;
        %end;
    %mend loadinfileB;
    ;;;;
    run;quit;


    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
