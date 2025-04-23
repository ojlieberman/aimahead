# aimahead
Dependencies:
install.packages("ggplot2")
install.packages("ggplot2")

**#obsper24hr**
OMOP sometimes splits ICU stays into separate visit_detail_ids even when they’re clearly continuous. To count measurements in the first true 24 hours of ICU stay, we need to:
✅ Plan
Group contiguous ICU visit_detail rows per person_id.
Merge them into a single interval if the start of one equals the end of the previous.
Filter only those with ≥24h duration.
Join with measurement for your target measurement_concept_id = 40762499.
Count measurements in first 24h.

**#obs per hour - first hour in ICU**
**v1**
Still find the start time of a contiguous ICU stay.

Count how many times measurement_concept_id = 40762499 appears in the first hour only.

Then compute count / 1 hour = count per hour, which in this case is just the raw count per patient for that hour.
Limit to each patient’s first ICU encounter.

Count measurements in the first hour of that first ICU stay.
**v2**
v1 only included the first ICU stay even if that duration was <1hr. To fix this we do the following:
✅ Goal:
Chain together contiguous ICU visit_detail segments until the combined duration exceeds 1 hour, and then:

Take the start time of that combined block

Count SpO₂ measurements (measurement_concept_id = 40762499) within the first hour from that point

⚙️ Logic Overview:
Pull all ICU visit_details.

Chain together contiguous segments per person_id.

Accumulate duration sequentially, stopping once the chain reaches ≥1 hour.

Take that chain’s start time.

Count SpO₂ measurements in first hour from that start.

🧠 Implementation Strategy:
We'll build this in steps using PostgreSQL-compatible SQL. Since SQL doesn't do loop-based accumulation easily, we'll use window frames and cumulative duration until we reach 1 hour.

But PostgreSQL also doesn't easily support "take the shortest number of rows that cumulatively exceed X" in pure SQL — so the cleanest way is to extract the raw chained visit data in R, and then:

Group and accumulate time per person

Find the first ≥1 hour block

Then query measurements for just those

