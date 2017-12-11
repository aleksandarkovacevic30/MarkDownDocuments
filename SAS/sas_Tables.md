
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

