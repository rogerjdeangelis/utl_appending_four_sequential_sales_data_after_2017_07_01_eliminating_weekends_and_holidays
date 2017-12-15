# utl_appending_four_sequential_sales_data_after_2017_07_01_eliminating_weekends_and_holidays
Appending four sequential trading days after 2017/07/01 eliminating weekends and holidays
    Appending four sequential trading days after 2017/07/01 eliminating weekends and holidays

    github
    https://goo.gl/1YJEk6
    https://github.com/rogerjdeangelis/utl_appending_four_sequential_sales_data_after_2017_07_01_eliminating_weekends_and_holidays

    see
    https://goo.gl/bsVpkN
    https://communities.sas.com/t5/General-SAS-Programming/Append-5-consecutive-data-set-in-a-library/m-p/421470


    INPUT
    =====

        365 datasets but we want four of these

                                  RULES
                                  =====

       _20170702.sas7bdat   Exclude Sun  JULY 2nd   (exclude because weekend)
       _20170703.sas7bdat   Select  Mon
       _20170704.sas7bdat   Exclude Tue  JULY 4th   (exclude because holiday)
       _20170705.sas7bdat   Select  Wed
       _20170706.sas7bdat   Select  Thu
       _20170707.sas7bdat   Select  Fri


       WORK.HOLIDAYS  total obs=15

       HOLIDAY             DATE    DAY     DTECHR

       CHRISTMAS      25DEC2017    Mon    _20171225
       COLUMBUS       09OCT2017    Mon    _20171009
       EASTER         17APR2017    Mon    _20170417
       FATHERS        19JUN2017    Mon    _20170619
       HALLOWEEN      31OCT2017    Tue    _20171031
       LABOR          04SEP2017    Mon    _20170904
       MLK            16JAN2017    Mon    _20170116
       MEMORIAL       29MAY2017    Mon    _20170529
       MOTHERS        15MAY2017    Mon    _20170515
       NEWYEAR        02JAN2017    Mon    _20170102
       THANKSGIVING   23NOV2017    Thu    _20171123
       USINDEPENDENCE 04JUL2017    Tue    _20170704
       USPRESIDENTS   20FEB2017    Mon    _20170220
       VALENTINES     14FEB2017    Tue    _20170214
       VETERANS       13NOV2017    Mon    _20171113


    PROCESS (all the code)
    ======================

       %symdel nams / nowarn;
       data want;

         * hard part is creating the meta data list of desired trading/sales days;
         if _n_=0 then do;
           %let rc=%sysfunc(dosubl('
              proc sql;
                 select
                   memname into :nams separated by " "
                 from
                   sashelp.vtable
                 where
                      libname = "WORK" and
                      memname > "_20170701" and  memname < "_20171231"  and
                      substr(put(input(substr(memname,2),yymmdd8.),WEEKDATX17. -l),1,3)
                        not in ("Sat","Sun") and
                      memname not in (select dteChr from holidays)
                 order
                   by memname
              ;quit;
            '));
          end;

          /* %put &=nams NAMS=_20170703 _20170705 _20170706 _20170707  */

          set
             &nams indsname=dsnin;
          dsn=dsnin;
       run;quit;

    OUTPUT
    ======

     WORK.WANT total obs=13

            DSN    SE   NAME       SEX    AGE    HEIGHT    WEIGHT

       WORK._20170703   Alfred      M      14     69.0      112.5
       WORK._20170703   Jane        F      12     59.8       84.5
       WORK._20170703   Louise      F      12     56.3       77.0
       WORK._20170703   William     M      15     66.5      112.0

       WORK._20170705   Barbara     F      13     65.3       98.0
       WORK._20170705   Jeffrey     M      13     62.5       84.0
       WORK._20170705   Philip      M      16     72.0      150.0

       WORK._20170706   Carol       F      14     62.8      102.5
       WORK._20170706   John        M      12     59.0       99.5
       WORK._20170706   Robert      M      12     64.8      128.0

       WORK._20170707   Henry       M      14     63.5      102.5
       WORK._20170707   Joyce       F      11     51.3       50.5
       WORK._20170707   Ronald      M      15     67.0      133.0

    *                _             _       _
     _ __ ___   __ _| | ___     __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ _ \   / _` |/ _` | __/ _` |
    | | | | | | (_| | |  __/  | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\___|   \__,_|\__,_|\__\__,_|

    ;

    data holidays;
     fornat date date9.;
     input  Date date9. day$ Holiday $24. ;
     dteChr=cats('_',put(date,yymmddn8.));
    cards4;
    25DEC2017 Mon CHRISTMAS
    09OCT2017 Mon COLUMBUS
    17APR2017 Mon EASTER
    19JUN2017 Mon FATHERS
    31OCT2017 Tue HALLOWEEN
    04SEP2017 Mon LABOR
    16JAN2017 Mon MLK
    29MAY2017 Mon MEMORIAL
    15MAY2017 Mon MOTHERS
    02JAN2017 Mon NEWYEAR
    23NOV2017 Thu THANKSGIVING
    04JUL2017 Tue USINDEPENDENCE
    20FEB2017 Mon USPRESIDENTS
    14FEB2017 Tue VALENTINES
    13NOV2017 Mon VETERANS
    ;;;;
    run;quit;

    proc print;
    run;quit;
    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    %symdel nams / nowarn;
    data want;

      * hard part is creating the meta data list of desired trading/sales days;
      if _n_=0 then do;
        %let rc=%sysfunc(dosubl('
           proc sql;
              select
                memname into :nams separated by " "
              from
                sashelp.vtable
              where
                   libname = "WORK" and
                   memname > "_20170701" and  memname < "_20171231"  and
                   substr(put(input(substr(memname,2),yymmdd8.),WEEKDATX17. -l),1,3)
                     not in ("Sat","Sun") and
                   memname not in (select dteChr from holidays)
              order
                by memname
           ;quit;
         '));
       end;

       /* %put &=nams NAMS=_20170703 _20170705 _20170706 _20170707  */

       set
          &nams indsname=dsnin;
       dsn=dsnin;
    run;quit;

