[Қазақша](README.kk.md) · [Русский](README.md) · [English](README.en.md)

# Question banks for Exam Trainer

> **Status: this is an unofficial repository of question banks for Al-Farabi Kazakh National University (KazNU), and it is not finished yet.** The university did not publish or review it, and the questions are not official exam tasks. For now it holds one university, one faculty, one specialty and one semester. The banks are still being filled in and revised, the answer keys have not been checked by a teacher, and the Kazakh and English translations are machine-made. The folder structure and the contents may change.

This is the data repository for the Exam Trainer app: a tree of sections (university, faculty, degree, specialty, year of study, academic year, semester, subject) and banks of test questions in the `exambank/2` format. There is no code here. The app, the format schema and the command-line tool live in the code repository `exam-platform`, which the examples below assume sits next to this folder (`../exam-platform`).

## What is inside

The repository currently holds 7 banks and 2450 questions: Al-Farabi Kazakh National University, Faculty of Information Technology and Artificial Intelligence, bachelor's degree, specialty "Information Security Systems", year 1, 2026, semester 1.

| Subject | Bank identifier | Bank language | Questions |
| --- | --- | --- | --- |
| Psychology | `kaznu/psychology` | ru | 350 |
| Political Science | `kaznu/political-science` | ru | 350 |
| Cultural Studies | `kaznu/cultural-studies` | ru | 350 |
| History of Kazakhstan | `kaznu/history-of-kazakhstan` | ru | 350 |
| Kazakh language, A1 | `kaznu/kazakh-a1` | ru | 350 |
| Kazakh language, A2 | `kaznu/kazakh-a2` | ru | 350 |
| Kazakh language, B1–B2 | `kaznu/kazakh-b1-b2` | kk | 350 |

The three Kazakh banks are gathered into the subject group "Kazakh language", with the levels inside.

The questions are original and written for study. They are not official exam tasks: the wording, the number of options and the spread over four difficulty levels follow the format of the final test, but the keys were written from lectures, textbooks and open sources, and no teacher has checked them. Psychology, Political Science and Cultural Studies have a Kazakh and an English translation; it is machine-made and has not been proofread. The other banks come only in the bank's own language. Typed-answer mode with auto-check is marked up only in part: for 18 questions in Psychology, for roughly every fifth question in History and Kazakh language, and not at all in Political Science and Cultural Studies. In typed mode the remaining questions are self-checked.

## Structure

```
catalog.json                  node kinds of the tree, the "place" mark on the semester
kaznu/                        university (node.json)
  fit-ai/                     faculty
    bachelor/                 degree
      information-security-systems/   specialty
        course-1/             year of study
          2026/               academic year: year 1 can also be for 2025
            semester-1/       semester (node.json with an items list)
              psychology/     bank: bank.json, questions/t01.json …, translations/<locale>/
              political-science/
              cultural-studies/
              history-of-kazakhstan/
              kazakh-language/  subject group (node.json)
                a1/  a2/  b1-b2/   level banks
schema/                       JSON Schema of the format, generated
source/v1/                    source banks in the old format (version 1.0)
```

A folder with `node.json` is a tree node, and a folder with `bank.json` is a bank inside its node. A node identifier is the path of its folder from the root. A bank identifier comes from `bank.json` and does not depend on the path. The display order inside a node is set by the `items` list in its `node.json`. The `schema` and `source` folders do not count as part of the tree. The full description of the format is in `docs/FORMAT.md` in the code repository.

## How to check

Install the dependencies once in the code repository (`npm install`), then run this from this folder:

```bash
node ../exam-platform/packages/content-tools/src/cli.ts validate .
```

On success the command prints `OK: 7 banks, 2450 questions`. On errors it lists them and exits with code 1. It checks the folder layout, schemas, duplicate identifiers, keys and free-input blocks. Run it before every commit.

## How to add

A new bank in an existing semester:

1. Create the bank folder inside the semester folder, for example `kaznu/fit-ai/bachelor/information-security-systems/course-1/2026/semester-1/<subject>/`.
2. Put a `bank.json` in it with a new identifier of the form `kaznu/<subject>` (lowercase Latin letters, digits, hyphen) and the question files in `questions/`. The identifier must be unique across the whole repository and does not change after publication.
3. Add the folder name to `items` in the semester's `node.json`; otherwise the bank ends up after the listed ones.
4. Run `validate`.

A bank in the old format can first be converted with the `migrate` command:

```bash
node ../exam-platform/packages/content-tools/src/cli.ts migrate <v1.json> <bank-id> <bank-dir>
```

A subject group: create a folder with a `node.json` of kind `subject`, put the folders of the level banks in it and list them in `items`.

A new semester, year of study, academic year (for example, `course-1/2025` next to `course-1/2026`) or another level: create a folder with a `node.json` of the right kind (`semester`, `course`, `academic-year` and so on; the kinds are described in `catalog.json`) and put banks or nested nodes inside. Add a new kind to `catalog.json` first.

Translations go in `<bank>/translations/<locale>/` as separate files and are merged with this command:

```bash
node ../exam-platform/packages/content-tools/src/cli.ts merge-translations .
```

## How to build

The `compile` command turns the tree into files with hashes in their names (`index.json`, `nodes/`, `banks/`), which the app reads:

```bash
node ../exam-platform/packages/content-tools/src/cli.ts compile . ../site
```

The destination folder is erased before the build, so it must lie outside this folder and must not contain it.

## How to connect to the app

Three ways, in order of increasing effort:

- Put this repository next to the code repository under the name `exam-content`. The interface build (`npm --prefix apps/ui run build`) picks it up on its own and embeds it in the app as a snapshot.
- Give the folder explicitly: the `EXAM_CONTENT_DIR` variable when building the interface.
- Publish the compiled site, for example on GitHub Pages, and pass its address in the `VITE_CONTENT_URLS` variable when building the app (together with `EXAM_CONTENT_DIR=none`, if you do not need a snapshot inside the app). Then the banks update without rebuilding the app. Step-by-step instructions and workflow templates are in `docs/PUBLISHING.md` of the code repository.

The compiled site of this repository is published on GitHub Pages from the `gh-pages` branch: https://sssox.github.io/exam-content. This address is stored in `content-sources.json` of the code repository. If you publish your own copy, use your own address.
