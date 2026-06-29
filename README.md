# ClarVis

ClarVis is a task-oriented dialogue dataset for studying clarification requests (CRs) in collaborative data visualization dialogues. The dataset contains real-time, two-user collaborative data-exploration sessions in which participants interacted with a visualization-generating conversational assistant while working with COVID-19-related data.

Each utterance is annotated for whether it is a clarification request. Utterances labeled as CRs are additionally annotated with the grounding modality that captures the context the clarification pertains to:

- `A`: Auditory, for clarification grounded in hearing, wording, or speech understanding.
- `V`: Visual, for clarification grounded in what is visible in the shared visualization workspace.
- `K`: Kinesthetic, for clarification grounded in actions, interface operations, system capabilities, or interaction flow.

## Data Source and Annotation

The dialogue data used in ClarVis comes from a prior user study on conversational data exploration and visualization:

> Bhattacharya, A., Di Eugenio, B., Grosso, V., Johnson, A., Tabalba, R., Kirshenbaum, N., Leigh, J., and Zellner, M. (2024). [A Conversational Assistant for Democratization of Data Visualization: A Comparative Study of Two Approaches of Interaction](https://dl.acm.org/doi/10.1002/sam.11714). *Statistical Analysis and Data Mining*, 17(6).

The source study is licensed under CC BY-NC 4.0. In that study, pairs of participants performed open-ended exploratory data analysis tasks over a COVID-19 dataset using a conversational assistant that generated visualizations from natural-language utterances. The system retrieved data from a structured database and produced maps, heat maps, bar charts, and line charts on a shared display.

For ClarVis, the dialogue transcripts were collected, polished, de-identified, speaker-diarized, and annotated for CR identification and grounding modality. The released files in this repository contain the resulting master dataset and the train/validation/test splits used in the ClarVis experiments.

## Files

- `cr_dataset_master.csv`: Full master dataset with all turns, dialogue metadata, context columns, system-event indicators, CR labels, and grounding-modality labels.
- `train.csv`: Training split used in the ClarVis experiments.
- `validation.csv`: Validation split used in the ClarVis experiments.
- `test.csv`: Test split used in the ClarVis experiments.
- `LICENSE`: License for this repository.

## Dataset Size

The master file contains 6,927 total turns across 29 dialogues. Of these, 445 utterances are annotated as clarification requests.

Grounding modality distribution for CRs in `cr_dataset_master.csv`:

| Modality | Meaning | Count |
| --- | --- | ---: |
| `A` | Auditory | 125 |
| `V` | Visual | 220 |
| `K` | Kinesthetic | 100 |

## Master Dataset Columns

`cr_dataset_master.csv` contains the following columns:

| Column | Description |
| --- | --- |
| `utterance_id` | Turn identifier within the source dialogue file for a given group-task dialogue. |
| `utterance` | Dialogue turn text. For system rows, this is the generated visualization caption. |
| `speaker` | Speaker identifier: `U1` and `U2` are the two users; `S` is the conversational assistant/system. |
| `utterance_intent` | Utterance-level label. `CR` marks clarification requests. Other labels come from the original dialogue/visualization annotation pipeline, including visualization requests, system visualization outputs, and non-CR dialogue turns. |
| `cr_directed_at` | For CRs, the target of the clarification request, such as `User`, `System`, or `System/User`; `na` for non-CR rows. |
| `source_of_response` | Annotation of the response source for CR-related turns where available; `na` when not applicable. |
| `grounding_modality` | Grounding label for CRs: `A`, `V`, or `K`; `na` for non-CR rows. |
| `group_id` | Participant group identifier from the user study described in Bhattacharya et al. (2024). |
| `task_id` | Task identifier from the user study; each group completed Task 1 and/or Task 2. |
| `__source` | Original source spreadsheet name. |
| `turn_index` | Zero-based turn index within the dialogue. |
| `dialogue_id` | Dialogue identifier in the form `<group_id>_<task_id>`; for example, `10_2` refers to Group 10, Task 2. |
| `master_utterance_id` | Unique utterance identifier in the master dataset in the form `<group_id>_<task_id>_U<turn_number>`; for example, `10_2_U5` refers to the fifth turn from Group 10, Task 2. |
| `prev_speaker` | Speaker of the immediately preceding turn, when available. |
| `prev_turn` | Text of the immediately preceding turn, used for +Prev1 context experiments. |
| `prev2_turn` | Text of the turn two positions before the current turn, used for +Prev2 context experiments. |
| `system_event` | `1` if the row is a system-generated visualization event; otherwise `0`. |
| `sys_to_user_transition` | `1` if the row follows a system-to-user transition; otherwise `0`. |

## Split Files

The split files contain the columns used for the text-classification experiments in the ClarVis paper:

| Column | Description |
| --- | --- |
| `master_utterance_id` | Unique utterance identifier from the master dataset in the form `<group_id>_<task_id>_U<turn_number>`. |
| `dialogue_id` | Dialogue identifier in the form `<group_id>_<task_id>`. |
| `speaker` | Speaker identifier. |
| `utterance` | Dialogue turn text. |
| `utterance_intent` | Label used for CR identification. `CR` indicates a clarification request; other labels are treated as non-CR/Rest for the CR-ID task. |
| `grounding_modality` | `A`, `V`, or `K` for CRs; `na` otherwise. |

The train/validation/test split is dialogue-level: all turns from a dialogue appear in exactly one split. This avoids leakage of conversational context, participant style, or task-specific dialogue history across splits. These are the fixed splits used in the ClarVis experiments.

| Split | Rows | Dialogues | CRs | Dialogue IDs |
| --- | ---: | ---: | ---: | --- |
| `train.csv` | 4,741 | 20 | 307 | `1_1`, `1_2`, `2_1`, `2_2`, `3_1`, `4_1`, `4_2`, `5_1`, `5_2`, `6_1`, `7_2`, `8_1`, `10_2`, `12_1`, `12_2`, `13_1`, `13_2`, `14_2`, `15_1`, `15_2` |
| `validation.csv` | 830 | 3 | 60 | `6_2`, `9_2`, `11_1` |
| `test.csv` | 1,356 | 6 | 78 | `3_2`, `7_1`, `8_2`, `9_1`, `11_2`, `14_1` |

## Tasks

The ClarVis paper evaluates two classification tasks:

- **Clarification Request Identification (CR-ID):** binary classification of each utterance as `CR` or `Rest`.
- **Grounding Modality Classification (GMOD):** three-way classification of CRs as `A`, `V`, or `K`.

The experiments use three context settings:

- **No Context:** the current utterance only.
- **+Prev1:** the current utterance plus `prev_turn`.
- **+Prev2:** the current utterance plus `prev2_turn` and `prev_turn`.

## Citation

The paper introducing ClarVis, *ClarVis: A Dataset of Clarification Requests and Grounding in Collaborative Data Visualization Dialogues*, has been accepted at SIGDIAL 2026.

The BibTeX citation will be added once the proceedings entry is available.

## License

<a rel="license" href="http://creativecommons.org/licenses/by/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by/4.0/">Creative Commons Attribution 4.0 International License</a>.
