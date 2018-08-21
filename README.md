# utl_proc_report_with_different_margins_on_even_and_odd_pages
proc report with different margins on even and odd pages.  Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.
    proc report with different margins on even and odd pages

    Just run proc report by page with a different styles for
    odd and even pages.

    github
    https://tinyurl.com/y9htcdbg
    https://github.com/rogerjdeangelis/utl_proc_report_with_different_margins_on_even_and_odd_pages

    SAS forum
    https://tinyurl.com/yau967zr
    https://communities.sas.com/t5/SAS-Programming/Report-with-variable-left-border-margin/m-p/488658


    INPUT
    =====

     TEMPLATES
     ---------

      * EVEN MARGIN = .5 inchesl
      proc template;
         define style styles.evn;
          parent=styles.printer;
           replace body from document /
              leftmargin = .5in
              rightmargin= 0.75in
                topmargin= 1.0in
             bottommargin=_undef_
            pagebreakhtml=html("pagebreakline");
         end;
       run;


      * ODD MARGIN = 1.5 inchesl
      proc template;
         define style styles.odd;
          parent=styles.printer;
           replace body from document /
              leftmargin = 1.5in
              rightmargin= 0.75in
                topmargin= 1.0in
             bottommargin=_undef_
            pagebreakhtml=html("pagebreakline");
         end;
       run;

     FOLDERS
     -------

      Need this folder

      d:/pdf/subset

      and this ghostscript executable here
      d:/pdf/subset/gswin64c.exe


      you can get gswin64c on my github or
      http://www.ghostscript.com/download/gsdnld.html

     DATA  ( with added page variable )
     -----------------------------------

      sashelp,cars(keep=pge make model type msrp)

      WORK.HAVE total obs=428

       PGE    MAKE        MODEL                      TYPE        MSRP

        1     Acura       MDX                        SUV        36945
        1     Acura       RSX Type S 2dr             Sedan      23820
        1     Acura       TSX 4dr                    Sedan      26990
        1     Acura       TL 4dr                     Sedan      33195
        1     Acura       3.5 RL 4dr                 Sedan      43755
        1     Acura       3.5 RL w/Navigation 4dr    Sedan      46100
       ...

        5     Chevrolet   Cavalier LS 2dr            Sedan      16385
        5     Chevrolet   Impala 4dr                 Sedan      21900
        5     Chevrolet   Malibu 4dr                 Sedan      18995
        5     Chevrolet   Malibu LS 4dr              Sedan      20370
        6     Chevrolet   Monte Carlo LS 2dr         Sedan      21825


      EXAMPLE OUTPUT

        d:/pdf/subset/evnOdd1.pdf
        d:/pdf/subset/evnOdd2.pdf
        d:/pdf/subset/evnOdd3.pdf
        d:/pdf/subset/evnOdd4.pdf
        d:/pdf/subset/evnOdd5.pdf


      WORK.LOG total obs=5

      PGE     PDF      RC    CC     STATUS

       1     evnOdd     0     0    Completed
       2     evnOdd     0     0    Completed
       3     evnOdd     0     0    Completed
       4     evnOdd     0     0    Completed
       5     evnOdd     0     0    Completed


     PROCESS
     =======

      1. Execute 'proc report' for each page
      2. Use Evn and Odd styles on even and odd pages respectively
      3. Use ghostscript to append

         d:/pdf/subset/evnOdd1.pdf
         d:/pdf/subset/evnOdd2.pdf
         d:/pdf/subset/evnOdd3.pdf
         d:/pdf/subset/evnOdd4.pdf
         d:/pdf/subset/evnOdd5.pdf

         into

         d:/pdf/subset/evnOdd.pdf

      CODE
      ----

      data log;
        retain pge; retain pdf "evnOdd";
        do pge=1 to 5;

           call symputx('pge',pge);
           if mod(pge-1,2)=0 then call symputx('mrg','evn');
           else call symputx('mrg','odd');

           rc=dosubl('
               ods pdf file="d:/pdf/subset/evnOdd&pge..pdf" style=styles.&mrg;
               proc report data=have(where=(pge=&pge)) nowd missing;
               title "Page &pge of 5";
               run;quit;
               %let cc=&sysrc;
               ods pdf close;
               run;quit;
           ');

            cc=symgetn('cc');
            if rc=0 and cc=0 then status="Completed";
            else status="Failed";
            output;

        end;
        rc=dosubl("
        x 'cd d:/pdf/subset/';
        x 'for %s in (*.pdf) do ECHO %s >> filename.txt';
        x 'gswin64c.exe -q -dNOPAUSE -sDEVICE=pdfwrite -sOutputFile=d:/pdf/subset/evnOdd.pdf -dBATCH @filename.txt';
        ");
        stop;

      run;quit;

    *                _               _       _
     _ __ ___   __ _| | _____     __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \   / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/  | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|   \__,_|\__,_|\__\__,_|

    ;

    proc datasets lib=work kill;
    run;quit;

    * EVEN MARGIN = .5 inches;
    proc template;
       define style styles.evn;
        parent=styles.printer;
         replace body from document /
            leftmargin = .5in
            rightmargin= 0.75in
              topmargin= 1.0in
           bottommargin=_undef_
          pagebreakhtml=html("pagebreakline");
       end;
     run;quit;


    * ODD MARGIN = 1.5 inches;
    proc template;
       define style styles.odd;
        parent=styles.printer;
         replace body from document /
            leftmargin = 1.5in
            rightmargin= 0.75in
              topmargin= 1.0in
           bottommargin=_undef_
          pagebreakhtml=html("pagebreakline");
       end;
    run;quit;

    options leftmargin=.5in noxwait xsync;

    data have;
      retain pge 0;
      set sashelp.cars(keep=make model type msrp);
      if mod(_n_-1,15)=0 then pge=pge+1;
    run;quit;

    * see process;

