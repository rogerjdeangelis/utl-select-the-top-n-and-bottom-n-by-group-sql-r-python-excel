# utl-select-the-top-n-and-bottom-n-by-group-sql-r-python-excel
Select the top n and bottom n by group base sas and sql r and python 
    %let pgm=utl-select-the-top-n-and-bottom-n-by-group-sql-r-python-excel;

    Select the top n and bottom n by group base sas and sql r and python

      SOLUTIONS
          1 sas sql
          2 r sql
          3 python sql
          4 sqlpartitionx (added descending monotonic also in https://tinyurl.com/y9nfugth)
          5 excel (see https://tinyurl.com/6bh5df4j)

    github
    https://tinyurl.com/mry2h6hf
    https://github.com/rogerjdeangelis/utl-select-the-top-n-and-bottom-n-by-group-sql-r-python-excel


    SOAPBOX ON
      You have to be careful how you use the undocumented monotonic() functon,
      NOTE: i use a subquery in the simplest query.
      select *, monotonic() as seq from sd1.have
      Without any sort flags, triggers, indexes or constraints
      Also note order cannot be used in a subquery in sas proc sql

      It might be useful to just add a row number primary key when working with sql.

    SOAPBOX OFF

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */


    /**************************************************************************************************************************/
    /*                         |                                                        |                                     */
    /*          INPUT          |            PROCESS                                     |             OUTPUT                  */
    /*          =====          |            =======                                     |             ======                  */
    /*                         |                                                        |                                     */
    /* NAME    SEX AGE         |  1 SAS SQL                                             |  NAME    SEX AGE FRO   PARTITION    */
    /*                         |  =========                                             |                                     */
    /* Alice    F   13 1 TOP2  |    proc sql;                                           |  Alice    F   13 TOP2  1 TOP2       */
    /* Barbara  F   13 2 FEMALE|      create                                            |  Barbara  F   13 TOP2  2 FEMALE     */
    /* Carol    F   14         |          table want as                                 |                                     */
    /* Jane     F   12         |      select                                            |  Alfred   M   14 TOP2  1 TOP2       */
    /* Janet    F   15         |          name                                          |  Henry    M   14 TOP2  2 MALES      */
    /* Joyce    F   11         |         ,sex                                           |                                     */
    /* Judy     F   14         |         ,age                                           |  Mary     F   15 BOT2  1 BOT2       */
    /* Louise   F   12 1 BOT2  |         ,'TOP2' as fro                                 |  Louise   F   12 BOT2  2 FEMALE     */
    /* Mary     F   15 2 FEMALE|         ,partition                                     |                                     */
    /* Alfred   M   14 1 TOP2  |      from                                              |  William  M   15 BOT2  1 BOT2       */
    /* Henry    M   14 2 MALE  |          %sqlpartitionx(                               |  Thomas   M   11 BOT2  2 MALES      */
    /* James    M   12         |            sd1.have                                    |                                     */
    /* Jeffrey  M   13         |           ,by=sex)                                     |                                     */
    /* John     M   12         |     where                                              |                                     */
    /* Philip   M   16         |          partition <= 2                                |                                     */
    /* Robert   M   12         |     union                                              |                                     */
    /* Ronald   M   15         |         all                                            |                                     */
    /* Thomas   M   11 1 BOT   |     select                                             |                                     */
    /* William  M   15 2 MALE  |      name                                              |                                     */
    /*                         |      ,sex                                              |                                     */
    /*                         |      ,age                                              |                                     */
    /* options                 |        ,'BOT2' as fro                                  |                                     */
    /*  validvarname=upcase;   |        ,partition                                      |                                     */
    /* libname sd1 "d:/sd1";   |     from                                               |                                     */
    /* data sd1.have;          |         %sqlpartitionx(                                |                                     */
    /* input name$             |           sd1.have                                     |                                     */
    /*       sex$              |          ,by=sex                                       |                                     */
    /*       age$;             |          ,minus=-1)  * NOTICE -1                       |                                     */
    /* cards4;                 |     where                                              |                                     */
    /* Alice F 13              |          partition <= 2                                |                                     */
    /* Barbara F 13            |     ;quit;                                             |                                     */
    /* Carol F 14              |----------------------------------------------------------------------------------------------*/
    /* Jane F 12               |                                                        |                                     */
    /* Janet F 15              |  2-3 R PYTHON EXCEL SQL                                |                                     */
    /* Joyce F 11              |  ===================                                   |     NAME SEX GET2                   */
    /* Judy F 14               |                                                        |                                     */
    /* Louise F 12             |     %utl_rbeginx;                                      |    Alice   F    1                   */
    /* Mary F 15               |     parmcards4;                                        |  Barbara   F    2                   */
    /* Alfred M 14             |     library(haven)                                     |                                     */
    /* Henry M 14              |     library(sqldf)                                     |   Louise   F    8                   */
    /* James M 12              |     source("c:/oto/fn_tosas9x.R")                      |     Mary   F    9                   */
    /* Jeffrey M 13            |     have<-read_sas("d:/sd1/have.sas7bdat")             |                                     */
    /* John M 12               |     print(have)                                        |   Alfred   M    1                   */
    /* Philip M 16             |     want <- sqldf('                                    |    Henry   M    2                   */
    /* Robert M 12             |      select name, sex, get2                            |                                     */
    /* Ronald M 15             |      from (                                            |   Thomas   M    9                   */
    /* Thomas M 11             |       select *,                                        |  William   M   10                   */
    /* William M 15            |        row_number() over                               |                                     */
    /* ;;;;                    |         (partition by sex order by name) as get2,      |                                     */
    /* run;quit;               |        row_number() over                               |                                     */
    /*                         |         (partition by sex order by name desc) as bot   |                                     */
    /*                         |       from have                                        |                                     */
    /*                         |       )                                                |                                     */
    /*                         |      where get2 <= 2 or bot  <= 2                      |                                     */
    /*                         |      order by sex, name                                |                                     */
    /*                         |     ')                                                 |                                     */
    /*                         |     want                                               |                                     */
    /*                         |     fn_tosas9x(                                        |                                     */
    /*                         |           inp    = want                                |                                     */
    /*                         |          ,outlib ="d:/sd1/"                            |                                     */
    /*                         |          ,outdsn ="want"                               |                                     */
    /*                         |          )                                             |                                     */
    /*                         |     ;;;;                                               |                                     */
    /*                         |     %utl_rendx;                                        |                                     */
    /*                         |                                                        |                                     */
    /**************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    options
     validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
    input name$
          sex$
          age$;
    cards4;
    Alice F 13
    Barbara F 13
    Carol F 14
    Jane F 12
    Janet F 15
    Joyce F 11
    Judy F 14
    Louise F 12
    Mary F 15
    Alfred M 14
    Henry M 14
    James M 12
    Jeffrey M 13
    John M 12
    Philip M 16
    Robert M 12
    Ronald M 15
    Thomas M 11
    William M 15
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  NAME       SEX    AGE                                                                                                 */
    /*                                                                                                                        */
    /*  Alice       F     13                                                                                                  */
    /*  Barbara     F     13                                                                                                  */
    /*  Carol       F     14                                                                                                  */
    /*  Jane        F     12                                                                                                  */
    /*  Janet       F     15                                                                                                  */
    /*  Joyce       F     11                                                                                                  */
    /*  Judy        F     14                                                                                                  */
    /*  Louise      F     12                                                                                                  */
    /*  Mary        F     15                                                                                                  */
    /*  Alfred      M     14                                                                                                  */
    /*  Henry       M     14                                                                                                  */
    /*  James       M     12                                                                                                  */
    /*  Jeffrey     M     13                                                                                                  */
    /*  John        M     12                                                                                                  */
    /*  Philip      M     16                                                                                                  */
    /*  Robert      M     12                                                                                                  */
    /*  Ronald      M     15                                                                                                  */
    /*  Thomas      M     11                                                                                                  */
    /*  William     M     15                                                                                                  */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                               _
    / |  ___  __ _ ___    __ _  __ _| |
    | | / __|/ _` / __|  / _` |/ _` | |
    | | \__ \ (_| \__ \ | (_| | (_| | |
    |_| |___/\__,_|___/  \__,_|\__, |_|
                                  |_|
    */

    proc sql;
       create
           table want as
       select
           name
          ,sex
          ,age
          ,'TOP2' as fro
          ,partition
       from
           %sqlpartitionx(
             sd1.have
            ,by=sex)
      where
           partition <= 2
      union
          all
      select
         name
         ,sex
         ,age
         ,'BOT2' as fro
         ,partition
      from
          %sqlpartitionx(
            sd1.have
           ,by=sex
           ,minus=-1)
      where
           partition <= 2
    ;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  NAME       SEX    AGE    FRO     PARTITION                                                                            */
    /*                                                                                                                        */
    /*  Alice       F     13     TOP2        1                                                                                */
    /*  Barbara     F     13     TOP2        2                                                                                */
    /*  Alfred      M     14     TOP2        1                                                                                */
    /*  Henry       M     14     TOP2        2                                                                                */
    /*  Mary        F     15     BOT2        1                                                                                */
    /*  Louise      F     12     BOT2        2                                                                                */
    /*  William     M     15     BOT2        1                                                                                */
    /*  Thomas      M     11     BOT2        2                                                                                */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___                       _   _                                     _
    |___ \   _ __   _ __  _   _| |_| |__   ___  _ __     _____  _____ ___| |
      __) | | `__| | `_ \| | | | __| `_ \ / _ \| `_ \   / _ \ \/ / __/ _ \ |
     / __/  | |    | |_) | |_| | |_| | | | (_) | | | | |  __/>  < (_|  __/ |
    |_____| |_|    | .__/ \__, |\__|_| |_|\___/|_| |_|  \___/_/\_\___\___|_|
                   |_|    |___/
    */


    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(sqldf)
    source("c:/oto/fn_tosas9x.R")
    have<-read_sas("d:/sd1/have.sas7bdat")
    print(have)
    want <- sqldf('
     select name, sex, get2
     from (
       select *,
         row_number() over
          (partition by sex order by name) as get2,
         row_number() over
          (partition by sex order by name desc) as bot
       from have
       )
     where get2 <= 2 or bot  <= 2
     order by sex, name
    ')
    want
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="want"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.want;
    run;quit;

    /**************************************************************************************************************************/
    /*                    |                                                                                                   */
    /*  want              | SAS                                                                                               */
    /*                    |                                                                                                   */
    /*     NAME SEX GET2  | ROWNAMES    NAME       SEX    GET2                                                                */
    /*                    |                                                                                                   */
    /*    Alice   F    1  |     1       Alice       F       1                                                                 */
    /*  Barbara   F    2  |     2       Barbara     F       2                                                                 */
    /*   Louise   F    8  |     3       Louise      F       8                                                                 */
    /*     Mary   F    9  |     4       Mary        F       9                                                                 */
    /*   Alfred   M    1  |     5       Alfred      M       1                                                                 */
    /*    Henry   M    2  |     6       Henry       M       2                                                                 */
    /*   Thomas   M    9  |     7       Thomas      M       9                                                                 */
    /*  William   M   10  |     8       William     M      10                                                                 */
    /*                    |                                                                                                   */
    /**************************************************************************************************************************/

    /*____               _   _                             _
    |___ /   _ __  _   _| |_| |__   ___  _ __    ___  __ _| |
      |_ \  | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | |
     ___) | | |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | |
    |____/  | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_|
            |_|    |___/                                |_|
    */
    proc datasets lib=sd1 nolist nodetails;
     delete pywant;
    run;quit;

    %utl_pybeginx;
    parmcards4;
    exec(open('c:/oto/fn_python.py').read());
    have,meta = ps.read_sas7bdat('d:/sd1/have.sas7bdat');
    want=pdsql('''                                       \
     select name, sex, get2                              \
     from (                                              \
       select *,                                         \
         row_number() over                               \
          (partition by sex order by name) as get2,      \
         row_number() over                               \
          (partition by sex order by name desc) as bot   \
       from have                                         \
       )
     where get2 <= 2 or bot  <= 2
     order by sex, name
       ''');
    print(want);
    fn_tosas9x(want,outlib='d:/sd1/',outdsn='pywant',timeest=3);
    ;;;;
    %utl_pyendx;

    proc print data=sd1.pywant;
    run;quit;


    /**************************************************************************************************************************/
    /*                    |                                                                                                   */
    /*  want              | SAS                                                                                               */
    /*                    |                                                                                                   */
    /*     NAME SEX GET2  |   NAME       SEX    GET2                                                                          */
    /*                    |                                                                                                   */
    /* 0   Alice   F    1 |   Alice       F       1                                                                           */
    /* 1 Barbara   F    2 |   Barbara     F       2                                                                           */
    /* 2  Louise   F    8 |   Louise      F       8                                                                           */
    /* 3    Mary   F    9 |   Mary        F       9                                                                           */
    /* 4  Alfred   M    1 |   Alfred      M       1                                                                           */
    /* 5   Henry   M    2 |   Henry       M       2                                                                           */
    /* 6  Thomas   M    9 |   Thomas      M       9                                                                           */
    /* 7 William   M   10 |   William     M      10                                                                           */
    /*                    |                                                                                                   */
    /**************************************************************************************************************************/

    /*  _               _                  _   _ _   _
    | || |    ___  __ _| |_ __   __ _ _ __| |_(_) |_(_) ___  _ __ __  __
    | || |_  / __|/ _` | | `_ \ / _` | `__| __| | __| |/ _ \| `_ \\ \/ /
    |__   _| \__ \ (_| | | |_) | (_| | |  | |_| | |_| | (_) | | | |>  <
       |_|   |___/\__, |_| .__/ \__,_|_|   \__|_|\__|_|\___/|_| |_/_/\_\
                     |_| |_|
    */

    filename ft15f001 "c:/oto/sqlpartitionx.sas";
    parmcards4;
    %macro sqlpartitionx(dsn,by=team,minus=1)/
       des="Improved sqlpartition that maintains data order";
     ( select
         *
         ,max(seq) as seq
       from
         (select
            *
            %if &minus=1 %then %do;
               %str(,seq-min(seq) + 1 as partition)
            %end;
            %else %do;
               %str(, seq - min(seq) + 1 as partition)
            %end;
         from
           (select *, &minus*monotonic() as seq from sd1.have)
         group
           by &by )
       group
           by &by, seq
       having
           1=1)
    %mend sqlpartitionx;
    ;;;;
    run;quit;


    filename ft15f001 "c:/oto/sqlpartitionx.sas";
    parmcards4;
    %macro sqlpartitionx(dsn,by=team,minus=1)/
       des="Improved sqlpartition that maintains data order";
     ( select
         *
         ,max(seq) as seq
       from
         (select
           *
          ,seq-min(seq) + 1 as partition
         from
           (select *, &minus*monotonic() as seq from &dsn)
         group
           by &by )
       group
           by &by, seq
       having
           1=1)
    %mend sqlpartitionx;
    ;;;;
    run;quit;

    /*                        _             _
     _ __    _____  _____ ___| |  ___  __ _| |
    | `__|  / _ \ \/ / __/ _ \ | / __|/ _` | |
    | |    |  __/>  < (_|  __/ | \__ \ (_| | |
    |_|     \___/_/\_\___\___|_| |___/\__, |_|
                                         |_|
    */

    excel (see https://tinyurl.com/6bh5df4j)

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
