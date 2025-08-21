# Modeling in Power BI


## Tables
- **Advisories** (from Excel curated CSV)
- **Date** (calculated table) – marked as Date table


## Relationships
- `Date[Date]` → `Advisories[Created]` (active)
- `Date[Date]` → `Advisories[Updated]` (inactive)
- `Date[Date]` → `Advisories[Due]` (inactive)


Use `USERELATIONSHIP` in measures when slicing by `Updated` or `Due` dates.


## Date table
```DAX
Date =
VAR MinDate = MINX(ALL(Advisories), Advisories[Created])
VAR MaxDate = MAXX(ALL(Advisories), COALESCE(Advisories[Due], Advisories[Updated]))
RETURN CALENDAR(MinDate, MaxDate)
