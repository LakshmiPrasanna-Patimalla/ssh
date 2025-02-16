@echo off
setlocal enabledelayedexpansion

:: Server Details
set SERVER_USER=kali
set SERVER_IP=192.168.1.31

:: Local Base Directory
set LOCAL_BASE_DIR=C:\Users\saiof\OneDrive\Desktop\Banks

:: Fixed Banks and Their Paths
set CNB_REMOTE_BASE_DIR=/home/kali/Banks/CNB
set PMC_REMOTE_BASE_DIR=/home/kali/Banks/PMC
set TEXAS_REMOTE_BASE_DIR=/home/kali/Banks/Texas

:: Ask user for date input
set /p TODAY_DATE="Enter the date folder name (format: MMDDYYYY): "

:: Validate input is not empty
if "%TODAY_DATE%"=="" (
    echo ERROR: No date entered. Please run the script again and enter a valid date.
    pause
    exit /b
)

:: Log the date being used
echo Fetching files for date: %TODAY_DATE%
echo =======================================
echo Fetching files for date: %TODAY_DATE% >> log.txt

:: Loop through each bank separately
for %%B in (CNB PMC Texas) do (
    set BANK_NAME=%%B
    set BANK_REMOTE_DIR=!%%B_REMOTE_BASE_DIR!

    echo Processing bank: !BANK_NAME! >> log.txt
    echo Processing bank: !BANK_NAME!

    :: Define Local Paths
    set BANK_LOCAL_DIR=%LOCAL_BASE_DIR%\!BANK_NAME!
    set BANK_LOCAL_DATE_DIR=!BANK_LOCAL_DIR!\%TODAY_DATE%

    :: Create Local Directories
    if not exist "!BANK_LOCAL_DATE_DIR!\input" (
        mkdir "!BANK_LOCAL_DATE_DIR!\input"
    )
    if not exist "!BANK_LOCAL_DATE_DIR!\output" (
        mkdir "!BANK_LOCAL_DATE_DIR!\output"
    )

    :: Copy Input Files
    echo Copying Input files... >> log.txt
    scp -r %SERVER_USER%@%SERVER_IP%:"!BANK_REMOTE_DIR!/%TODAY_DATE%/*/input/*" "!BANK_LOCAL_DATE_DIR!\input" 2>> log.txt

    :: Copy Output Files
    echo Copying Output files... >> log.txt
    scp -r %SERVER_USER%@%SERVER_IP%:"!BANK_REMOTE_DIR!/%TODAY_DATE%/*/output/*" "!BANK_LOCAL_DATE_DIR!\output" 2>> log.txt

    echo Completed processing bank: !BANK_NAME! >> log.txt
    echo Completed processing bank: !BANK_NAME!
)

echo File transfer completed!
echo File transfer completed! >> log.txt
pause
