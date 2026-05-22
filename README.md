[![DOI](https://img.shields.io/badge/DOI-10.82901%2Fnemar.on004849-blue)](https://doi.org/10.82901/nemar.on004849)

STRONG dataset

This is a placeholder dataset.

## NEMAR curation changes (2026-05-21)

BIDS validator: 1 error + 47 warnings → 0 errors + 36 warnings. Raw `.set`/`.fdt` binary payloads unchanged.

The original `HED_ERROR:TAG_EXPRESSION_REPEATED` is a real dataset annotation defect. Mechanism: `events.tsv` has 197 onsets where two rows share an `onset` (BIDS allows duplicate onsets). The BIDS-HED validator merges all HED annotations for rows that share an `onset` into one combined HED string per time bucket. The HED dictionary in `task-nback_events.json` assigns the same tag (`"Task"`) to ~80 of the ~107 distinct event codes, so any time two rows with different codes that both map to `"Task"` share an `onset`, the merged HED string becomes `"Task,Task"` and the validator legitimately fires a duplicate-tag error. 196 of the 197 collisions hit this pattern (the 197th collision pairs two codes with different HED tags, which merges cleanly).

### `dataset_description.json`
- Added `DatasetType: "raw"`.
- Added `GeneratedBy: [{Name: "nemar-cli", Version: "0.8.8", CodeURL: "https://github.com/nemar-org/nemar-cli"}]`.

### `sub-001/eeg/sub-001_task-nback_channels.tsv`
- All 64 rows: `type=n/a` → `type=EEG`, `units=n/a` → `units=uV`.

### `sub-001/eeg/sub-001_task-nback_eeg.json`
- Added `MISCChannelCount: 0`.
- Added `TriggerChannelCount: 0`.
- Added `EEGPlacementScheme: "10-10"`.

### `task-nback_events.json`
- Added `sample` column definition.
- `value.HED`: dropped entries for codes `"1"` and `"307"` (105 of 107 entries preserved). Both codes appear in `events.tsv` **only** in collision with another code already tagged `"Task"` — code `"1"` fires 195 times, always paired with `"1103"` (×120) or `"1113"` (×75), never alone; code `"307"` fires once, paired with `"2096"`. Their HED tags were redundant on every appearance and produced the `"Task,Task"` duplicate-tag merges. Dropping the two entries removes the duplicates without affecting any lone event. `value.Levels` is left unchanged (all 107 entries preserved) so the codes remain documented.
