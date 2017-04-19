# Abstract

Converting strings to date in Powershell can be tricky if date in string is not in standard format or/and computers are set up to use defferent culture settings.
Below are some links to documentation about formating dates and using culture info. And in examples section are some examples about how to convert non-standard dates and how to use cultureinfo when converting string to date.

# Links

[Formatting dates and times](https://technet.microsoft.com/en-us/library/ee692801.aspx)

[Formatting numbers and dates using cultureinfo object](https://technet.microsoft.com/en-us/library/ff730954.aspx)

# Examples

* Example 1

Lets assume we have a string with standard format date:
```powershell
$date = '5/4/2017'
```
When we are converting it to date format using Parse() method on computers using different cultures we get different result:
```powershell
# run on computer using en-US culture
[datetime]::Parse($date)

Thursday, May 4, 2017 12:00:00 AM
```
```powershell
# run on computer using et-EE culture
[datetime]::Parse($date)

5. aprill 2017. a. 0:00:00
```
To get always consistent result define culture
```powershell
$culture = [System.Globalization.CultureInfo]::InvariantCulture
[datetime]::Parse($date,$culture)

4. mai 2017. a. 0:00:00
```

When the date in a string is presented in a standard format and month and weekday are defined unambigously, there is no need to define a culture as conversion gives consistent result in every environment.
```powershell
$date = 'Thu, Aug 30, 2007 11:19:59 AM'
```
```powershell
# run on computer using en-US culture
[datetime]::Parse($date)

Thursday, August 30, 2007 11:19:59 AM
```
```powershell
# run on computer using et-EE culture
[datetime]::Parse($date)

30. august 2007. a. 11:19:59
```
* Example 2

Lets assume the string contains a date in non standard format like:
```powershell
$date = 'Tue Sep 20 03:38:20 2016'
```
In this case we need to use the ParseExact() method for convering string to date and also to define a date template.
```powershell
$template = 'ddd MMM %d HH:mm:ss yyyy'
[datetime]::ParseExact($date, $template, $null)

Tuesday, September 20, 2016 3:38:20 AM
```
This works well on computer with en-US culture but not on computer using et-EE culture. To run command successfully on computer using different culture it is necessary to define a culture also.
```powershell
$template = 'ddd MMM %d HH:mm:ss yyyy'
$culture = [System.Globalization.CultureInfo]::InvariantCulture
[datetime]::ParseExact($date, $template, $culture)

20. september 2016. a. 3:38:20
```
* Example 3

Lets assume the string contains a date in non standard format like and has some extra spaces:
```powershell
$date = 'Fri Dec  2 13:05:04 2016'
```
In this case it is not enough to just define a culture but excess spaces must be removed also. Otherwise script gives an error.
```powershell
$template = 'ddd MMM %d HH:mm:ss yyyy'
$date = $date -replace '\s+', ' '
$culture = [System.Globalization.CultureInfo]::InvariantCulture
[datetime]::ParseExact($date, $template, $culture)

2. detsember 2016. a. 13:05:04
```