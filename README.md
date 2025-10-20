# Asset Data QA/QC Analyst Tool v4.1

## Overview

The Asset Data QA/QC Analyst Tool is a powerful, browser-based application designed to internally audit asset data exported from Ninox. It automates the quality control process by running a comprehensive set of validation checks against a CSV file, identifying common errors, inconsistencies, and logical discrepancies.

This tool helps ensure data integrity before it is used for analysis, reporting, or system import, saving significant time and reducing manual errors.

## Key Features & Validation Rules

The tool performs a wide array of checks, categorized for clarity.

### 1. Global & Work Zone Analysis

* **Global Model Inconsistency:** Scans the *entire dataset* to find assets with the same model number but conflicting `Manufacturer`, `Capacity`, or `Refrigerant` data. It uses a "majority wins" rule to identify the correct data and flag only the true outliers.

* **Work Zone Duplicate Assets:** Identifies any duplicate `att_Asset #` within the same `WORK ZONE`.

* **System Verifications:** Checks for the presence of core building systems (e.g., Roof, Parking Lot, Fire Alarm) based on the selected building type (Meetinghouse or Welfare/Storehouse). The report shows if a system is `Found`, `Missing`, or `Documented Not Found`.

* **HVAC Split System Count:** Verifies that the number of indoor split system units matches the number of outdoor units within a work zone.

### 2. Data Integrity & Required Fields

* **Required Fields:** Ensures critical fields like `att_Asset #`, `att_In-Service Date`, and `att_NGM CA-Condition` are not blank.

* **Template Row Handling:** Flags any template assets that have not been properly marked for deletion (`TagID` = "DELETE").

* **"Not Found" Asset Validation:** Enforces strict rules for assets marked as "Not Found," ensuring location fields and nameplate data are filled out correctly and that the validation comment is appropriate.

* **Photo Requirement:** Checks that each asset has at least three photos.

### 3. Logical & Consistency Checks

* **Condition vs. Age:** Cross-references an asset's `In-Service Date` with its `Condition` against a BOMA-standard Expected Useful Life (EUL) map to flag:
    * New assets (≤ 2 years old) in poor condition.
    * Old assets (past their EUL) in excellent condition.

* **Refrigerant & Date Inconsistency:** Flags assets with an in-service date that is incompatible with the phase-out period of its listed refrigerant (e.g., an asset from 2022 using R-22).

* **Refrigerant in Comments:** If `Refrigerant` is set to "Other (List in Comments)", the tool verifies that a valid ASHRAE refrigerant is actually present in the `att_Validation Comment`.

* **Parent/Child Asset Logic:**
    * Verifies that a Duct Furnace (`DF`) has a parent asset, and that the parent is an Air Handling Unit (`AHU`).
    * Verifies that a Forklift Battery (`FLB`) is correctly parented to a Forklift (`FRK`).

* **Building Type Appropriateness:** Flags assets that are specific to Welfare/Storehouse facilities if they appear in a Meetinghouse report.

### 4. Formatting & Syntax Rules

* **Refrigerant Validation:** Checks the `Refrigerant` field against a comprehensive list of ASHRAE-standard refrigerants.

* **Room Naming Conventions:**
    * Enforces proper capitalization.
    * Flags room numbers that are missing a descriptive prefix (e.g., "101" instead of "Room 101").
    * Checks for correct use of apostrophes in common names (e.g., "Bishop's Office").
    * Disallows abbreviated cardinal directions (e.g., "SW").

* **No Spaces in Model/Serial:** Ensures that model and serial numbers do not contain spaces.

* **Capacity Unit Validation:** Checks that the `att_Capacity Unit` is valid for the specific asset type. Supports optional capacity entries for assets like Fire Risers (`FSSD`/`FSSW`) and Fire Alarm Panels (`FACP`).

## How to Use

1.  **Open the HTML File:** Launch the `index.html` file in any modern web browser.

2.  **Upload Your File:** Drag and drop your CSV export from Ninox onto the designated area, or click to browse and select the file.

3.  **Select Building Type:** Choose either "Meetinghouse" or "Welfare" from the dropdown menu. This is critical for the tool to apply the correct set of rules.

4.  **Run the Audit:** Click the "Run Internal Audit" button.

5.  **Review the Report:** The results will appear below, organized by Work Zone. Each section is collapsible.

6.  **Download Results:**
    * **Download Detailed CSV:** Exports a spreadsheet listing every individual error found.
    * **Download Summary CSV:** Exports a high-level summary of error counts by category for each work zone.

## Understanding the Report

* **Error Summary:** A high-level overview of the number of errors found in each category.

* **System Verifications:** A checklist of core building systems and their status (`Found`, `Missing`, `Not Found`).

* **Error Details:** A detailed table listing every error, including the asset number, row number, the field with the error, the invalid data, and the reason it was flagged.

## Changelog - v4.1 (09/12/2025)

* **New Feature:** Added **Condition vs. Age Inconsistency Check** using a BOMA-standard EUL map.

* **New Feature:** Added **Refrigerant & In-Service Date Validation** to check for phase-out conflicts.

* **Enhancement:** Upgraded the **Model Number Inconsistency Check** to be global (dataset-wide) and to use a "majority wins" logic for higher accuracy. The check now covers Manufacturer, Capacity, and Refrigerant.

* **Enhancement:** Implemented **"System Verifications" for Storehouses**, which now correctly checks for required welfare facility assets.

* **Fix:** The internal refrigerant list was expanded to be **comprehensive based on ASHRAE standards**.

Asset Data QA/QC Analyst: Version 4.1.1 Change Log (2025-10-20)
This update introduces significant enhancements to the validation logic, improves data consistency checks across large datasets, and refines the user interface for a clearer, more actionable audit report.
🚀 New Features & Rule Enhancements
1. Global Model Inconsistency Analysis (Major Enhancement)
The tool now performs a powerful new check across the entire uploaded file to enforce model consistency.
	• Previous Logic: Model consistency was only checked within each work zone.
	• New Logic: The analyzeGlobalInconsistencies function now establishes a "majority rule" for each model number based not only on its manufacturer, capacity, and refrigerant, but also its asset acronym (e.g., RTU, AHU).
	• Benefit: This catches critical data entry errors where the same model number might be incorrectly assigned to different types of equipment across the entire project (e.g., a model XYZ-123 being used for both an RTU in one zone and an AHU in another). These global errors are now displayed prominently at the top of the report.
2. Intelligent HVAC Split System Counting
The indoor/outdoor HVAC unit count, which is critical for verifying system integrity, is now more accurate.
	• Previous Logic: All AHU (Air Handling Unit) assets were counted as indoor units.
	• New Logic: The tool now identifies self-contained or packaged AHUs by searching for keywords like package, roof top, or make up air unit in the asset description. These units are now correctly excluded from the indoor unit count, preventing false mismatches.
3. Granular "Required System" Checks for Flooring (Meetinghouse)
The validation for required flooring systems in Meetinghouses is now more precise.
	• Previous Logic: The tool performed a general check for the presence of any flooring type.
	• New Logic: The check is now split into two distinct requirements: "Flooring (Building Envelope)" and "Flooring (Cultural Center)". The tool now validates the presence of these specific flooring systems by cross-referencing the asset's att_Areas Served field, ensuring the right type of flooring is documented for the right area.
4. Robust Parent Asset Validation
The logic for verifying parent-child asset relationships has been completely overhauled for greater accuracy.
	• Previous Logic: Parent validation was based on a simple string search (e.g., checking if the parent tag contained "FRK"). This was unreliable.
	• New Logic: The tool now finds the parent asset in the dataset by its exact TagID and verifies that its acronym is correct. This ensures: 
		○ A Duct Furnace (DF) is correctly parented to an Air Handling Unit (AHU).
		○ A Forklift Battery (FLB) is correctly parented to a Forklift (FRK).
5. Additional Validation Rules
	• Forklift Capacity: A new rule requires that if a Forklift's (FRK) att_Capacity Unit is "Other (List In Comments)", the att_Validation Comment field must specify the capacity using the words pounds or lbs.
	• Room Name Formatting: Added a check to flag two-word cardinal directions (e.g., "North East") and recommend the correct compound word ("Northeast").
	• Coil Validation: The requirement to document the presence or absence of a cooling coil in the comments has been extended to Electric Furnaces (EF) and Air Handling Units (AHU) that have no refrigerant listed.
	• Unnecessary Parent Tags: The tool now flags assets that have a parent tag assigned but do not require one (e.g., an RTU with a parent).


🐛 Bug Fixes & Code Refinements
	• Smarter Redundant Comment Check: The logic that flags redundant comments (where the comment repeats the att_Areas Served field) is now more intelligent. It uses a synonym dictionary to avoid flagging legitimate comments that use different but related phrasing.
	• Corrected Global Error Reporting: Fixed a bug where global model inconsistency errors were incorrectly grouped with the first work zone's report. They are now correctly displayed in their own dedicated section.
	• Data Definition Correction: The validation rule for Forklift (FRK) capacity unit was updated from Tons to Other (List In Comments) to align with the new comment validation rule.
	• Work Zone Analysis Stability: Resolved an issue where duplicate asset number errors and local model inconsistency errors were not being correctly included in the downloadable CSV reports.
	• General Stability: Miscellaneous internal syntax errors were corrected to improve script reliability.


🎨 UI/UX Improvements
	• Dedicated Global Errors Section: Global model inconsistencies are now presented in a distinct, high-visibility collapsible section at the top of the report, bordered in red to draw immediate attention.
	• "Copy Table" Functionality: A "Copy Table" button has been added to each work zone's error details section, allowing users to easily copy all errors for that zone to their clipboard in a spreadsheet-friendly format.
	• "Not Found" Asset List: The report for each work zone now includes a clear, bulleted list of all assets that have been explicitly documented as "Not Found," improving visibility.
	• Clearer System Status: The "System Verifications" summary now uses icons (✅ Found, ⚠️ Documented Not Found, ❌ Missing) for at-a-glance readability.
<img width="740" height="1525" alt="image" src="https://github.com/user-attachments/assets/e8fd8af8-e850-4c63-87de-c9dc41370b32" />


* **Fix:** Added logic to handle **"Other (List in Comments)"** as a valid refrigerant entry and verify the associated comment.

* **Fix:** Updated validation rules to make capacity entry **optional** for Fire Risers (FSSD/FSSW) and Fire Alarm Panels (FACP).
