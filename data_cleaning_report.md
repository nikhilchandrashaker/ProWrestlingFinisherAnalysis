# Data Cleaning Report — Wrestling Match Quality Analytics

## 1. Promotion Event Tables

- AEW events: 1,098 rows loaded, date range 2019-05-25 to 2025-12-31
- WWE events: 5,970 rows loaded, date range 2015-01-04 to 2025-12-30
- NJPW events: 2,164 rows loaded, date range 2015-01-04 to 2025-12-22

**Key data quality issue:** 6,601 of 9,228 events (72%) share a Date+Promotion with at least one other event (e.g. RAW and Superstars taped same night, or two house shows in different cities same day). The data dictionaries' suggested join key (Date + Promotion) is therefore ambiguous for these rows. No EventID exists in the raw match-rating files to disambiguate, so any match-to-event join inherits this ambiguity — flagged explicitly below rather than silently guessed.

Saved `events_clean.csv` — 9,228 rows, 3 promotions.

## 2. Match Rating Tables

- AEW matches: 4,125 rows | CageMatchRating range 0.46-9.68 | WONStarRating range 0.50-6.50
- WWE matches: 8,101 rows | CageMatchRating range 0.29-9.68 | WONStarRating range 0.00-5.50
- NJPW matches: 7,426 rows | CageMatchRating range 0.63-9.82 | WONStarRating range 0.00-7.00

Note: CageMatchRating is on a 0-10 scale; WONStarRating is on a 0-5 star scale. These are NOT directly comparable and should not be averaged together without normalizing (e.g. WONStarRating * 2 to approximate a 0-10 scale, or z-scores within each system).

3,856 of 19,652 matches (19.6%) have neither a CageMatch nor a WON rating (unrated matches, e.g. squash matches or dark matches).

Saved `match_ratings_clean.csv` — 19,652 rows.

## 3. Match-to-Event Join

- 8,012 matches join to exactly one event (clean join).
- 11,228 matches join to 2+ possible events on the same date (ambiguous — event-level fields like EventName/EventType are stored as a list rather than guessed).
- 412 matches have no matching event date in the events table at all.

Saved `matches_with_event_context.csv` — 19,652 rows. Use EventName_ifUnambiguous / EventType_ifUnambiguous for analyses that need clean event context (these are blank when the join was ambiguous); use EventNames/EventTypes (list columns) if you want to inspect or manually resolve the ambiguous cases.

## 4. John Cena Career Dataset

- MatchesTable used ISO dates (YYYY-MM-DD); ChampionshipTable/LocationTable used DD.MM.YYYY — both parsed and standardized to a single date type.

Saved `cena_career_clean.csv` — 2,326 rows, one row per match, all 3 source tables merged on Match_ID with consistent dates.

Cena career span: 1999-11-05 to 2025-12-13 | Record: 1830-438-58 (W-L-NC)