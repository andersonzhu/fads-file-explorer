# FADS File Explorer

Open a THECB Financial Aid Database (FAD) report file in your browser, read every student record as named, filterable columns, and find the records the FAD edit checks would flag before you upload to CBPass. You can also correct records and save the file back out.

**Use it:** open `FADS File Explorer.html` in Chrome or Edge (double-click the file). There's nothing to install.

Everything runs in your browser tab. Files are read locally and never uploaded, so it's safe to use with student-level data. SSNs are masked on screen by default.

This is an independent tool built from THECB's published FAD Report Manuals. It isn't made or supported by the Texas Higher Education Coordinating Board.

## What it does

- **Reads FAD files for FY 2025 and FY 2026.** It recognizes the HY2K header, the F student records and the EOF1 trailer. It picks the layout from the reporting year in the header: FY 2025 records are 481 characters (121 data elements), and FY 2026 records are 486 (122 data elements). Tab-delimited files saved from Excel open too.
- **Splits every record into its data elements.** Each column is labeled with its data element number and character positions, for example `#50 · 186–190` for Federal Pell Grant, so it lines up with the manual.
- **Filters and sorts.** Type in the box under any column, or use the column's value list with counts. The box at the top searches the whole raw record, so it finds an SSN, ID or name anywhere.

  | Type | Meaning |
  |---|---|
  | `GARC` | contains GARC (codes also match their labels, so `hazlewood` finds every Hazlewood exemption code) |
  | `=2` / `=1,2` | exactly 2 / exactly 1 or 2. Leading zeros don't matter in numeric fields, so `=500` finds `00500`. |
  | `!=00` | anything except 00 |
  | `>0` | compare numbers: `>0` on an award finds everyone who received it |
  | `<12` | hours compare in hours, so `<12` on fall hours means under 12 hours |
  | `>20250101` | dates are MMDDYYYY in the file but compare as YYYYMMDD, so this means after January 1, 2025 |
  | `1000..5000` | a range, inclusive |
  | `=` | blank |
- **Shows values the way people read them.** Dollar amounts show without leading zeros (`00500` shows as 500), and zero amounts are grayed. Hours show as hours (`095` is 9.5). Codes show their meaning: Justification `12` is "Completed FAFSA/TASFA but attended no terms", Exemption code `09` is "Hazlewood veteran (credit hours)", and Program Level `2` is "Associate degree".
- **Runs the manual's edit checks.** Every record is checked, and so are the header, the trailer and the file as a whole. See [Checks](#checks).
- **Inspects one record.** Select a row to see the raw line under a character ruler, with every data element shaded and matched to its column. The record's problems are listed at the top; choose one to jump to its field.
- **Profiles fields.** The Fields view shows each data element's type (amount, hours, code, date, number, text or filler), the share of records that report it (amounts and hours count only when more than zero), and the most common values. Award and loan columns show a **dollar total** for the records shown, which helps when reconciling against THECB's Reconciliation Report.
- **Keeps the grid manageable.** Columns that are blank in every record stay hidden until you turn on **Empty columns**. Filler data elements stay hidden until you turn on **Filler**.
- **Exports.** Copy the table for Excel, or save it as CSV. Dollar amounts and hours come out as numbers, so Excel can add them up. Codes keep their leading zeros.
- **Edits, one record or many.** Correct a single record, or set one value in every record your filters show. Every change can be undone, and History lists exactly what changed.

## Checks

Choose **Checks** on the toolbar for a list of every problem in the file, with the number of records for each. **Show records** filters the grid to just those records. The red and amber chips in the file summary do the same for all records with errors or warnings. In the grid, cells with a problem are shaded, and hovering one shows the message.

### Institution type

Some checks depend on the kind of institution: public institutions can't report TEG or a tuition differential, community colleges can't award TEXAS Grant, TEOG has a different maximum at each type of two-year college, and so on. Choose yours from **Institution type** at the top of the Checks view. It starts as **Public community college** and is remembered in your browser.

### Header, trailer and whole file

| Check | What it looks for |
|---|---|
| HY2K header record | File label HY2K, a 6-digit FICE, data identifier FAD001, reporting cycle 2 or 3, a reporting year that matches the layout, record type C, length of data 0481 or 0486, and a record count that matches the student records |
| EOF1 trailer record | File label EOF1 and a record count that matches the student records |
| Need analysis mix | Error when more than 75% of records have need analysis (#23) 2; warning between 25% and 75%, or when every record has 1 |
| No first-time students | Warning when no record has 000001 in #92 |

### Student records

**Errors** are what the FAD edit report would list as errors:

| Check | What it looks for |
|---|---|
| Report code isn't F / Record is the wrong length | Fixed-width structure problems |
| Not digits in every position | Numeric data elements are right justified with leading zeros, and every position is required, even when reporting zero. Excel often drops the zeros. |
| Filler isn't zeros | Fillers, including the ones that replaced retired fields, must be all zeros |
| Not one of the manual's codes | Any coded data element: justifications, exemption/waiver codes, program level, residency, and so on |
| Not a valid date | Date of birth (#35) and FAFSA/TASFA date (#106) must be real MMDDYYYY dates |
| Doesn't match the header | FICE (#2), reporting cycle (#3) and reporting year (#4) must match the header |
| SSN/ID or student ID missing | #5 and #95 can't be blank or all zeros, and #5 has no spaces |
| SSN isn't valid | FY 2026 only (a warning in FY 2025): an SSN starting with 000, 666 or 9, or with 00 in the middle or 0000 at the end. A student ID in #5 is fine when #95 holds the same ID. |
| Name isn't valid | Last and first names are required, have no numbers, and start with a letter. The middle initial is a letter or blank. |
| ZIP code isn't valid | 000000000, or starting with 00007 |
| Date of birth out of range | Birth year outside the manual's range (1901–2013 for FY 2026), or an age under 13 or over 124 |
| Residency code not allowed | Residency 4, or 3 (unknown) at a public institution |
| Justification 11 needs an override | Code 11 stays an error until THECB approves the justification (override) request |
| Need analysis, SAI and COA don't agree | For example, need analysis 1 with SAI 9999999 or COA 999999, need analysis 2 with a real SAI, COA 0 with need-based aid, or need-based aid with need analysis 2 |
| Award more than COA minus SAI | Need-based awards over COA − SAI, plus $300 for TEXAS Grant, TEOG, FWS, TCWS and WSMP. COA is adjusted by #93 when it's filled. Justification 18 lifts the limit. |
| More than the program allows | Award maximums from the manual (Pell, FSEOG, TEOG by institution type, TASSP, Direct Loans, TEACH and others), and loans more than the COA |
| Exemption/waiver fields don't agree | An award amount without a code, a code or impact without an amount, awards out of order, inactive codes, and misuse of #48 |
| Not allowed at this type of institution | Aid, program levels and classifications your institution type can't report |
| Student isn't eligible for this aid | State aid for a nonresident, without attempted hours, below half-time without a hardship, or for graduate students; TEXAS Grant, TEOG and TEG combined; Pell for an ineligible program level; Direct Loans below half-time without a justification; gender U with state aid |
| Award details don't match the award | Award type, semester of first award, priority model, pathway, SAP, job location and match (#55, #57, #70–#72, #103–#105, #110–#112, #117) filled exactly when their award is |
| Selective Service doesn't fit | #97 above 0 for a female student; male students with need analysis 1 and no code, or not registered but receiving state aid or exemptions |
| No race reported | None of #26–#32 reported |
| Enrollment status doesn't match hours | For undergraduates (#33 is 1 or 5), the term with the most attempted hours sets the status: 12 or more full-time, 9–11 three-quarter time, 6–8 half-time, under 6 less than half-time. Students with no hours in any term need a justification (12 if they never attended). |
| Attempted hours problem | Term hours over 80 (60 for graduate levels outside health-related institutions), cumulative hours over 700, or cumulative hours of 0 when term hours are reported |
| Tuition and fees don't match hours | No tuition and fees in any term, or tuition and fees in a term with no attempted hours, without a justification |
| First-time code doesn't match | Classification 5 (first-time in college) without 000001 in #92 |
| FAFSA/TASFA date problem | Missing with need analysis 1, or outside the manual's window (12/30/2024–9/12/2026 for FY 2026) |
| SSN/ID on more than one record | THECB expects one record per student |
| Value longer than its field | A tab-delimited value has more characters than the data element allows |

**Warnings** are worth a look:

| Check | What it looks for |
|---|---|
| SSN may not be valid | The FY 2025 version of the SSN check |
| Student ID on more than one record | Two or more records share #95 |
| COA looks off | COA 0 with need analysis 1, or a term's tuition and fees more than the COA |
| SAP code 3 isn't allowed | SAP code 3 for TEXAS Grant or TEOG |

### Rules that needed interpretation

The manual's wording is ambiguous in a few places. The explorer reads it this way:

- **Enrollment status** is compared with the single term that has the most attempted hours, because the manual defines #42 as the highest enrollment status during the year.
- Where an edit says "hardship status (#59) or hardship provision (#58) = 0", either one being 0 counts.
- Where an edit lists both "more than COA − SAI" and "more than COA − SAI & EAP > 0", the COA is multiplied by the enrollment adjustment percentage (#93) when it's filled.

### What the checks can't see

These checks only see what's in the file. Edits that compare your file with your other THECB reports, such as the cycle 3 match of SSN/ID and residency against CBM0C1 and the Unmatched SSN Report, still appear only in CBPass. A clean Checks view means the file follows the manual, not that the edit report will be empty.

## Editing records

1. Select a row, then choose **Edit record** (or double-click the row).
2. Change values. Each one is checked as you type, and the raw record and the record's list of problems update live, so you can see a problem clear before you apply. You don't need to type leading zeros: `500` in an amount saves as `00500`, `1` in a justification saves as `01`, and `9.5` in an hours field saves as `095`. Clearing a numeric box saves zeros.
3. Press **Enter** to apply, or **Esc** to cancel. Edited records and cells are marked in violet, and hovering an edited cell shows the old value.
4. Choose **Save as text file**. The saved file matches the original except for the characters you changed: the same record order, line endings and encoding. **The header and trailer record counts are recalculated** to match the records.

A tab-delimited file is always saved as fixed-width text, because THECB accepts only fixed-length files. The header and trailer are padded to the record length, like THECB's own files.

In Chrome and Edge, saving opens a Save As dialog, and later saves write to the same file. Other browsers download the file.

Edits live only in the open tab until you save. Closing a file (the × on its tab) with unsaved edits asks whether to save first. Closing the whole browser tab shows only the browser's own generic "Leave site?" warning, because browsers don't allow pages to customize it.

### Changing many records at once

1. Search or filter so that only the records you want to change are shown. The Checks view's **Show records** is a quick way to do this.
2. Open the arrow on the column's header and expand **Change values in the N records shown**.
3. Type the new value and choose **Set**. The value is checked the same way as a single edit, and you confirm the number of records before anything changes.

For example, to add justification 12 for students who never attended: filter Attempted Hours, Fall; Spring; and Summer to `=0`, check the records shown, then set Justification to `12`.

Records that already have the value are skipped. Records that no longer match your filters drop out of view.

### Undo, redo and the change list

Every change is one step: a record edit, a bulk change, putting a record back, or **Undo all edits**. **History** on the toolbar lists the steps, newest first, with what changed in each.

- Choose **Undo** on the latest step, or **Undo + N later** on an older step to go back further. Undone steps stay in the list with **Redo** until you make a new change.
- **Ctrl+Z** undoes and **Ctrl+Y** (or **Ctrl+Shift+Z**) redoes, as long as you're not typing in a box.
- **Copy change list for Excel** copies one row per changed value: record number, student ID (#95), data element, field, original value and new value. Use it to document your corrections. Rows are identified by student ID rather than SSN, so the list shows SSNs only if you changed #5 itself.

History lasts while the file is open in the tab. If you undo past the point where you saved, the file shows as unsaved again.

## SSN masking

**Mask SSNs** is on by default. The grid, record panel and value lists then show only the last four characters of #5. Search and filters still match the full number. Exports, copied records and saved files always keep the full SSN. Turn the switch off to see full numbers on screen.

## The file layout

The built-in layouts come from the File Layout section of THECB's FAD Report Manual:

| Record | Starts with | Contents |
|---|---|---|
| Header | `HY2K` | FICE, `FAD001`, reporting cycle, reporting year, `C`, length of data (0481 or 0486), record count |
| Student | `F` | One per student: identity, exemptions and waivers, need analysis, demographics, COA and SAI, every grant, scholarship, work-study and loan, attempted hours, tuition and fees, and SAP |
| Trailer | `EOF1` | Record count (student records only, not the header and trailer) |

FY 2026 turned five FY 2025 data elements into fillers (#24 living arrangement, #37 a second SAI, #44 and #45 parent education, #98 defaulted loan) and added #122 Texas Leadership Research Scholars at positions 482–486.

Dates are MMDDYYYY. Hours are in tenths with no decimal point. Dollar amounts are whole dollars, right justified with leading zeros.

Manuals:

- FY 2026: <https://reportcenter.highered.texas.gov/agency-publication/guidelines-manuals/financial-aid-database-fy-2026-report-manual/>
- FY 2025: <https://reportcenter.highered.texas.gov/agency-publication/guidelines-manuals/financial-aid-database-fy-2025-report-manual/>

## A new reporting year

A file whose header has a year with no built-in layout opens with the latest layout of the same record length, and the file summary says so. Check the new manual's update pages before relying on it: new data elements, retired fields, code lists and award maximums change most years. To adjust a layout yourself, open **Layouts**, choose a built-in layout, and choose **Edit a copy**. Copies keep the checks, because each line carries its tags. Award maximums and other year-specific limits are built into the explorer, so a new year's limits need an update to the HTML file.

## Adding your own layouts

Open **Layouts → New layout** and paste the rows of a record layout, one field per line: a name, then its start position and length (or start–end). Rows copied from Excel work. Optional tags at the end of a line: `[unused]`, `[num]`, `[money]`, `[dec=1]`, `[mdy]`, `[codes=EXEMPT]`, `[key=pell]`.

Layouts you make are saved in your browser only. To share them:

- **With a colleague:** Layouts → Share → Copy export, then they paste it and choose Import.
- **With everyone who uses your copy:** paste the export between the `office-layouts` script tags near the top of the HTML file.

## Limitations

- **Only the FAD report is built in.** Open CBM files in the CBM File Explorer.
- **Excel workbooks can't be opened directly.** In Excel, choose Save As → Text (Tab delimited), then open that file.
- **Code meanings and checks** were transcribed by hand from the manuals. The field positions were checked to chain with no gaps or overlaps, and the checks were tested against a real FY 2025 file, but spot-check any code label or rule you rely on.
- **The sample file is made up.** It's a made-up community college (FICE 000000) whose students are reported by student ID instead of SSN, and a few records contain deliberate mistakes so the Checks view has something to show.
- Web fonts load from Google Fonts. Offline, the page falls back to system fonts.

## Data and privacy

No server, no analytics, no uploads. The page reads files with the browser's file APIs. Layouts you create and your display settings (code labels, SSN masking, empty columns, institution type) are stored in the browser's local storage on your computer.
