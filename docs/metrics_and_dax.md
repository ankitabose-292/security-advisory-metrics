```markdown
# Metrics & DAX


## KPI definitions
- **Open Items**: `Status ∈ {Open, In Progress, Blocked}`
- **% Completed**: Closed / All
- **% Overdue**: Open with `Due < Today()` / Open
- **High/Critical Mix**: `(High + Critical) / All Open`
- **Blocked Aging (median)**: median `Days Open` where Status = Blocked


## Representative measures
```DAX
Open Items = COUNTROWS(FILTER(Advisories, Advisories[Status] <> "Closed" && Advisories[Status] <> "Cancelled"))


Completed Count = CALCULATE(COUNTROWS(Advisories), Advisories[Status] = "Closed")


Pct Completed = DIVIDE([Completed Count], COUNTROWS(Advisories))


Overdue Count = COUNTROWS(FILTER(Advisories, Advisories[Status] <> "Closed" && NOT ISBLANK(Advisories[Due]) && Advisories[Due] < TODAY()))


Pct Overdue = DIVIDE([Overdue Count], [Open Items])


HighCritical Open = COUNTROWS(FILTER(Advisories, Advisories[Status] <> "Closed" && Advisories[Risk Label] IN {"High","Critical"}))


HighCritical Mix = DIVIDE([HighCritical Open], [Open Items])


Days Open (measure) =
VAR Created = SELECTEDVALUE(Advisories[Created])
VAR Closed = SELECTEDVALUE(Advisories[Updated])
RETURN IF(SELECTEDVALUE(Advisories[Status]) = "Closed", DATEDIFF(Created, Closed, DAY), DATEDIFF(Created, TODAY(), DAY))


Closures by Updated =
CALCULATE([Completed Count], USERELATIONSHIP(Date[Date], Advisories[Updated]))
