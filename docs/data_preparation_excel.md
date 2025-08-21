# Data Preparation (Excel)


**Source columns**: Project, Summary, Task, Status, Assignee, Reporter, Created, Updated, Due, Risk Label.


## Cleaning rules
1. **Trim & clean text**
- Helper columns: `=TRIM(CLEAN([@Project]))`, etc.
- Normalize casing for `Status` and `Risk Label`: `=PROPER([@Status])` then map via a table.


2. **Standardize Status & Risk Label**
- Mapping table (Sheet `Lookups`):
- Status → {Open, In Progress, Blocked, Closed, Cancelled}
- Risk Label → {Low, Medium, High, Critical}
- Apply with `XLOOKUP([@Status], Lookups[From], Lookups[To], [@Status])`.


3. **Dates to proper types**
- Ensure date serials: `=DATEVALUE(TEXT([@Created],"yyyy-mm-dd"))` where needed.
- Validate chronology: flag issues where `Updated < Created` or `Due < Created`.
- `Bad Dates? = IF(OR([@Updated]<[@Created],[@Due]<[@Created]), "Y", "")`


4. **Identify duplicates** (by `Task`)
- `=IF(COUNTIF([Task],[[@Task]])>1, "Duplicate","")`


5. **Derive analysis fields**
- `Days Open = IF([@Status]<>"Closed", TODAY()-[@Created], "")`
- `Days To Close = IF([@Status]="Closed", [@Updated]-[@Created], "")`
- `Overdue? = IF(AND([@Status]<>"Closed",[@Due]<>"", TODAY()>[@Due]), "Y","N")`
- `At Risk? = IF(AND([@Risk Label] IN {"High","Critical"}, [@Overdue?]="Y"), "Y","N")`


6. **Export**
- Create a final table/range `tblAdvisories_Curated` with the cleaned & derived columns and **Export to CSV** for Power BI.


## Notes
- The messy mock used: missing Status/Due, inconsistent Risk Label values (HIGH/hi/CRIT), and a few duplicate Task IDs.
- All fixes above are reproducible with formulas—no VBA required.
