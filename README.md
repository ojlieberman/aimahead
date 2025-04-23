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
Still find the start time of a contiguous ICU stay.

Count how many times measurement_concept_id = 40762499 appears in the first hour only.

Then compute count / 1 hour = count per hour, which in this case is just the raw count per patient for that hour.
Limit to each patient’s first ICU encounter.

Count measurements in the first hour of that first ICU stay.


