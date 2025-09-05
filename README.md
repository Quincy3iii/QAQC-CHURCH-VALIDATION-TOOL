# Asset Data QA/QC Analyst v4.1 - README

## Overview

The **Asset Data QA/QC Analyst** is a browser-based tool designed to automate the quality control process for asset data. It validates a CSV export against a comprehensive set of rules to ensure data integrity, formatting consistency, and completeness.

The tool groups assets by their "WORK ZONE," runs a series of checks on each asset and on the zone as a whole, and then generates a detailed report of all errors found. This allows for rapid identification and correction of data issues before final submission.

---

## How to Use

Using the tool is a simple, four-step process:

1.  **Open the File**: Using the most recent GitHub.io link; https://quincy3iii.github.io/QAQC-CHURCH-VALIDATION-TOOL/.
2.  **Upload Data**: Drag and drop your CSV data file onto the upload area, or click the area to select the file from your computer.
3.  **Select Building Type**: Choose the appropriate building type from the dropdown menu that appears. This selection is crucial as some validation rules are specific to the building type.
4.  **Run Audit**: Click the **"Run Internal Audit"** button to start the validation process.
5.  **Review & Download**: The results will appear below in the "Audit Report" section, organized by Work Zone. You can expand or collapse each zone's report. From here, you can download two different CSV reports:
    * **Download Detailed CSV**: A row-by-row list of every error found across all work zones.
    * **Download Summary CSV**: A high-level summary of the total count of errors per category for each work zone.



---

## Function & Rule List

The following is a comprehensive list of all validation checks performed by the tool.

### 1. File Handling & Templates

These rules ensure that template rows, which are not real assets, are handled correctly.

* **Template Deletion**: Any asset with an `att_Asset #` starting with `TEMPLATE` must have its `TagID` set to `DELETE`.
* **Deletion Formatting**: If an asset's `TagID` is `DELETE`, it must also have:
    * `att_Space (Floor)` = `"Not Found"`
    * `att_Room` = `"Template"`
    * `Status` = `"Offline"`

### 2. "Not Found" Assets

These rules apply to assets that are expected but could not be located during the survey.

* **Condition**: This rule set applies when `Reason Not Tagged` is set to `"Not Found"` and the `TagID` field is empty.
* **Required Fields**:
    * `att_Space (Floor)` must be `"Not Found"`.
    * `att_Room` must be `"Not Found"`.
    * `att_Areas Served` cannot be blank.
    * `att_Manufacturer`, `att_Model`, and `att_Serial #` must all be set to `"No Nameplate Information Available"`.
* **Required Comment**: The `att_Validation Comment` must contain the phrase `"No [ACRONYM] found"` (e.g., `"No RTU found"`). The only exception is for `FLW` (Wood Flooring), which must contain `"no cultural center flw found"`.

### 3. Formatting & Syntax Errors

These rules check for common formatting mistakes.

* **Model/Serial Numbers**: The `att_Model` and `att_Serial #` fields should not contain any spaces (unless the value is `"No Nameplate Information Available"`).
* **Room Number Formatting**:
    * A room number cannot be a standalone number (e.g., `"112"`). It must have a descriptive prefix (e.g., `"Classroom 112"`).
    * All words in the room name must be capitalized (e.g., `"Mechanical Room"`, not `"Mechanical room"`).
    * Cardinal directions must be spelled out (e.g., `"Southwest"` instead of `"SW"`).
    * Common possessive names must include an apostrophe (e.g., `"Bishop's Office"` instead of `"Bishops Office"`).
* **Redundant Comments**: The `att_Validation Comment` should not repeat information already present in the `att_Areas Served` field.
* **Invalid Acronyms**: The acronym derived from the `att_Asset #` (e.g., `RTU` from `RTU-1`) must be a known acronym in the tool's database.

### 4. Missing Required Data

These checks ensure that critical fields are never left blank.

* **Asset Number**: `att_Asset #` cannot be empty.
* **Date & Condition**: `att_In-Service Date` and `att_NGM CA-Condition` cannot be blank unless `Reason Not Tagged` is `"Tag on PM (put reason in comments)"`.
* **Photo Requirement**: Each asset must have at least **3 photos**.
* **Gas Furnace Comment**: For a `Gas Fired Furnace`, the `att_Validation Comment` must either state `"No coil attached."` or mention the word `"Coil"` along with a valid refrigerant type (e.g., R-22, R-410A).
* **Parent Asset**:
    * A Duct Furnace (`DF`) must have a parent asset, and that parent must be an Air Handling Unit (`AHU`).
    * A Forklift Battery (`FLB`) must have a Forklift (`FRK`) as its parent.

### 5. Data Consistency & Logic

These rules validate the relationships between different data fields.

* **Capacity Unit**: The `att_Capacity Unit` must match the allowed unit(s) for that asset's acronym. For example, an `RTU` must have a capacity unit of `TON`. If an asset does not require a unit, its unit must be `N/A` and its `att_Capacity Quantity` must be `0`.
* **Location vs. Environment**:
    * If `att_Space (Floor)` is `"Attic"`, the `Environment` must be `"Wide variation in temp/humidity/dust"`.
    * If `att_Space (Floor)` is `"Roof"`, the `Environment` must be `"Extremes of temperature"`.
* **Refrigerant**: The `Refrigerant` field should be blank for assets that do not use refrigerant (e.g., flooring, boilers, parking lots).
* **Nameplate Data**: If `att_Manufacturer` is `"No Nameplate Information Available"`, then `att_Model` and `att_Serial #` must also be `"No Nameplate Information Available"`.
* **Building Type**: An asset type designated for "Welfare" facilities (e.g., `DOCK`, `FRK`, `COOL`) cannot be present in a "Meetinghouse" file.
* **FLC-1 Asset**: For the specific asset `FLC-1`:
    * `att_Room` must be `"Building Interior"`.
    * `att_Areas Served` must be `"Building Envelope"`.

### 6. Work Zone-Wide Checks

These checks analyze the entire set of assets within a single Work Zone.

* **Duplicate Asset #**: The same `att_Asset #` cannot appear more than once within the same Work Zone.
* **HVAC Split System Count**: The number of indoor split system units (`ACU`, `AHU`, etc.) should match the number of outdoor units (`CU`, `CTU`). A mismatch is flagged.
* **Required Meetinghouse Systems**: When the "Meetinghouse" building type is selected, the tool verifies that at least one of each of the following systems is accounted for (either found or documented as "Not Found"):
    * Sound System (`SS`)
    * Fire Alarm Panel (`FACP`)
    * Fire Suppression System (`FSSD`, `FSSW`, or `FSSC`)
    * Roof System
    * Parking Lot
    * Flooring
