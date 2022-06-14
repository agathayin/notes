check if bixolon is running, if not, open bixolon app. 


Important:

Remember to add /C: `/C:"%EXEName%"` when looking for app name. Or, the tasklist will return other task such as printnode.


Full file:


```
@ECHO OFF

SET EXEName=Web Print SDK.exe
SET EXEFullPath=C:\BIXOLON\Web Print SDK\Web Print SDK.exe

TASKLIST | FINDSTR /I /C:"%EXEName%"
IF ERRORLEVEL 1 GOTO :StartBixolon
GOTO :EOF

:StartBixolon
START "" "%EXEFullPath%"
GOTO :EOF
```
