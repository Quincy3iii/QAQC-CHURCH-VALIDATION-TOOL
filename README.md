[Asset Data QA/QC Analyst Tool v4.1
Overview
The Asset Data QA/QC Analyst Tool is a powerful, browser-based application designed to internally audit asset data exported from Ninox. It automates the quality control process by running a comprehensive set of validation checks against a CSV file, identifying common errors, inconsistencies, and logical discrepancies.
This tool helps ensure data integrity before it is used for analysis, reporting, or system import, saving significant time and reducing manual errors.
Key Features & Validation Rules
The tool performs a wide array of checks, categorized for clarity.
1. Global & Work Zone Analysis
Global Model Inconsistency: Scans the entire dataset to find assets with the same model number but conflicting Manufacturer, Capacity, or Refrigerant data. It uses a "majority wins" rule to identify the correct data and flag only the true outliers.
Work Zone Duplicate Assets: Identifies any duplicate att_Asset # within the same WORK ZONE.
System Verifications: Checks for the presence of core building systems (e.g., Roof, Parking Lot, Fire Alarm) based on the selected building type (Meetinghouse or Welfare/Storehouse). The report shows if a system is Found, Missing, or Documented Not Found.
HVAC Split System Count: Verifies that the number of indoor split system units matches the number of outdoor units within a work zone.
2. Data Integrity & Required Fields
Required Fields: Ensures critical fields like att_Asset #, att_In-Service Date, and att_NGM CA-Condition are not blank.
Template Row Handling: Flags any template assets that have not been properly marked for deletion (TagID = "DELETE").
"Not Found" Asset Validation: Enforces strict rules for assets marked as "Not Found," ensuring location fields and nameplate data are filled out correctly and that the validation comment is appropriate.
Photo Requirement: Checks that each asset has at least three photos.
3. Logical & Consistency Checks
Condition vs. Age: Cross-references an asset's In-Service Date with its Condition against a BOMA-standard Expected Useful Life (EUL) map to flag:
New assets (≤ 2 years old) in poor condition.
Old assets (past their EUL) in excellent condition.
Refrigerant & Date Inconsistency: Flags assets with an in-service date that is incompatible with the phase-out period of its listed refrigerant (e.g., an asset from 2022 using R-22).
Refrigerant in Comments: If Refrigerant is set to "Other (List in Comments)", the tool verifies that a valid ASHRAE refrigerant is actually present in the att_Validation Comment.
Parent/Child Asset Logic:
Verifies that a Duct Furnace (DF) has a parent asset, and that the parent is an Air Handling Unit (AHU).
Verifies that a Forklift Battery (FLB) is correctly parented to a Forklift (FRK).
Building Type Appropriateness: Flags assets that are specific to Welfare/Storehouse facilities if they appear in a Meetinghouse report.
4. Formatting & Syntax Rules
Refrigerant Validation: Checks the Refrigerant field against a comprehensive list of ASHRAE-standard refrigerants.
Room Naming Conventions:
Enforces proper capitalization.
Flags room numbers that are missing a descriptive prefix (e.g., "101" instead of "Room 101").
Checks for correct use of apostrophes in common names (e.g., "Bishop's Office").
Disallows abbreviated cardinal directions (e.g., "SW").
No Spaces in Model/Serial: Ensures that model and serial numbers do not contain spaces.
Capacity Unit Validation: Checks that the att_Capacity Unit is valid for the specific asset type. Supports optional capacity entries for assets like Fire Risers (FSSD/FSSW) and Fire Alarm Panels (FACP).
How to Use
Open the HTML File: Launch https://frillyllama.github.io/QAQC-CHURCH-VALIDATION-TOOL/ in any modern web browser.
Upload Your File: Drag and drop your CSV export from Ninox onto the designated area, or click to browse and select the file.
Select Building Type: Choose either "Meetinghouse" or "Welfare" from the dropdown menu. This is critical for the tool to apply the correct set of rules.
Run the Audit: Click the "Run Internal Audit" button.
Review the Report: The results will appear below, organized by Work Zone. Each section is collapsible.
Download Results:
Download Detailed CSV: Exports a spreadsheet listing every individual error found.
Download Summary CSV: Exports a high-level summary of error counts by category for each work zone.
Understanding the Report
Error Summary: A high-level overview of the number of errors found in each category.
System Verifications: A checklist of core building systems and their status (Found, Missing, Not Found).
Error Details: A detailed table listing every error, including the asset number, row number, the field with the error, the invalid data, and the reason it was flagged.
Changelog - v4.1 (09/12/2025)
New Feature: Added Condition vs. Age Inconsistency Check using a BOMA-standard EUL map.
New Feature: Added Refrigerant & In-Service Date Validation to check for phase-out conflicts.
Enhancement: Upgraded the Model Number Inconsistency Check to be global (dataset-wide) and to use a "majority wins" logic for higher accuracy. The check now covers Manufacturer, Capacity, and Refrigerant.
Enhancement: Implemented "System Verifications" for Storehouses, which now correctly checks for required welfare facility assets.
Fix: The internal refrigerant list was expanded to be comprehensive based on ASHRAE standards.
Fix: Added logic to handle "Other (List in Comments)" as a valid refrigerant entry and verify the associated comment.
Fix: Updated validation rules to make capacity entry optional for Fire Risers (FSSD/FSSW) and Fire Alarm Panels (FACP).
](https://frillyllama.github.io/QAQC-CHURCH-VALIDATION-TOOL/)
