# utl-manipulating-dates-in-sas-r-and-python-sql-adding-time-periods-to-start-dates
Manipulating dates in sas r and python sql adding time periods to start dates
    %let pgm=utl-manipulating-dates-in-sas-r-and-python-sql-adding-time-periods-to-start-dates;

     Manipulating dates in sas r and python sql adding time periods to start dates

      SOLUTIONS
           1 r sql
           2 sas sql
           3 python sql
           4 Reposs with a combination of sas, python and r

      github
      https://tinyurl.com/2khc6prs
      https://github.com/rogerjdeangelis/utl-manipulating-dates-in-sas-r-and-python-sql-adding-time-periods-to-start-dates

      stackoverflow python
      https://tinyurl.com/nbz4pmjc
      https://stackoverflow.com/questions/78986437/add-variable-monthly-increments-to-a-datetime-column-in-pandas-using-another-c

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    /**************************************************************************************************************************/
    /*  ADD MONTHS RO DATE                |                                                                                   */
    /*                                    |                                                                                   */
    /*        INPUT                       |       OUTPUT                                                                      */
    /*                                    |                                                                                   */
    /*  SD1.HAVE total obs=5              |  ADD INC MONTHS TO DATE                                                           */
    /*                                    |                                                                                   */
    /*   Obs    IDX    INC       DATE     |                                                                                   */
    /*                                    |                                                                                   */
    /*    1      0      1     2018-12-01  |  +1 month  =  2019-01-01                                                          */
    /*    2      1      2     2018-10-01  |  +2 months =  2018-12-01                                                          */
    /*    3      2      3     2018-12-01  |  +3 months =  2019-03-01                                                          */
    /*    4      3      4     2018-01-01  |  +4 months =  2018-05-01                                                          */
    /*    5      4      5     2018-06-01  |  +5 months =  2018-11-01                                                          */
    /*                                    |                                                                                   */
    /**************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
     input idx 2. inc 2. date $10.;
    cards4;
    0 1 2018-12-01
    1 2 2018-10-01
    2 3 2018-12-01
    3 4 2018-01-01
    4 5 2018-06-01
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* SD1.HAVE total obs=5                                                                                                   */
    /*                       DATE_PLUS                                                                                        */
    /*  Obs    IDX    INC    _MONTHS                                                                                          */
    /*                                                                                                                        */
    /*   1      0      1     2018-12-01                                                                                       */
    /*   2      1      2     2018-10-01                                                                                       */
    /*   3      2      3     2018-12-01                                                                                       */
    /*   4      3      4     2018-01-01                                                                                       */
    /*   5      4      5     2018-06-01                                                                                       */
    /*                                                                                                                        */
    /* PYTHON                                                                                                                       */
    /*                                                                                                                        */
    /*   Variables in Creation Order                                                                                          */
    /*                                                                                                                        */
    /*  #    Variable    Type    Len                                                                                          */
    /*                                                                                                                        */
    /*  1    IDX         Num       8                                                                                          */
    /*  2    INC         Num       8                                                                                          */
    /*  3    DATE        Char     10   R AND PYTHON SQL EXPECT A POSIX CHARACTER DATA                                         */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                    _
    / |  _ __   ___  __ _| |
    | | | `__| / __|/ _` | |
    | | | |    \__ \ (_| | |
    |_| |_|    |___/\__, |_|
                       |_|
    */

    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(sqldf)
    source("c:/oto/fn_tosas9x.r")
    have<-read_sas("d:/sd1/have.sas7bdat")
    str(have)
    have$DATE<-as.Date(have$DATE)
    want <- sqldf("
      select
         idx
        ,inc
        ,date
        ,date(date + 2440588, '+'||inc||' month') as date_plus_one_month
      from
         have
    ")
    str(want)
    print(want)
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="rwant"
         )
    ;;;;
    %utl_rendx;

    libname sd1 "d:/sd1";
    proc print data=sd1.rwant;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  R                                                                                                                     */
    /*                                                                                                                        */
    /*  > print(want)                                                                                                         */
    /*    IDX INC       DATE date_plus_one_month                                                                              */
    /*  1   0   1 2018-12-01          2019-01-01                                                                              */
    /*  2   1   2 2018-10-01          2018-12-01                                                                              */
    /*  3   2   3 2018-12-01          2019-03-01                                                                              */
    /*  4   3   4 2018-01-01          2018-05-01                                                                              */
    /*  5   4   5 2018-06-01          2018-11-01                                                                              */
    /*                                                                                                                        */
    /*  > str(want)                                                                                                           */
    /*  'data.frame':	5 obs. of  4 variables:                                                                                  */
    /*   $ IDX                : num  0 1 2 3 4                                                                                */
    /*   $ INC                : num  1 2 3 4 5                                                                                */
    /*   $ DATE               : Date, format: "2018-12-01" "2018-10-01"  NOTE DATE FORMAT                                     */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*  SAS                                                                                                                   */
    /*                                                                                                                        */
    /*  MM/DD/YY IS DEFAULT STATTRANSFER DATE FORMAT YOU CAN EDIT THE CONFIG FOR OTHER FORMATS                                */                                                                                      */
    /*                                                                                                                        */
    /*                                         DATE_PLUS_                                                                     */
    /*   ROWNAMES    IDX    INC        DATE    ONE_MONTH                                                                      */
    /*                                                                                                                        */
    /*       1        0      1     12/01/18    2019-01-01                                                                     */
    /*       2        1      2     10/01/18    2018-12-01                                                                     */
    /*       3        2      3     12/01/18    2019-03-01                                                                     */
    /*       4        3      4     01/01/18    2018-05-01                                                                     */
    /*       5        4      5     06/01/18    2018-11-01                                                                     */
    /*                                                                                                                        */
    /*   CONTENTS                                                                                                             */
    /*                                                                                                                        */
    /*   Variable               Type    Len    Format                                                                         */
    /*                                                                                                                        */
    /*   ROWNAMES               Num       3                                                                                   */
    /*   IDX                    Num       3                                                                                   */
    /*   INC                    Num       3                                                                                   */
    /*   DATE                   Num       8    MMDDYY8. SAS NUMERIC DATE                                                      */
    /*   DATE_PLUS_ONE_MONTH    Char     10                                                                                   */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___                              _
    |___ \   ___  __ _ ___   ___  __ _| |
      __) | / __|/ _` / __| / __|/ _` | |
     / __/  \__ \ (_| \__ \ \__ \ (_| | |
    |_____| |___/\__,_|___/ |___/\__, |_|
                                    |_|
    */
     proc sql;
       create
         table saswant as
       select
        idx
       ,inc
       ,date
       ,intnx('month', input(date,yymmdd10.), 1) as date_plus_one_month format=yymmdd10.
      FROM
        sd1.have
    ;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* SASWANT with formatted variables                                                                                       */
    /*                                                                                                                        */
    /*                              DATE_PLUS_                                                                                */
    /*  IDX    INC       DATE       ONE_MONTH                                                                                 */
    /*                                                                                                                        */
    /*   0      1     2018-12-01    2019-01-01                                                                                */
    /*   1      2     2018-10-01    2018-11-01                                                                                */
    /*   2      3     2018-12-01    2019-01-01                                                                                */
    /*   3      4     2018-01-01    2018-02-01                                                                                */
    /*   4      5     2018-06-01    2018-07-01                                                                                */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*   Variable               Type    Len    Format                                                                         */
    /*                                                                                                                        */
    /*   IDX                    Num       8                                                                                   */
    /*   INC                    Num       8                                                                                   */
    /*   DATE                   Char     10                                                                                   */
    /*   DATE_PLUS_ONE_MONTH    Num       8    YYMMDD10.                                                                      */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*____               _   _                             _
    |___ /   _ __  _   _| |_| |__   ___  _ __    ___  __ _| |
      |_ \  | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | |
     ___) | | |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | |
    |____/  | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_|
            |_|    |___/                                |_|
    */
    %utl_pybeginx;
    parmcards4;
    import pyarrow.feather as feather
    import tempfile
    import pyperclip
    import os
    import sys
    import subprocess
    import time
    import pandas as pd
    import pyreadstat as ps
    import numpy as np
    from pandasql import sqldf
    mysql = lambda q: sqldf(q, globals())
    from pandasql import PandaSQL
    pdsql = PandaSQL(persist=True)
    sqlite3conn = next(pdsql.conn.gen).connection.connection
    sqlite3conn.enable_load_extension(True)
    sqlite3conn.load_extension('c:/temp/libsqlitefunctions.dll')
    mysql = lambda q: sqldf(q, globals())
    exec(open('c:/oto/fn_tosas9x.py').read())
    have, meta = ps.read_sas7bdat("d:/sd1/have.sas7bdat")
    print(have)
    have.info()
    want=pdsql("""
      select
          idx
         ,inc
         ,date
         ,date(date, '+'||inc||' month') as date_plus_one_month
      from
          have
       """)
    print(want)
    fn_tosas9x(want,outlib="d:/sd1/",outdsn="pywant",timeest=3)
    ;;;;
    %utl_pyendx;

    libname sd1 "d:/sd1";
    proc print data=sd1.pywant;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  PYTHON                                                                                                                */
    /*                                                                                                                        */
    /*   IDX  INC        DATE date_plus_one_month                                                                             */
    /*   0.0  1.0  2018-12-01          2019-01-01                                                                             */
    /*   1.0  2.0  2018-10-01          2018-12-01                                                                             */
    /*   2.0  3.0  2018-12-01          2019-03-01                                                                             */
    /*   3.0  4.0  2018-01-01          2018-05-01                                                                             */
    /*   4.0  5.0  2018-06-01          2018-11-01                                                                             */
    /*                                                                                                                        */
    /*  SAS                                                                                                                   */
    /*                                     DATE_PLUS_                                                                         */
    /*  Obs    IDX    INC       DATE       ONE_MONTH                                                                          */
    /*                                                                                                                        */
    /*   1      0      1     2018-12-01    2019-01-01                                                                         */
    /*   2      1      2     2018-10-01    2018-12-01                                                                         */
    /*   3      2      3     2018-12-01    2019-03-01                                                                         */
    /*   4      3      4     2018-01-01    2018-05-01                                                                         */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*
     _ __ ___ _ __   ___  ___
    | `__/ _ \ `_ \ / _ \/ __|
    | | |  __/ |_) | (_) \__ \
    |_|  \___| .__/ \___/|___/
             |_|
    */

    https://github.com/rogerjdeangelis/utl-add-zero-counts-for-missing-category-combinations-sas-sparse-completetypes-sql-r-python
    https://github.com/rogerjdeangelis/utl-analyzing-mean-and-median-by-groups-in-sas-wps-r-python
    https://github.com/rogerjdeangelis/utl-bigint-longint-in-sas-r-and-python
    https://github.com/rogerjdeangelis/utl-calculate-regression-coeficients-in-base-sas-fcmp-proc-reg-r-and-python
    https://github.com/rogerjdeangelis/utl-change-from-baseline-using-sql-in-sas-r-and-python
    https://github.com/rogerjdeangelis/utl-chiSquare-anaysis-in-sas-r-python-using-matrix-algebra-procs-and-sql
    https://github.com/rogerjdeangelis/utl-classic-transpose-by-index-variableid-and-value-in-sas-r-and-python
    https://github.com/rogerjdeangelis/utl-compute-the-mean-by-group-using-sas-wps-python-r-native-code-and-sql
    https://github.com/rogerjdeangelis/utl-converting-sas-proc-rank-to-wps-python-r-sql
    https://github.com/rogerjdeangelis/utl-converting-sas-proc-sql-code-to-r-and-python
    https://github.com/rogerjdeangelis/utl-create-a-simple-n-percent-clinical-table-in-r-sas-wps-python-output-pdf-rtf-xlsx-html-list
    https://github.com/rogerjdeangelis/utl-create-tables-from-xml-files-using-sas-wps-r-and-python
    https://github.com/rogerjdeangelis/utl-creating-spss-tables-from-a-sas-datasets-using-sas-r-and-python
    https://github.com/rogerjdeangelis/utl-determinating-gender-from-firstname-AI-sas-r-and-python
    https://github.com/rogerjdeangelis/utl-drop-down-using-dosubl-from-sas-datastep-to-wps-r-perl-powershell-python-msr-vb
    https://github.com/rogerjdeangelis/utl-examples-of-drop-downs-from-sas-to-wps-r-microsoftR-python-perl-powershell
    https://github.com/rogerjdeangelis/utl-how-to-sum-a-variable-by-group-in-sas-r-and-python-using-sql
    https://github.com/rogerjdeangelis/utl-importing-sas-tables-sas7bdats-and-sas7bcats-into-python-and-r-with-associared-format-catalogs
    https://github.com/rogerjdeangelis/utl-last-value-carried-backwards-using-mutate-dow-sql-in-wps-sas-r-python
    https://github.com/rogerjdeangelis/utl-left-join-two-datasets-to-a-master-dataset-native-and-sql-using-wps-sas-r-and-python
    https://github.com/rogerjdeangelis/utl-leveraging-your-knowledge-of-perl-regex-to-sas-wps-r-python-and-perl
    https://github.com/rogerjdeangelis/utl-merging-two-tables-without-any-common-column-data-in-r-python-and-sas
    https://github.com/rogerjdeangelis/utl-minimmum-code-to-transpose-and-summarize-a-skinny-to-fat-with-sas-wps-r-and-python
    https://github.com/rogerjdeangelis/utl-monty-hall-problem-r-sas-python
    https://github.com/rogerjdeangelis/utl-mysql-queries-without-sas-using-r-python-and-wps
    https://github.com/rogerjdeangelis/utl-native-r-and-r-python-sas-sql-calculate-the-standard-deviation-of-all-columns
    https://github.com/rogerjdeangelis/utl-overall-frequency-of-values-over-all-columns-and-rows-simutaneously-sas-r-python
    https://github.com/rogerjdeangelis/utl-partial-key-matching-and-luminosity-in-gene-analysis-sas-r-python-postgresql
    https://github.com/rogerjdeangelis/utl-partial-sql-join-based-on-a-column-value-in-wps-sas-r-and-python
    https://github.com/rogerjdeangelis/utl-passing-r-python-and-sas-macro-vars-to-sqllite-interface-arguments
    https://github.com/rogerjdeangelis/utl-pivot-long-transpose-three-arrays-of-size-three-sas-r-python-sql
    https://github.com/rogerjdeangelis/utl-python-r-and-sas-sql-solutions-to-add-missing-rows-to-a-data-table
    https://github.com/rogerjdeangelis/utl-python-r-import-a-subset-of-SAS-columns-from-sas7bdat-and-v5-export-files
    https://github.com/rogerjdeangelis/utl-r-python-sas-sqlite-subtracting-the-means-of-a-specific-column-from-other-columns
    https://github.com/rogerjdeangelis/utl-read-print-file-backwards-in-perl-powershell-sas-r-and-python
    https://github.com/rogerjdeangelis/utl-sas-fcmp-hash-stored-programs-python-r-functions-to-find-common-words
    https://github.com/rogerjdeangelis/utl-sas-proc-transpose-in-sas-r-wps-python-native-and-sql-code
    https://github.com/rogerjdeangelis/utl-sas-proc-transpose-wide-to-long-in-sas-wps-r-python-native-and-sql
    https://github.com/rogerjdeangelis/utl-transpose-fat-to-skinny-pivot-longer-in-sas-wps-r-pythonv
    https://github.com/rogerjdeangelis/utl-update-master-using-transaction-data-using-sql-in-sas-r-and-python
    https://github.com/rogerjdeangelis/utl-using-column-position-instead-of-excel-column-names-due-to-misspellings-sas-r-python
    https://github.com/rogerjdeangelis/utl-very-simple-sql-join-and-summary-in-python-r-wps-and-sas
    https://github.com/rogerjdeangelis/utl-append-rows-and-merge-with-reference-table-in-sql-wps-r-and-python                           
    https://github.com/rogerjdeangelis/utl-calculate-percentage-by-group-in-wps-r-python-excel-sql-no-sql                               
    https://github.com/rogerjdeangelis/utl-calculating-median-values-by-group-wps-r-python-sql                                          
    https://github.com/rogerjdeangelis/utl-calculations-involving-a-sql-self-join-in-wps-r-and-python                                   
    https://github.com/rogerjdeangelis/utl-cartesian-join-with-condition-in-sql-wps-r-python                                            
    https://github.com/rogerjdeangelis/utl-change-from-baseline-to-week1-to-week8-using-wps-r-python-base-and-sql                       
    https://github.com/rogerjdeangelis/utl-common-products-sold-by-two-stores-split-string-pivot-long-sql-wps-r-and-python              
    https://github.com/rogerjdeangelis/utl-compute-the-mean-by-group-using-sas-wps-python-r-native-code-and-sql                         
    https://github.com/rogerjdeangelis/utl-converting-multiple-columns-from-numeric-to-character-in-sql-wps-r-python                    
    https://github.com/rogerjdeangelis/utl-converting-sas-proc-rank-to-wps-python-r-sql                                                 
    https://github.com/rogerjdeangelis/utl-create-equally-spaced-values-using-partitioning-in-sql-wps-r-python                          
    https://github.com/rogerjdeangelis/utl-create-new-column-based-on-complex-logic-involving-four-other-columns-in-wps-r-python-sql-nos
    https://github.com/rogerjdeangelis/utl-create-summary-statistics-datasets-in-sql-wps-r-python                                       
    https://github.com/rogerjdeangelis/utl-distance-between-a-point-and-curve-in-sql-and-wps-pythony-r-sympy                            
    https://github.com/rogerjdeangelis/utl-doblebook-hotel-and-same-guest-booked--hotel-same-day-in-different-years-sql-wps-r-py        
    https://github.com/rogerjdeangelis/utl-efficiently-swap-values-between-related-columns-in-wps-r-python-sql-nosql                    
    https://github.com/rogerjdeangelis/utl-exporting-python-panda-dataframes-to-wps-r-using-a-shared-sqllite-database                   
    https://github.com/rogerjdeangelis/utl-frequency-of-duplicated-digits-in-social-security-numbers-in-wps-r-python-sql                
    https://github.com/rogerjdeangelis/utl-group-by-id-an-subtract-fist.date-from-last.date-using-wps-r-and-python-native-and-sql       
    https://github.com/rogerjdeangelis/utl-how-to-compare-one-set-of-columns-with-another-set-of-columns-wps-r-python-sql               
    https://github.com/rogerjdeangelis/utl-last-value-carried-backwards-using-mutate-dow-sql-in-wps-sas-r-python                        
    https://github.com/rogerjdeangelis/utl-left-join-two-datasets-to-a-master-dataset-native-and-sql-using-wps-sas-r-and-python         
    https://github.com/rogerjdeangelis/utl-manipulate-excel-directly-using-passthru-microsoft-sql-wps-r-rodbc                           
    https://github.com/rogerjdeangelis/utl-merging-inner-join-dataframes-based-on-single-primary-key-in-wps-r-python-sql-nosql          
    https://github.com/rogerjdeangelis/utl-mysql-queries-without-sas-using-r-python-and-wps                                             
    https://github.com/rogerjdeangelis/utl-nearest-sales-date-on-or-before-a-commercial-date-using-r-roll-join-and-wps-r-and-python-sql 
    https://github.com/rogerjdeangelis/utl-partial-sql-join-based-on-a-column-value-in-wps-sas-r-and-python                             
    https://github.com/rogerjdeangelis/utl-passing-arguments-to-sqldf-wps-r-sql-functional-sql                                          
    https://github.com/rogerjdeangelis/utl-pivot-long-pivot-wide-transpose-partitioning-sql-arrays-wps-r-python                         
    https://github.com/rogerjdeangelis/utl-pivot-transpose-by-id-using-wps-r-python-sql-using-partitioning                              
    https://github.com/rogerjdeangelis/utl-rename-and-cast-char-to-numeric-using-do-over-arrays-in-natve-and-mysql-wps-r-python         
    https://github.com/rogerjdeangelis/utl-sas-proc-transpose-in-sas-r-wps-python-native-and-sql-code                                   
    https://github.com/rogerjdeangelis/utl-sas-proc-transpose-wide-to-long-in-sas-wps-r-python-native-and-sql                           
    https://github.com/rogerjdeangelis/utl-select-all-unique-pairs-of-ingredients-in-salads-r-wps-r-python-sql                          
    https://github.com/rogerjdeangelis/utl-select-groups-of-rows-having-a-compound-condition-and-further-subset-using-wps-r-python-sql  
    https://github.com/rogerjdeangelis/utl-select-students-that-have-not-transfered-schools-over-the-last-three-years-wps-r-python-sql  
    https://github.com/rogerjdeangelis/utl-select-the-first-two-observations-from-each-group-wps-r-python-sql                           
    https://github.com/rogerjdeangelis/utl-self-join-get-all-combinations-where-left-col-less-then-right-col-sql-wps-r-and-python       
    https://github.com/rogerjdeangelis/utl-set-type-for-subject-based-on-baseline-dose-wps-r-python-sql                                 
    https://github.com/rogerjdeangelis/utl-simple-classic-transpose-pivot-wider-in-native-and-sql-wps-r-python                          
    https://github.com/rogerjdeangelis/utl-simple-conditional-summarization-in-sql-wps-r-and-python-multi-language                      
    https://github.com/rogerjdeangelis/utl-simple-left-join-in-native-r-and-sql-wps-r-and-python                                        
    https://github.com/rogerjdeangelis/utl-top-four-seasonal-precipitation-totals--european-cities-sql-partitions-in-wps-r-python       
    https://github.com/rogerjdeangelis/utl-transpose-pivot-wide-using-sql-partitioning-in-wps-r-python                                  
    https://github.com/rogerjdeangelis/utl-using-sql-in-wps-r-python-select-the-four-youngest-male-and-female-students-partitioning     
    https://github.com/rogerjdeangelis/utl-very-simple-sql-join-and-summary-in-python-r-wps-and-sas                                     


    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
