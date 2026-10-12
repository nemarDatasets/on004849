[![DOI](https://img.shields.io/badge/DOI-10.82901%2Fnemar.on004849-blue)](https://doi.org/10.82901/nemar.on004849)

STRONG dataset

This is a placeholder dataset.

## NEMAR curation changes (2026-05-21, revised 2026-05-27)

The BIDS validator went from 1 error + 47 warnings to 0 errors + 37 warnings. None of the raw `.set`/`.fdt` files were modified, every change is to a text sidecar.

**Channel table (`sub-001/eeg/sub-001_task-nback_channels.tsv`)**
- All 64 electrode rows had `type=n/a` and `units=n/a`, which is not informative and not a valid BIDS channel type. They were set to `type=EEG` and `units=uV`, matching what the `.set` header actually records.

**Recording sidecar (`sub-001/eeg/sub-001_task-nback_eeg.json`)**
- Added `MISCChannelCount: 0` and `TriggerChannelCount: 0` so the channel-count fields are explicit (the recording has 64 EEG channels and no miscellaneous or trigger channels). BIDS recommends these be present.
- Added `EEGPlacementScheme: "10-10"`, the standard montage the 64 electrode labels in `channels.tsv` follow.

**Task events dictionary (`task-nback_events.json`)**
- Added a `sample` column definition so the sample-index column in `events.tsv` is documented (it was present in the data but missing from the dictionary).
- Dropped the `value.HED` entries for codes `"1"` and `"307"` so the validator stops reporting duplicate HED tags. The original HED dictionary tagged roughly 80 of the 107 event codes with `"Task"`, and `events.tsv` has 197 onsets where two rows share the same `onset`. The BIDS-HED validator merges HED annotations for rows that share an onset, so any pair of co-occurring codes both tagged `"Task"` produced the merged string `"Task,Task"` and a legitimate duplicate-tag error. 196 of the 197 collisions hit this pattern. Codes `"1"` and `"307"` are the ones that only ever appear in such collisions, never alone: code `"1"` fires 195 times, always paired with `"1103"` (120 times) or `"1113"` (75 times); code `"307"` fires once, paired with `"2096"`. Their HED tags were redundant on every appearance, so removing them eliminates the duplicates without losing information for any lone event. The remaining 105 of 107 HED entries are preserved, and `value.Levels` is left unchanged (all 107 codes still documented there).

**Dataset description (`dataset_description.json`)**
- Updated `BIDSVersion` from `1.8.0` to `1.11.1` (the version the current validator checks against).
- Added `DatasetType: "raw"` so the dataset is validated as raw data rather than a derivative.
- `GeneratedBy` was left absent, exactly as the source published it, nothing was added there.

**Remaining warnings (37), left on purpose**
- These are all "recommended but missing" fields that need information from the study, lab, or equipment that isn't in the dataset (for example: manufacturer, model name, software version, serial number, institution name and address, cap manufacturer and model, ground-electrode location, head circumference, hardware filters, subject-artefact description, task description, task instructions, cognitive-atlas and CogPO IDs, stimulus-presentation software). `GeneratedBy` is also among them, recommended at the dataset level but absent in the source. They were left blank rather than filled with guesses.
