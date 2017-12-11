CSS: sascs.css

<!--<div class="three-cols">-->
<!--</div><div class="page-break"></div><font size=32>test</font><div class="three-cols">-->
# SAS Base CheatSheet

## Basics

A **SAS program** consists of DATA steps and PROC steps. 
A **SAS programming step** is comprised of a sequence of statements. Every step has a beginning and ending step boundary. SAS compiles and executes each step independently, based on the step boundaries.

A **SAS program** can also contain global statements, which are outside DATA and PROC steps, and typically affect the SAS session. A **TITLE** statement is a global statement.

DATA step only produces output. PROC step only produces report.

    /* comment */
    
    * comment statement;

##Accessing SAS Libraries

**Work** is a temporary library that is used to store and access SAS data sets for the duration of the session. **Sasuser** and **sashelp** are permanent libraries that are available in every SAS session.

You refer to a SAS library by a library reference name, or libref. A **libref** is a shortcut to the physical location of the SAS files.

    LIBNAME libref 'SAS-library' <options>;

Use **PROC CONTENTS** with **libref._ALL_** to display the contents of a SAS library.Use the **NODS** option in the PROC CONTENTS statement to suppress the descriptor information for each data set.

    PROC CONTENTS DATA=libref._ALL_ NODS;
    RUN;

After associating a libref with a permanent library, you can write a **PROC PRINT** step to display a SAS data set within the library.

    PROC PRINT DATA=libref.SAS-data-set;        
    RUN;

In an interactive SAS session, a libref remains in effect until you cancel it, change it, or end your SAS session. To cancel a libref, you submit a LIBNAME statement with the CLEAR option.

    LIBNAME libref CLEAR;


##Examining SAS Data Sets

The **descriptor portion** contains general information about the data set (such as the data set name and the number of observations) and information about the variable attributes (such as name, type, and length). 

There are two types of variables: character and numeric. 

* A character variable can store any value and can be up to 32,767 characters long. 
* Numeric variables store numeric values in floating point or binary representation in 8 bytes of storage by default. 

Other attributes include formats, informats, and labels. You can use PROC CONTENTS to browse the descriptor portion of a data set.

    PROC CONTENTS DATA=libref.SAS-data-set;
    RUN;

The **data portion** contains the data values. Data values are either character or numeric. 

A missing value is a valid value in SAS. 

* A missing character value is displayed as a blank, and 
* a missing numeric value is displayed as a period. 
 
You can specify an alternate character to print for missing numeric values using the *MISSING=* SAS system option. You can use PROC PRINT to display the data portion of a SAS data set.

SAS variable and data set names must be

* 1 to 32 characters in length and 
* start with a letter or underscore, followed by letters, underscores, and numbers. 
* Variable names are not case sensitive.

###Sample Programs

####Accessing a SAS Library

    %let path=filepath;
    libname orion "&path";

####Browsing a Library

    proc contents data=orion._all_;
    run;

    proc contents data=orion._all_ nods;
    run;

####Viewing a Data Set with PROC PRINT

    proc print data=orion.country;
    run;
 
####Viewing the Descriptor Portion of a Data Set

    proc contents data=orion.sales;
    run;

####Viewing the Data Portion of a SAS Data Set

    proc print data=orion.sales;
    run;

##Subsetting Report Data

You can use the **VAR** statement in a PROC PRINT step to **subset the variables** in a report. You specify the variables to include and list them in the order in which they are to be displayed.

You can use the **SUM** statement in a PROC PRINT step to calculate and display report totals for the requested numeric variables.

    PROC PRINT DATA=SAS-data-set; 
            VAR variable(s); 
            SUM variable(s); 
    RUN;

The **WHERE** statement in a PROC PRINT step subsets the observations in a report. When you use a WHERE statement, the output contains only the observations that meet the conditions specified in the WHERE expression. 

    WHERE where-expression;

You can use the **ID** statement in a PROC PRINT step to specify a variable to print at the beginning of the row instead of an observation number. The variable that you specify replaces the Obs column.

    ID variable(s);

##Sorting and Grouping Report Data

You can sort on one variable or multiple variables, sort on character or numeric variables, and sort in ascending or descending order. 

By default, SAS replaces the original SAS data set unless you use the OUT= option to specify an output data set. 

PROC SORT does not generate printed output.

Every PROC SORT step **must** include a BY statement to specify one or more BY variables. 

By default, SAS sorts in ascending order, but you can use the keyword DESCENDING to specify that the values of a variable are to be sorted in descending order. 

*Remember that the input data set must be sorted on the variables specified in the BY statement.*

    PROC SORT DATA=input-SAS-data-set
        <OUT=ouput-SAS-data-set>;
        BY <DESCENDING> by-variable(s);
    RUN;


##Enhancing Reports

    TITLEn 'text';
    FOOTNOTEn 'text';

n=1..10

    TITLE;
    FOOTNOTE;
cancel all the previous Title definition, and footnotes.

Use the LABEL statement in a PROC PRINT step to define temporary labels to display in the report instead of variable names. 

Labels can be up to 256 characters in length. 

Use the **LABEL** option or **SPLIT=** option in the PROC PRINT statement to tell SAS to display the labels. The **SPLIT=** option specifies a split character to control line breaks in column headings.

    PROC PRINT DATA=SAS-data-set LABEL; 
            LABEL variable='label'
                         variable='label'
                        ... ;
    RUN;


    SPLIT='split-character';
 


###Sample Programs

####Selecting Observations

    proc print data=orion.sales noobs;
       var Last_Name First_Name Salary Country;
       where Country='AU' 
        and Salary<25500 
        and Job_Title contains 'Rep';
       sum Salary;
       id Customer_ID;
       var Customer_Name
           Customer_Gender Customer_Country
           Customer_Group Customer_Age_Group
           Customer_Type;
    run;

####Grouping Observations in Reports

    proc sort data=orion.sales          
              out=work.sales2;   
       by Country descending Salary;
    run;
    proc print data=work.sales2;
       by Country;
    run;

####Displaying Labels in a Report

    title1 'Orion Star Sales Staff';
    title2 'Salary Report';
    footnote1 'Confidential';
    proc print data=orion.sales label;
       var Employee_ID Last_Name Salary;
       label Employee_ID = 'Sales ID'
             Last_Name = 'Last Name'
             Salary = 'Annual Salary';
    run;
    title;
    footnote;

##Formatting Data Values

Using SAS Formats

    FORMAT variable(s) format;

SAS stores date values as the number of days between January 1, 1960, and a specific date. 

| FORMAT    | Result     |
|-----------|------------|
| DATE9.    | 17MAR2000  |
| DDMMYY10. | 17/03/2000 |
| MMDDYY10. | 03/17/2000 |
| MONYY.    | MAR2000    |
| QTRw.     | 1          |

| FORMAT | Stored Value | Displayed Value |
|-----------|--------------|-----------------|
| MMDDYY6. | 365 | 123160 |
| MMDDYY8. | 365 | 12/31/60 |
| MMDDYY10. | 0 | 12/31/1960 |

| FORMAT     | Stored Value | Displayed Value |
|------------|--------------|-----------------|
| $4.        | Programming  | Prog            |
| 12.        | 27134.5864   | 27135           |
| 12.2       | 27134.5864   | 27134.59        |
| COMMA12.2  | 27134.5864   | 27,134.59       |
| DOLLARw.d  | 27134.5864   | $ 27,134.59     |
| COMMAX12.2 | 27134.5864   | 27.134,59       |
| EUROX12.2  | 27134.5864   | € 27.134,59     |

| FORMAT | Stored Value | Displayed Value |
|------------|--------------|-----------------|
| DOLLAR12.2 | 27134.5864 | $27,134.59 |
| DOLLAR9.2 | 27134.5864 |  |
| DOLLAR8.2 | 27134.5864 | 27134.59 |
| DOLLAR5.2 | 27134.5864 | 27135 |
| DOLLAR4.2 | 27134.5864 | 27E3 |

###Creating and Applying User-Defined Formats

**format name** can have up to 32 characters. 

The name of a **character format** must begin with a dollar sign, followed by a letter or underscore, followed by letters, numbers, and underscores. 

Names for **numeric formats** must begin with a letter or underscore, followed by letters, numbers, and underscores. 

*A format name cannot end in a number and cannot be the name of a SAS format.*

You use a VALUE statement in a PROC FORMAT step to specify the way that you want the data values to appear in your output.

    PROC FORMAT;
            VALUE format-name 
              value-or-range1='formatted-value1'
              value-or-range2='formatted-value2'
              ...;
    RUN;

The **LOW** and **HIGH** keywords are used to define a continuous range when the lowest and highest values are not known. 

*Remember that for character values, the LOW keyword treats missing values as the lowest possible values. However, for numeric values, LOW does not include missing values.*


###Sample Programs

####Specifying a User-Defined Format for a Character Variable

    proc format;
       value tiers low-<50000='Tier1'
                   50000-100000='Tier2'
                   100000<-high='Tier3';
    run;
    proc format;
       value $ctryfmt 'AU'='Australia'
                      'US'='United States'
                      other='Miscoded';
    run;
    proc print data=orion.sales label;
       var Employee_ID Job_Title Salary 
           Country Birth_Date Hire_Date;
       label Employee_ID='Sales ID'
             Job_Title='Job Title'
             Salary='Annual Salary'
             Birth_Date='Date of Birth'
             Hire_Date='Date of Hire';
       format Salary tiers. 
              Birth_Date Hire_Date monyy7.
              Country $ctryfmt.;
    run;


##Reading SAS Data Sets

You use the WHERE statement to subset the input data set by selecting only the observations that meet a particular condition. To subset based on a SAS date value, you can use a SAS date constant in the WHERE expression. SAS automatically converts a date constant to a SAS date value.

    DATA output-SAS-data-set;
           SET input-SAS-data-set;
           WHERE where-expression; 
    RUN;


    variable=expression;

###Customizing a SAS Data Set

By default, the SET statement reads all of the observations and variables from the input data set and writes them to the output data set. 

You can use a DROP statement to list the variables to exclude from the new data set, or use a KEEP statement to list the variables to include. 

If you use a KEEP statement, you must include every variable to be written, including any new variables.

    DROP variable-list;
    KEEP variable-list;

**WHERE** statement you can use **ONLY** for variables that are defined in the input data set. Where statement is more efficient as it subsets the input data.

**IF** statement you can use also for new variables that are created in the data step.

*Remember that, although IF expressions are similar to WHERE expressions, you cannot use special WHERE operators in IF expressions.*

    IF expression;

To **subset observations** in a **PROC** step, you **MUST** use a WHERE statement. You **cannot** use a subsetting IF statement in a PROC step. 

###Adding Permanent Attributes

Using a FORMAT statement in a DATA step permanently associates formats with variables. 

    LABEL variable='label'
                variable='label'
                  ... ;

    FORMAT variable(s) format ...;

###Sample Programs

####Subsetting Variables in a DATA Step: DROP and KEEP

    data work.subset1;
       set orion.sales;
       where Country='AU' and
             Job_Title contains 'Rep';
       Bonus=Salary*.10;
       label Job_Title='Sales Title'
             Hire_Date='Date Hired';
       format Salary Bonus dollar12. 
              Hire_Date ddmmyy10.;
       drop Employee_ID Gender Country Birth_Date;
        if Bonus>=3000;
    run;
    proc print data=work.subset1;
    run;
    data work.subset1;
       set orion.sales;
       where Country='AU' and
             Job_Title contains 'Rep';
       Bonus=Salary*.10;
       keep First_Name Last_Name 
        Salary Job_Title Hire_Date Bonus;
    run;
    proc print data=work.subset1;
    run;


##Reading Spreadsheet and Database Data

###Reading Spreadsheet Data

    LIBNAME libref <engine> 
        "workbook-name" <options>;
    LIBNAME libref <engine> 
        <PATH=> "workbook-name" <options>;

In order to address Sheet within the spreadsheet, you do it like this:

    libref.'worksheetname$'n

When you assign a libref to an Excel workbook in SAS, the workbook cannot be opened in Excel. To disassociate a libref, you submit a LIBNAME statement specifying the libref and the CLEAR option. SAS disconnects from the data source and closes any resources that are associated with the connection.

###Reading Database Data

You can also read database tables as if they were SAS data sets by using the LIBNAME statement supported by SAS/ACCESS Interface to Oracle. This SAS/ACCESS LIBNAME statement includes a libref, an engine name, and additional connection options that are site- and installation-specific. After you submit the LIBNAME statement, SAS treats the Oracle database as if it were a SAS library, and any table in the database can be referenced using a SAS two-level name, as if it were a SAS data set.

    LIBNAME libref engine <SAS/ACCESS Oracle options>;

###Sample Programs

####Accessing Excel Worksheets in SAS

    libname orionx pcfiles path="&path/sales.xls";
    proc contents data=orionx._all_;
    run;

####Printing an Excel Worksheet

    proc print data=orionx.'Australia$'n;
    run;
    proc print data=orionx.'Australia$'n noobs;
       where Job_Title ? 'IV';
       var Employee_ID Last_Name Job_Title Salary;
    run;

####Creating a SAS Data Set from an Excel Worksheet

    libname orionx pcfiles path="&path/sales.xls";
    data work.subset;
       set orionx.'Australia$'n;
       where Job_Title contains 'Rep';
       Bonus=Salary*.10;
       label Job_Title='Sales Title'
             Hire_Date='Date Hired';
       format Salary comma10. Hire_Date mmddyy10.
              Bonus comma8.2;
    run;
    libname orionx clear;

## Reading Raw Data Files

###Reading Standard Delimited Data

The **INFILE** statement identifies the name and location of the input file. You use the **DLM=** option if the file has a delimiter other t.-.-,.–m,han a blank space. 
The **INPUT** statement tells SAS how to read the values, and specifies the name and type for each variable to be created. 

*With list input, the default length for all variables is 8 bytes, regardless of type.*

    DATA output-SAS-data-set-name;
            INFILE 'raw-data-file-name' 
                DLM='delimiter';
            INPUT variable1 <$> variable2 <$> 
                ... variableN <$>;
    RUN; 

SAS processes the DATA step in two phases: *compilation* and *execution*. 

During **compilation**, SAS creates an input buffer to hold a record from the raw data file. The input buffer is an area of memory that SAS creates only when reading raw data, not when reading a SAS data set. SAS also creates the PDV, an area of memory where an observation is built. 
In addition to the variables named in the INPUT statement, SAS creates the iteration counter, **\_N\_**, and the error indicator, **\_ERROR\_**, in the PDV. These temporary variables are not written to the output data set. At the end of the compilation, SAS creates the descriptor portion of the output data set.

At the start of the **execution** phase, SAS initializes the PDV and then reads the first record from the raw data file into the input buffer. 
It scans the input buffer from non-delimiter to delimiter and assigns each value to the corresponding variable in the PDV. SAS ignores delimiters. At the bottom of the DATA step, SAS writes the values from the PDV to the new SAS data set and then returns to the top of the DATA step.

*WARNING: Truncation often occurs with list input, because character variables are created with a length of 8 bytes, by default.* 

You can use a **LENGTH** statement before the INPUT statement in a DATA step to explicitly define the length of character variables. Numeric variables can be included in the LENGTH statement to preserve the order of variables, but you need to specify a length of 8 for each numeric variable.

    LENGTH variable(s) <$> length;

###Reading Nonstandard Delimited Data

You can use modified list input to read standard and nonstandard data from a delimited raw data file. Modified list input uses an **informat** and a colon format modifier for each field to be read. 

When SAS reads character data, a standard character informat, such as $12., is often used instead of a LENGTH statement. 

The **colon format modifier** tells SAS to ignore the specified length when it reads data values, and instead to read only until it reaches a delimiter. Omitting the colon format modifier is likely to result in data errors.

    INPUT variable <$> variable <:informat>;

An informat is required to read nonstandard numeric data, such as calendar dates, and numbers with dollar signs and commas. Many SAS informats are available for nonstandard numeric values. Every informat has a width, whether stated explicitly or set by default.

When reading a raw data file, you can use a DROP or KEEP statement to write a subset of variables to the new data set. 

*WARNING: You cant use WHERE statement to select observations from raw data format!*

You must use a subsetting IF statement to select observations, because the variables are not coming from an input SAS data set. 

You can use **LABEL** and **FORMAT** statements to permanently store label and format information in the new data set.

    DATALINES;
    <data line 1>
    <data line 2>
    ...
    ;

###Validating Data

When data values in the input file aren't appropriate for the INPUT statement in a program, a data error occurs during program execution. SAS records the error in the log by writing a note about the error, along with a ruler and the contents of the input buffer and the PDV. The variable _ERROR_ is set to 1, a missing value is assigned to the corresponding variable, and execution continues.

You can use the **DSD** option in the **INFILE** statement if data values are missing in the middle of a record. When you use the **DSD** option, SAS assumes that the file is **comma delimited**, treats consecutive delimiters as missing data, and allows embedded delimiters in a field that is enclosed in quotation marks. 

If you have missing data values at the end of a record, you can use the **MISSOVER** option in the INFILE statement. SAS sets the variable values to missing.

    INFILE 'raw-data-file-name' 
        <DLM=> <DSD> <MISSOVER>;

###Sample Programs

####Specifying the Lengths of Variables Explicitly

    data work.sales2;
       length Employee_ID  8 First_Name $ 12
              Last_Name $ 18 Gender $ 1 
              Salary  8 Job_Title $ 25
              Country $ 2;
       infile "&path/sales.csv" dlm=',';
       input Employee_ID First_Name $ Last_Name $ 
             Gender $ Salary Job_Title $ Country $; 
    run;
    proc contents data=work.sales2 varnum;
    run;
    proc print data=work.sales2;
    run;

####Specifying Informats in the INPUT Statement

    data work.sales2;
       infile "&path/sales.csv" dlm=',';
       input Employee_ID First_Name :$12. 
            Last_Name :$18. Gender :$1. 
            Salary Job_Title :$25. Country :$2.
             Birth_Date :date. Hire_Date :mmddyy.;
    run; 
    proc print data=work.sales2;
    run;

####Reading Instream Data

    data work.newemps;
       input First_Name $ Last_Name $  
             Job_Title $ Salary :dollar8.;
       datalines;
    Steven Worton Auditor $40,450
    Merle Hieds Trainee $24,025
    Marta Bamberger Manager $32,000
    ;
    proc print data=work.newemps;
    run;
    data work.newemps2;
       infile datalines dlm=',';
       input First_Name $ Last_Name $
             Job_Title $ Salary :dollar8.;
       datalines;
    Steven,Worton,Auditor,$40450
    Merle,Hieds,Trainee,$24025
    Marta,Bamberger,Manager,$32000
    ;
    proc print data=work.newemps2;
    run; 

###Reading a Raw Data File That Contains Missing Data

    data work.contacts;
       length Name $ 20 Phone Mobile $ 14;
       infile "&path/phone2.csv" dsd;
       input Name $ Phone $ Mobile $;
    run;
    proc print data=work.contacts noobs;
    run;

####Reading a Raw Data File Using the MISSOVER Option

    data work.contacts2;
       infile "&path/phone.csv" dlm=',' missover; 
       input Name $ Phone $ Mobile $;
    run;
    proc print data=contacts2 noobs;
    run;
    data work.contacts2;
       length Name $ 20 Phone Mobile $ 14;
       infile "&path/phone.csv" dlm=',' missover; 
       input Name $ Phone $ Mobile $;
    run;
    proc print data=contacts2 noobs;
    run;

##Manipulating Data

###Using SAS Functions

You use an assignment statement in a DATA step to evaluate an expression and assign the result to a new or existing variable. The expression on the right side of an assignment statement can include calls to SAS functions. A SAS function is a routine that accepts arguments and returns a value.

    variable=expression;

The SUM function, a descriptive statistics function, returns the sum of its arguments and ignores missing values. The arguments can be numeric constants, numeric variables, or arithmetic expressions, but the arguments must be numeric values and must be enclosed in parentheses and separated with commas. The parentheses are required, even if no arguments are passed to the function.

    SUM(argument1,argument2, ...)

    MONTH(SAS-date)
    YEAR(SAS-date)
    DAY(SAS-date)
        TODO: add more functions used within practice

###Conditional Processing

    IF expression THEN 
          DO;
                executable statements
          END; 
    ELSE IF expression THEN 
          DO;
                executable statements
    END;

*Truncation can occur when new variables are assigned values within conditional program statements. During compilation, SAS creates a variable in the PDV the first time it encounters the variable in the program. If this is in conditional code, be sure that it is created with a length long enough to store all possible values. It is a best practice to use a LENGTH statement to explicitly define the length.*


###Sample Programs

####Creating Variables by Using Functions

    data work.comp;
       set orion.sales;
       Bonus=500;
       Compensation=sum(Salary,Bonus);
       BonusMonth=month(Hire_Date);
    run;
    proc print data=work.comp;
       var Employee_ID First_Name Last_Name 
           Salary Bonus Compensation BonusMonth;
    run;

####Using Compound Conditions

    data work.comp;
       set orion.sales;
       Freq='Once a Year';
       if Job_Title='Sales Rep. III' or
          Job_Title='Sales Rep. IV' then
          Bonus=1000;
       else if Job_Title='Sales Manager' then
          do;
          Freq='Twice a Year';
          Bonus=1500;
          end;
       else if Job_Title='Senior Sales Manager' then 
          Bonus=2000;
       else if Job_Title='Chief Sales Officer' then 
          Bonus=2500;
       else Bonus=500;
    run; 
    proc print data=work.comp;
       var Last_Name Job_Title Bonus;
    run;

##Combining SAS Data Sets

###Concatenating Data Sets

    DATA SAS-data-set;
           SET SAS-data-set1 
              SAS-data-set2(RENAME=(oldName1=newName1;
                            old-name-2=new-name-2
                            ...
                            old-name-n=new-name-n))
                                 ...;
    RUN;

This dataset would **combine two dataset vertically**. In the **compilation phase** it would create PDV out of all variables that are existing. In the **execution phase**, it will store values from SAS-data-set1 and then from SAS-data-set2. All non-existent values will be filled as missing value.  

You can use the **RENAME=** data set option to change variable names in one or more data sets, for purpose of:

* merge variables which should be merged (e.g. Birth_Date and DateOfBirth)
* rename variables that are same-named in different datasets to avoid overwriting of data (and thereby data-loss).

###Merging SAS Data Sets One-to-One

In order to **combine two datasets horizontally**, you can use **MERGE** statement.

The **BY** statement indicates a match-merge and specifies the common variable or variables to match. The **BY** variables must exist in every data set, and each data set **MUST be sorted** by the value of the BY variables.

    DATA SAS-data-set;
           MERGE SAS-data-set1 SAS-data-set2 ...;
           BY <DESCENDING> BY-variable(s);
           <additional SAS statements>
    RUN;


###Merging SAS Data Sets That Have Non-Matches

By default, both **matches and non-matches are included** in a merged data set.

You can use the **IN=** data set option in a MERGE statement to create a **temporary variable** that indicates whether a data set contributed information to the observation in the PDV. 

The IN= variables have two possible values: 0 and 1. 

    MERGE SAS-data-set1 <(IN=variable)>...

###Sample Programs

####Merging Data Sets One-to-One

    /* Create Data */
    data empsau;
       input First $ Gender $ EmpID;
       datalines;
    Togar   M   121150
    Kylie   F   121151
    Birin   M   121152
    ;
    run;
    data phoneh;
       input EmpID Phone $15.;
       datalines;
    121150 +61(2)5555-1793
    121151 +61(2)5555-1849
    121152 +61(2)5555-1665
    ;
    run;
    /* Match-Merge One-to-One */
    data empsauh;
       merge empsau phoneh;
       by EmpID;
    run;
    proc print data=empsauh;
    run;

####Selecting Non-Matches

    /* Create Data */
    data empsau;
       input First $ Gender $ EmpID;
       datalines;
    Togar   M   121150
    Kylie   F   121151
    Birin   M   121152
    ;
    run;
    data phonec;
       input EmpID Phone $15.;
       datalines;
    121150 +61(2)5555-1795
    121152 +61(2)5555-1667
    121153 +61(2)5555-1348
    ;
    run;
    /* Non-Matches from empsau Only */
    data empsauc2;
       merge empsau(in=Emps) 
             phonec(in=Cell);
       by EmpID;
       if Emps=1 and Cell=0;
    run;
    proc print data=empsauc2;
    run;


##Creating Summary Reports

###Using PROC FREQ to Create Summary Reports

You can use PROC FREQ to produce frequency tables that report the distribution of any or all variable values in a SAS data set. You use the TABLES statement to specify the frequency tables to produce.

    PROC FREQ DATA=SAS-data-set <optionFreq(s)>;
           TABLES variable(s) </optionTable(s)>;
           <additional SAS statements>
    RUN;

optionFreq:

* **COMPRESS** - Begins the next one-way table on the current page
* **NLEVELS** - Displays the number of levels for all TABLES variables
* **NOPRINT** - Suppresses all displayed output
* **PAGE** - Displays one table per page 
* **ORDER=**
    + **FREQ** - Descending frequency count; levels with the most observations come first in the order
    + **INTERNAL** - Unformatted value
    + **DATA** - Order of appearance in the input data set
    + **FORMATTED** - External formatted value, except for numeric variables with no explicit format, which are sorted by their unformatted (internal) value

optionTable:

* **MISSING** - Treats missing values as nonmissing 
* **CUMCOL** - Displays cumulative column percentages
* **MISSPRINT** - Displays missing value frequencies
* **FORMAT=** - Formats the frequencies in crosstabulation tables
* **LIST** - Displays two-way to n-way tables in list format
* **NOCOL** - Suppresses display of column percentages
* **NOCUM** - Suppresses display of cumulative frequencies and percentages
* **NOFREQ** - Suppresses display of frequencies
* **NOPERCENT** - Suppresses display of percentages
* **NOPRINT** - Suppresses display of crosstabulation tables but displays statistics
* **NOROW** - Suppresses display of row percentages
* **OUT=** - Names an output data set to contain frequency counts

TABLES statement: It creates a one-way frequency table for each variable. 

* You can request a separate analysis for each group by including a BY statement.
* You can request a two-way frequency table by separating the variables with an asterisk instead of a space.

###Examples

#### Place the proc freq result into a sas dataset

    proc freq data=orion.order_fact;
        tables Product_ID /out=work.bla noprint;
    run;

####Using Formats in PROC FREQ

    proc format;
       value Tiers low-25000='Tier1'
                   25000<-50000='Tier2'
                   50000<-100000='Tier3'
                   100000<-high='Tier4';
    run; 
    proc freq data=orion.sales;
       tables Salary;
       format Salary Tiers.;
    run;

####Listing Multiple Variables on a TABLES Statement

    proc freq data=orion.sales;
       tables Gender Country;
    run;
    proc sort data=orion.sales out=sorted;
       by Country;
    run;
    proc freq data=sorted;
       tables Gender; 
       by Country;
    run;

####Creating a Crosstabulation Table

    proc freq data=orion.sales;
       tables Gender*Country;
    run;

####Using PROC FREQ Options to Validate Your Data

    proc freq data=orion.nonsales2 order=freq;
       tables Employee_ID/nocum nopercent;
    run;
    proc freq data=orion.nonsales2 nlevels;
       tables Gender Country Employee_ID/nocum 
            nopercent;
    run;
    proc freq data=orion.nonsales2 nlevels;
       tables Gender Country Employee_ID/nocum 
            nopercent noprint;
    run;

####Using PROC PRINT to Validate Your Data

    proc print data=orion.nonsales2 (obs=20);
       where Gender not in ('F','M') or
             Country not in ('AU','US') or
             Job_Title is null or 
             Salary not between 24000 and 500000 or 
             Employee_ID is missing or
             Employee_ID=120108;
    run;


###Using the MEANS and UNIVARIATE Procedures

You can use PROC MEANS to produce summary reports with descriptive statistics. By default, it reports:

* (N Obs) the number of total observations (including missing values), 
* (N) the number of nonmissing values, 
* the mean, 
* the standard deviation, 
* the minimum, and 
* the maximum value of every numeric variable in a data set.
 
You can use the **VAR** statement to specify the numeric variables for which the stastics (min,max, std,...) will be computed. 

You can use a **CLASS** statement to request statistics for groups of observations (e.g. compute salary statistics for each US employee and AU employee divided by gender) 

*The variables listed in the CLASS statement are called* **classification variables**, *or class variables, and each combination of class variable values is called a* **class level.**

    PROC MEANS DATA=SAS-data-set <statistic(s)>;
           VAR analysis-variable(s);
           CLASS classification-variable(s);
    RUN;

statistics: 
* **N** - show number of observations
* **MEAN STD MIN  MAX  SUM** - show statistical values
*  **NONOBS** - supress showing N Obs columns
*  **NMISS** - show number of missing values

Values are formatted in BESTw. format by default. MAXDEC=number (maximum number of decimal places)

When you use the CLASS statement, the output includes N Obs, which reports the number of observations with each unique combination of class variables. You can request specific statistics by listing them as options in the PROC MEANS statement. Other options are available to control the output.

You can use **PROC MEANS** to validate a data set. The MIN, MAX, and NMISS statistics can be used to validate numeric data when you know the range of valid values. 

**PROC UNIVARIATE** can be more useful because it displays the extreme observations, or outliers. By default, it displays the five highest and five lowest values of the analysis variable, and the number of the observation with each extreme value. You can use the **NEXTROBS=** option to display a different number of extreme observations.

    PROC UNIVARIATE DATA=SAS-data-set;
           VAR variable(s);
    RUN;

* **NONOBS** - Suppress reporting the total number of observations for each unique  combination of the class variables
* **NOPRINT** - Suppress all displayed output   

###Examples

#### output proc means to dataset

    proc means data=orion.order_fact noprint;
       class Product_ID;
       var Total_Retail_Price;
       output out=product_orders 
            sum(Total_Retail_Price)=Product_Revenue;
    run;

####Creating a Summary Report with PROC MEANS

    proc means data=orion.sales;
       var Salary;
    run;

####Creating a PROC MEANS Report with Grouped Data

    proc means data=orion.sales;
       var Salary;
       class Gender Country;
    run;

####Requesting Specific Statistics in PROC MEANS

    proc means data=orion.sales n mean;
       var Salary;
    run;
    proc means data=orion.sales min max sum;
       var Salary;
       class Gender Country;
    run;

####Validating Data Using PROC MEANS

    proc means data=orion.nonsales2 n nmiss min max;
       var Salary;
    run;

####Validating Data Using PROC UNIVARIATE

    proc univariate data=orion.nonsales2;
       var Salary;
    run;
    proc univariate data=orion.nonsales2 nextrobs=3;
       var Salary;
    run;
    proc univariate data=orion.nonsales2 nextrobs=3;
       var Salary;
       id Employee_ID;
    run;


| Procedure | Numeric | Character | Method |
|-----------------------|---------|-----------|---------------------------------------------|
| FREQ                       | YES | YES | looking at distinct values |
| with TABLES statement      |    |    |                            |
| PRINT                       | YES | YES | subsetting observations based on conditions |
| with WHERE statements       |    |    |                                             |
| MEANS                    | YES | NO | using summary statistics |
| with VAR statement       |    |   |                          |
| UNIVARIATE                     | YES | NO |  |
| with VAR statements            |    |   | |

###Using the Output Delivery System (ODS)

You can use the SAS Output Delivery System to create different output formats by directing output to various ODS destinations. 

    ODS destination FILE="filename" <options>;
          <SAS code to generate the report>
    ODS destination CLOSE; 

###Sample Programs

####Using the SAS Output Delivery System

    ods pdf file="c:/output/salaries.pdf";
    proc means data=orion.sales min max sum;
       var Salary;
       class Gender Country;
    run;
    ods pdf close;
    ods csv file="c:/output/salarysummary.csv";
    proc means data=orion.sales min max sum;
       var Salary;
       class Gender Country;
    run;
    ods csv close;



##Controlling Input and Output

###Writing to Multiple SAS Data Sets

    SELECT <(select-expression)>;
    WHEN-1 (when-expression -1 <…, when-expression-n>) statement;
    WHEN-n (when-expression -1 <…, when-expression-n>) statement;
    <OTHERWISE statement;>
    END;

* When a SELECT expression is specified, SAS evaluates the SELECT expression and compares that value to each WHEN expression and returns either a true or false.
* If no SELECT expression is specified, SAS evaluates each WHEN expression in order until it finds a true expression. If no WHEN expression is true, SAS executes the OTHERWISE statement if one is present.
* You can use functions in a SELECT expression.
* You can use DO-END groups in a SELECT group. You can execute multiple statements when a WHEN expression is true by using DO-END groups.

###Controlling Variable Input and Output

    SAS-data-set-name (DROP=variable(s)) 
    SAS-data-set-name (KEEP=variable(s))
￼
* When the **DROP=** and **KEEP=** data set options are used in the **SET** statement, the variables are not processed and are not available in the program data vector.
* When the **DROP=** and **KEEP=** data set options are used in the **DATA** statement, they affect the variables in the output data set they are associated with.
* **DROP** and **KEEP** statements affect **all** output data sets listed in the DATA statement.
* You can use a combination of data set options and statements. If you use them together, **statements are applied before data set options**. If you attempt to **drop and keep the same variable**, you will get a **warning**.

###Controlling Observation Input and Output

    SAS-data-set-name (OBS=n)
    SAS-data-set-name (FIRSTOBS=n)
￼
* The **OBS=** data set option specifies the **number of the last observation** to process. It does **not** specify **how many** observations should be processed.
* The **FIRSTOBS=** data set option specifies a **starting point** for processing an input data set. By **default**, **FIRSTOBS=1**.
* **FIRSTOBS=** and **OBS=** can be used in a procedure step to limit the number of observations that are processed. If a **WHERE** statement is used to subset the observations, it **is applied before the data set options**.
* **(OBS - FIRSTOBS) + 1 = Number of Results**

    data australia;
       set orion.employee_addresses
          (firstobs=50 obs=100);
       if Country='AU' then output;
    run;

*takes from orion.employee_addresses only observation from 50-100, totalling 51 observations.*

    proc print data=orion.employee_addresses
               (obs=10);
       where Country='AU';
       var Employee_Name City State Country;
    run;

*shows the observation from firstobs=1 (default) to obs=10... first ten observations*

##Sumarizing Data

###Creating an Accumulating Variable Using the RETAIN Statement

    RETAIN variable-name <initial-value> ...;

* The **RETAIN** statement is a **compile-time-only** statement that **prevents SAS** from **reinitializing the variable** at the **top of the DATA step**. Because the variable is not reinitialized, it retains its value across multiple iterations of the DATA step.
* If you **don't specify an initial value**, the **RETAIN** statement **initializes** the variable to **missing** before the first execution of the DATA step.

    data mnthtot2;
       set orion.aprsales;
       retain Mth2Dte 0;
       Mth2Dte=Mth2Dte+SaleAmt;
    run;

###Creating an Accumulating Variable Using the Sum Statement

    variable+expression;

As an **alternative** to using the **RETAIN** statement with an assignment statement, you can use the sum statement. 

**By default**, the sum statement **initializes** the variable **to 0** and retains the variable. It also **ignores missing input values** from the expression.

    data mnthtot2;
       set orion.aprsales;
       Mth2Dte+SaleAmt;
    run;

###Using BY-Group Processing: Summarizing Data by Groups

    DATA output-SAS-data-set; 
        SET input-SAS-data-set; 
        BY BY-variable ...;
        <additional SAS statements> 
    RUN;

* The BY statement creates two temporary variables (FIRST.variable and LAST.variable) for each BY variable listed. These variables identify the first and last observation in each BY group.
* You can use the FIRST. and LAST. variables in a DATA step to summarize the grouped data.
    + First, set the accumulating variable equal to 0 at the start of each BY group.
    + Second, increment the accumulating variable with a sum statement.
    + Third, output only the last observation of each BY group.

sample : the following code outputs the total salaries for each department

    data deptsals (keep=Dept DeptSal);
       set SalSort;
       by Dept;
       if First.Dept then DeptSal=0;
       DeptSal+Salary;
       if Last.Dept;
    run;

###Using BY-Group Processing: Summarizing Data by Multiple Groups

* When you need to accumulate totals for multiple groups, you specify two or more BY variables. The first variable listed becomes the primary sort variable, and the second variable listed is the secondary sort variable.
* The BY statement creates two temporary variables for each BY variable listed.
* If the last observation for a value of the primary sort variable is encountered, it sets LAST. to 1 for all subsequent BY variables. (see example table bellow)

sample 2: 

    data deptsals (keep=Proj Dept DeptSal NumEmps);
       set projsort;
       by Proj Dept;
       if First.Dept then
          do;
             DeptSal=0;
             NumEmps=0;
          end;
       DeptSal+Salary;
       NumEmps+1;
       if Last.Dept;
    run;

| Proj | Dept   | FIRST.Proj | FIRST.Dept | LAST.Proj | LAST.Dept |
|------|--------|------------|------------|-----------|-----------|
| CAP1 | ADMIN  | 1          | 1          | 0         | 0         |
| CAP1 | ADMIN  | 0          | 0          | 0         | 0         |
| CAP1 | ADMIN  | 0          | 0          | 1         | **1**         |
| EZ   | ADMIN  | 1          | 1          | 0         | 1         |
| EZ   | ENGINR | 0          | 1          | 0         | 0         |

##Reading Raw Data

###Using Column Input

    INPUT variable <$> startcol-endcol . . . ;

* You can use column input to read input data that is arranged in columns or fixed fields. To use column input your data must be standard data in fixed columns.
* **Standard data** is data that SAS can read without special instructions. 
* **Nonstandard data** is data that SAS needs special instructions to read.

###Using Formatted Input

    INPUT column-pointer-control variable informat . . . ;

    INPUT @n variable informat . . . ;

    INPUT +n variable informat . . . ;

* You can use **formatted input** to read **both** standard and nonstandard data that is arranged in fixed fields.
* An informat is the special instruction that specifies how SAS reads raw data.
* The **@n** is an **absolute** pointer control that specifies the beginning location for a field. 
* The **+n** is a **relative** pointer control that specifies how many columns to move the pointer before reading the next field.

Sample:

    data work.discounts;
    infile 'my-file-path\offers.dat'; 
    input @1 Cust_type 4.
              @5 Offer_dt mmddyy8.
              @14 Item_gp $8.
              @22 Discount percent3.;
    run;
    proc print data=work.discounts;
    run;


###Creating a Single Observation from Multiple Records

You can use multiple INPUT statements to read a group of records in a raw data file as a single observation in a new data set.

    DATA SAS-data-set;
        INFILE 'raw-data-file-name';
        INPUT specifications;
        INPUT specifications;
    <additional SAS statements>

As an alternative to writing multiple INPUT statements, you can write one INPUT statement that contains line pointer controls to specify the record(s) from which values are to be read. There are two line pointer controls, the **forward slash** (a relative line pointer control) and the **#n** (an absolute line pointer control).

    DATA SAS-data-set;
        INFILE 'raw-data-file-name';
        INPUT specifications /
        #n specifications; <additional SAS statements>

Sample:

    data contacts;
        infile 'my-file-path\address.dat'; input FullName $30.;
        input;
        input Address2 $25.;
        input Phone $8.;
        run;
    proc print data=work.contacts;
    run;

###Controlling When a Record Loads

    INPUT specifications . . . @ ;

* By **default**, **each INPUT** statement in a DATA step **reads a new data record** into the **input buffer**.
* You can use a **line-hold specifier**, the single trailing **@**, to prevent the second INPUT statement in a DATA step from moving to the second line in a raw data file. When you use the trailing @, the pointer position doesn't change and a new record isn't loaded in the input buffer when a subsequent input statement executes.
* The single trailing **@** **holds a raw data record** in the input buffer 
    + until an **INPUT** statement **without a trailing @** executes or 
    + the **next iteration of the DATA step** begins.

Sample:

    data salesQ1;
        infile 'my-file-path\sales.dat'; input SaleID $4. @6 Location $3. @; if Location='USA' then
          input @10 SaleDate mmddyy10.
                @20 Amount 7.;
       else if Location='EUR' then
          input @10 SaleDate date9.
                @20 Amount commax7.;
    run;
    proc print data=salesQ1;
    run;

###Reading Raw Data with Missing Values

    INFILE 'raw-data-file' MISSOVER;

* By **default**, the **DATA step** looks in the **next record** if the **end of the current record** is encountered **before** all of the **variables are assigned** values. This default action is known as the **FLOWOVER** option.
* You can override the **FLOWOVER** option by using the **MISSOVER** option, which **causes** the **DATA step** to **assign missing values** to any variables that do not have values in the PDV when the end of a record is reached.
* The **MISSOVER** option works **only** for missing values that **occur at the end** of the record.

However,

    INFILE 'raw-data-file' DSD;

The **DSD** option sets the **default delimiter** to a **comma**, treats **consecutive delimiters** as **missing values**, and **enables SAS** to **read values** with **embedded delimiters** if the value is surrounded by **quotation marks**.

###Creating Multiple Observations from a Single Record

    INPUT specifications @@;
    INFILE 'raw-data-file' DSD;

You can use the the **double trailing @@**, which is a **line pointer control**, to **hold a record across iterations** of the DATA step. The double trailing @@ should only be used with list input and **should not be used** with the **MISSOVER** option.

A record that is being held by the double trailing @@ is **not released until**
* the input pointer moves past the end of the record or 
* an INPUT statement that has no line-hold specifier executes.

Sample:

    data donate07;
        length ID $ 4;
        infile 'my-file-path\charity.dat'; 
        input ID $ Amount @@;
    run;
    proc print data=work.donate07;
    run;

##Manipulating Character Values

| Function | Purpose | Output Size |
|-------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------|
| Var=SUBSTR(string,start<,length>); | On the right side of an assignment statement, the SUBSTR function extracts a substring of characters from a character string, starting at a specified position in the string. | Same as input |
| LENGTH(argument) | The LENGTH function returns the length of a character string, excluding trailing blanks. |  |
| RIGHT(argument) | The RIGHT function right-aligns a value. If there are trailing blanks, they are moved to the beginning of the value. | Same as input |
| LEFT(argument) | The LEFT function left-aligns a character value. If there are leading blanks, they are moved to the end of the value. | Same as input |
| CHAR(string,position) | The CHAR function returns a single character from a specified position in a character string. | 1 |
| PROPCASE(argument<,delimiter(s)>) | The PROPCASE function converts all letters in a value to proper case. | 200 |
| UPCASE(argument) | The UPCASE function converts all letters in a value to uppercase. | Same as input |
| LOWCASE(argument) | The LOWCASE function converts all letters in a value to lowercase. | Same as input |
| SCAN (string,n<,'delimiter(s)'>) | The SCAN function enables you to separate a character value into words and to return the nth word. | 200 |
| CATX (separator,string1,...,string-n) | The CATX function removes leading and trailing blanks, inserts separators, and returns a concatenated character string. | 200 |
| NewVar=string1 !! string2; | The concatenation operator joins character strings. |  |
| TRIM(argument) | The TRIM function removes trailing blanks from a character string. | Same as input |
| STRIP(argument) | The STRIP function removes leading and trailing blanks from a character string. | Same as input |
| CAT(string1,...,string-n)                                                       | These functions return concatenated character strings. The CAT function does not remove any leading or trailing blanks. The CATT function trims trailing blanks. The CATS function strips leading and trailing blanks. | 200 |
| CATT(string1,...,string-n)                                                      |                                                                                                                                                                                                                       |    |
|                                                                                 |                                                                                                                                                                                                                       |    |
| CATS(string1,...,string-n)                                                      |                                                                                                                                                                                                                       |    |
| FIND (string,substring<,modifiers,start>) | The FIND function searches for a specific substring of characters within a character string that you specify. The function searches for the first occurrence of the substring and returns the starting position of that substring. If the substring is not found in the string, FIND returns a value of 0. |  |
| SUBSTR(string,start<,length>)=value; | On the left side of the assignment statement, the SUBSTR function replaces characters at a specified position within           | if length is not declaring, default is 8. |
|                                     | the value.                                                                                                                     |                                           |
| TRANWRD (source,target,replacement) | The TRANWRD function replaces or removes all occurrences of a given word (or a pattern of characters) within a                  | 200 |
|                                    | character string.                                                                                                               |    |
| COMPRESS (source<,chars>) | The COMPRESS function removes the                                                                                                                                                   | Same as input |
|                          | characters listed in the chars argument                                                                                                                                             |              |
|                          | from the source. If no characters are                                                                                                                                               |              |
|                          | specified, the COMPRESS function                                                                                                                                                    |              |
|                          | removes all blanks from the source.                                                                                                                                                 |              |



## Manipulation Numeric Values

| Function | Returns |
|----------|------------------------------------------------|
| SUM | the sum of the nonmissing arguments |
| MEAN | the arithmetic mean (average) of the arguments |
| MIN | the smallest value from the arguments |
| MAX | the largest value from the arguments |
| N | the number of nonmissing arguments |
| NMISS | the number of missing numeric arguments |
| CMISS |  |

* You can list all the variables in the function, or you can use a variable list by preceding the first variable name in the list with the keyword OF.

| Variable List | Description | Example |
|---------------|-----------------------------------------------------------------------------------------------------------------------------------------|-------------------------------|
| Numbered      | all variables x1 to xn, inclusive | Total = sum(of Qtr1-Qtr4); |
| Range         |                                  |                           |
| Name Range | all variables ordered as they are in the program data vector, from x to a inclusive | Total = sum(of Qtr1--Fourth); |
| Name Prefix | all variables that begin with the same string | Total = sum(of Tot:); |
| Special SAS           | all of the variables, all of the character variables, or all of the numeric variables that are already defined in the current DATA step | Total = sum(of _All_);                                                          |
| Name List:            |                                                                                                                                        |                                                                                 |
|                       |                                                                                                                                        | Total = sum(of _Character_);                                                    |
|                       |                                                                                                                                        |                                                                                 |
|                       |                                                                                                                                        | Total = sum(of _Numeric_);                                                      |


###Truncating Numeric Values

    ROUND(argument<,round-off-unit>)
    CEIL(argument)
    FLOOR(argument)
    INT(argument)

* The **ROUND** function returns a value rounded to the nearest multiple of the round-off unit. If you don't specify a round-off unit, the argument is rounded to the nearest integer.
* The **CEIL** function returns the smallest integer greater than or equal to the argument. 
* The **FLOOR** function returns the greatest integer less than or equal to the argument.
* The **INT** function returns the integer portion of the argument.

### Converting values

You can allow SAS to automatically convert data to a different data type for you, but it can be more efficient to use SAS functions to explicitly convert data to a different data type.

By default, if you reference a character variable in a numeric context, SAS tries to convert the variable values to numeric. Automatic conversion uses the w. informat, and it produces a numeric missing value from any character value that does not conform to standard numeric notation.

    INPUT(source, informat)

You can use the **INPUT** function to explicitly **convert character** values **to numeric** values. The INPUT function returns the value that is produced when the source is read with a specified informat.

Numeric data values are automatically converted to character values whenever they are used in a character context. SAS writes the numeric value with the **BEST12.** format and **right aligns** the value. The resulting value might contain leading blanks.

    PUT(source, format)

**PUT** function is used to explicitly control the **numeric-to-character** conversion using a format.

##Debugging Techniques

    PUTLOG <specifications>;
    PUTLOG 'text';
    PUTLOG variable-name=;
    PUTLOG variable-name=format-namew.; 
    PUTLOG _ALL_;

*To write the current contents of the PDV to the log, use the _ALL_ option in the PUTLOG statement. When you use the _ALL_ option, the values of the automatic variables _ERROR_ and _N_ are included in the log.*

    SET SAS-data-set END= variable <options>;
    INFILE 'raw-data-file' END= variable <options>;
￼
You can use the **END=** option in the SET statement to create and name a temporary variable that acts as an end-of-file indicator. You can also use the END= option in an INFILE statement to indicate the end of a raw data file.

###Using the DATA Step Debugger

    DATA data-set-name/DEBUG;

* You can also debug logic errors by using the DATA step debugger. This tool consists of windows and a group of commands. By issuing commands, you can execute DATA step statements one by one and pause to display the resulting variable values in a window. By observing the results that are displayed, you can determine where the logic error occurs.
* The DATA Step Debugger can only be used in an interactive session. To invoke the debugger, you use the DEBUG option in the DATA statement. Because the debugger is interactive, you can repeat the process of issuing commands and observing results as many times as you need during a single debugging session.
* Once the DATA step comes to an end, it can’t be restarted. You can examine the final values of the variables. But, to restart the debugging process, you have to quit and then restart the DATA Step Debugger.

## Using iterative DO loops

###Constructing a Simple DO Loop

    DO index-var=start TO stop <BY increment>;
        iterated SAS statements...
    END;

    DO index-var=item-1 <,... item-n>; 
        iterated SAS statements...
    END;

* An iterative DO loop executes the statements between the DO statement and the END statement repetitively.
* If you do not specify an increment for a DO loop, the increment defaults to 1.
* If your start value is greater than your stop value, you must specify an increment that is negative.
* You can use an item list rather than a start value and stop value to control your DO loop. The items must either be all numeric or all character constants, or they can be variables.

###Conditionally Executing DO Loops

    DO UNTIL (expression); 
        iterated SAS statements...
    END;

    DO WHILE (expression); 
        iterated SAS statements...
    END;


    DO index-var=start TO stop <BY increment> 
        UNTIL | WHILE (expression);
        iterated SAS statements...
    END;

* In a **DO UNTIL** loop, SAS evaluates the expression at the bottom of the loop after each iteration.
* In a **DO WHILE** loop, SAS evaluates the condition at the top of the loop and executes the statements within the loop if the condition is true.
* Nesting of the DO loops is supported.

## Using SAS Arrays

###Understanding SAS Arrays

* A SAS array is a temporary grouping of elements that exists only for the duration of the DATA step. **Unlike arrays in other programming languages, SAS arrays are not data structures.**
* An array is identified by a single unique name. The array name must be different from any variable name in the data set you are referencing.
* All variables that are grouped together in an array must be the same type: either all character or all numeric.
* Arrays can be one-dimensional or multi-dimensional.

### Creating SAS Arrays

    ARRAY array-name {dimension} <array-elements>;

* dimension describes the number and arrangement of elements in the array. The default dimension is one. The array dimension must be enclosed in braces, parentheses, or brackets. It is best to use braces or brackets so that there is no confusion with functions.
* You can use an asterisk to indicate the dimension of a one-dimensional array. When you use an asterisk, SAS determines the dimension of the array by counting the variables in the list of array elements. You can list each variable name separated by a space, or you can use a numbered range list or name range list specification.
* To create an array of character variables, type a dollar sign ($) after the dimension in the ARRAY statement. By default, all character variables that are created in an ARRAY statement are assigned a length of 8. You can assign a different length by specifying the length after the dollar sign.
* array-elements lists the variables to include in the array. Array elements can be listed in any order, but they must be either all numeric or all character. You can list each variable name separated by a space, or you can use a variable list. You can also specify the keywords _NUMERIC_ or _CHARACTER_ .

**DIM** returns number of elements in the array:

    DIM(array-name)

##TRICKY THINGS

###Length thing

The **LENGTH** statement must precede the **INPUT** statement in order to correctly set the length of the variable.

**Length** statement defines the input variables as much as input does, so if it does something like this 

    length d $ 12 e $ 1 f $ 8;
    input a b c d e f; 

the final order of variable in the sas dataset would be then 

    d e f a b c ????

### Difference between INFORMAT and FORMAT

formats - how to write
informats - how to read

### DATA WITH RAW DATA AND WHERE STATEMENT 

**WHERE** statement cannot be used in the **DATA** step when input data is not the sas dataset.

### Arithmetic operations and Missing values

When in **DATA** step, 

    C = A + B

and A is a missing value, then C will be missing value.

BUT when  

    C = SUM(A,B)

and A is a missing value, then C will be B, as it sums all non-missing values and ignores the missing values.

### Default size of the variables

when assigning new values, in case of **numeric value**, the **default size** is **8**. however, when it is case of **character value**, the sas checks the size of the **first value being assigned** to this variable, and sets size to its size.

### PROC MEANS creates all statistics

By default, a PROC MEANS step that has no CLASS statement produces all of these statistics **except for N Obs**.

### RETAIN STATEMENT

* It prevents SAS from reinitializing the values of the new variables named in the RETAIN statement at the top of the DATA step.
* It is compile-time-only statement.
* It retains the value of the variable named in the RETAIN statement in the PDV across iterations of the DATA step.
* It initializes the retained variable to missing before the first execution of the DATA step if an initial value is not specified.
* The Variable referenced in the RETAIN statement appears in the output SAS dataset only if it is given an initial value or referenced elsewhere in the DATA step.

### By GROUP

