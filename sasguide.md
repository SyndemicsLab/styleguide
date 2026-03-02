# Syndemics Lab SAS Style Guide

The motivation for the Syndemics Lab SAS Style Guide is public deployment and open contribution to our code. SAS code is primarily used to pull aggregated data from Massachusetts Public Health Data Repository (PHD) governed by the Massachusetts Department of Public Health (DPH). It is important to note the DPH only allows use of limited packages in SAS. PHD also requires the code to only pull aggregated and suppressed data and not the row-wise data.

Following is the code audit process defined by PHD an the SAS code should foremost stick to these guidelines.

**Project Phase: When the project first begins accessing PHD data.**

- In the initial stage of a new PHD project, analysts will need to submit their code for the first two times
for the whole project (not for each analyst in the project) before visiting DPH or submitting an
electronic run through their DPH Liaison.

  - Analysts should use the dummy datasets to write the draft code they will be working on.
  - Analysts will send the draft code to their DPH Liaison for review no later than 48 hours before their scheduled visit.
    - If analysts are only submitting code electronically and not in person, then their liaison will review the first two code submissions that they submit.

- The DPH Liaison will briefly review:
  - The code to make sure the code is in general achieving what the researcher is intending for it to achieve and that the correct variables are being used. The DPH Liaison is not responsible for ensuring that the code is correct.
  -  That procedures are not being used to view datasets such as:
    -  Using Proc Print to view observations in a dataset that are not summary level.
    - Proc SQL without a create table statement (as that will print out the resulting dataset in the output).
    - Procedures that produce small cell sizes (i.e. less than 10).

Further Details can be found here [PHD_Data Analysis Guidelines](https://bmcorg.sharepoint.com/:b:/r/teams/SyndemicsLab-PHD/Shared%20Documents/PHD/Official%20Docs/PHD_Data%20Analysis%20Guidelines.pdf?csf=1&web=1&e=ArgqIZ).

## Packages

1. R Packages can be incorporated in the SAS code using PROC IML. 
2. R version 4.2.0  is installed in the SAS environment.
3. Below is the comprehensive list of the R Packages approved by DPH in their SAS environment.

[R Packages in PHD SAS Environment](https://bmcorg.sharepoint.com/:b:/r/teams/SyndemicsLab-PHD/Shared%20Documents/PHD/Official%20Docs/PHD_Data%20Analysis%20Guidelines.pdf?csf=1&web=1&e=fuXFN5)

## Repo Structure

- `sas/` - Where the SAS code goes
- `.gitignore` - The file used to ignore details within git
- `README.md` - The base file used to describe the contents of the file
- `LICENSE.md` - The Syndemics specific open source license

## File Naming

The following naming standards apply to .sas files:

1. File names must contain only lowercase letters.
2. Use underscores _ instead of spaces.
3. Avoid numbers unless required for ordered workflows.
4. Avoid special characters.
5. Extension must be .sas.

```text
# Good Examples
detox.sas
respond.sas
overdoses.sas

# Bad Examples
TableOne_before.sas
TableOne_after.sas
```

## File Organization
Each SAS program should contain three major sections:

1. File Header
2. Global Setup
3. Logic (Data Steps / Procedures / Macros)

### File Header

The file header should follow the pattern:

```sql
/******************************************************************************
* File: <filename>.sas
* Project: <project name>
* Created Date: <dd MMM yyyy>
* Author: <author name>
* -----------------------------------------------------------------------------
* Last Modified: <dd MMM yyyy>
* Modified By: <author name>
* -----------------------------------------------------------------------------
* Description:
*   Brief description of purpose, inputs, outputs.
*
* Inputs:
*   
*
* Outputs:
*  
*
* Copyright (c) <yyyy> Syndemics Lab at Boston Medical Center
******************************************************************************/
```
## Formatting and Syntax Standards

### Indentation

1. Use 4 spaces per indentation level.
2. Never use tabs.
3. Align nested do blocks clearly.

```sql
PROC SQL;
    CREATE TABLE ndc AS
    SELECT DISTINCT NDC 
    FROM PHDPMP.PMP
    WHERE BUP_CAT_PMP = 1;
QUIT;

```

### Line Length

1. Recommended maximum: 80 characters
2. Break long statements across logical boundaries.

### Capitalization

1. SAS keywords should be lowercase/uppercase consistently throughout the document.
2. Dataset, macros and variable names should be snake_case.

## Variable Naming

1. Use snake_case
2. Names must be descriptive
3. Avoid abbreviations unless standard (e.g., id, dob)
4. Never use special characters
5. Avoid single-letter variable names

```text
# Good Examples
treatment
new_start_date

# Bad Examples
NED
flag_mim

```

## Macro Standards

Macros are the primary method of modular programming in SAS.

## Macro Naming

1. Use snake_case
2. Verb-based naming preferred
3. File name must match the macro name if standalone.

## Macro Documentation

Every macro must include documentation immediately above definition:

```sql
/******************************************************************************
* Macro: calculate_bmi
*
* Description:
*   Calculates body mass index.
*
* Parameters:
*   weight - numeric weight in kilograms
*   height - numeric height in meters
*
* Returns:
*   BMI value
******************************************************************************/
```

## Data Step Guidelines

1. Only one dataset created per data step.

2. Avoid deeply nested conditional logic.

3. Always explicitly define lengths when creating character variables.

4. Use retain only when necessary.

5. Drop unused variables explicitly.

```sql

```

## PROC Usage Standards

1. Always specify data=

2. Explicitly define output datasets

3. Do not rely on default behavior

## Commenting Standards

1. Use block comments for sections.

```sql
/*===========================*/
/*      MAIN MERGE           */
/*===========================*/
/* 
As a final series of steps:
1. APCD-Pharm, APCD-Medical, Casemix, Death, PMP, Matris, 
   BSAS are joined together on the cartesian coordinate of Months 
   (1:12), Year (2015:2023), and SPINE (Race, Sex, ID)
2. The sum of the fabricated flags is taken. If the sum is strictly
   greater than zero, then the master flag is set to 1. 
   Zeros are deleted
4. We select distinct ID, Age Bins, Race, Year, and Month and 
   output the count of those detected with OUD
5. Any count that is between 1 and 10 are suppressed and set to -1,
   any zeros are true zeros
*/
```
2. Use inline comments sparingly and meaningfully.

```sql
%LET ICD = (
        '30550','30551','30552','30553', /* ICD9 */
        'F1110','F1111','F11120','F11121', 
        'F11288','F1129','F1193','F1199',  /* ICD10 */
	    '9701','96500','96501','96502',
	    'T40691D','T40692D','T40693D','T40694D', /* Overdose Codes */ 
        'T40411A', 'T40411D', 'T40412A', 'T40412D',
	    'F1193','F1199'/* Additional RESPOND */);       
```

## Library Management

1. Define libraries at the beginning of scripts.
2. Never hard-code user-specific paths.
3. Use macro variables for root directories.