[Қазақша](README.kk.md) · [Русский](README.md) · [English](README.en.md)

# Question banks for Exam Trainer

> **Status: this is an unofficial repository of question banks for Al-Farabi Kazakh National University (KazNU), and it is not finished yet.** The university did not publish or review it, and the questions are not official exam tasks. For now it holds one university, one faculty, one specialty and one semester. The banks are still being filled in and revised, no teacher has checked the answer keys, and the Kazakh and English translations are machine-made. The folder structure and the contents may change.

This is the data repository for the Exam Trainer app: a tree of sections (university, faculty, degree, specialty, year of study, academic year, semester, subject) and banks of test questions in the `exambank/2` format. There is no code here. The app, the format schema and the command-line tool live in the code repository `exam-platform`. The examples below assume it sits next to this folder (`../exam-platform`).

## What is inside

The repository currently holds 10 banks and 4336 questions: Al-Farabi Kazakh National University, Faculty of Information Technology and Artificial Intelligence, bachelor's degree, specialty "Information Security Systems", year 1, 2026, semester 1.

| Subject | Bank identifier | Bank language | Questions |
| --- | --- | --- | --- |
| Psychology | `kaznu/psychology` | ru | 450 |
| Political Science | `kaznu/political-science` | ru | 450 |
| Cultural Studies | `kaznu/cultural-studies` | ru | 450 |
| Sociology | `kaznu/sociology` | ru | 286 |
| History of Kazakhstan | `kaznu/history-of-kazakhstan` | ru | 450 |
| Kazakh language, A1 | `kaznu/kazakh-a1` | ru | 450 |
| Kazakh language, A2 | `kaznu/kazakh-a2` | ru | 450 |
| Kazakh language, B1–B2 | `kaznu/kazakh-b1-b2` | kk | 450 |
| Introduction to information security | `kaznu/introduction-to-information-security` | ru | 450 |
| English, Pre-Intermediate | `kaznu/english-pre-intermediate` | en | 450 |

The three Kazakh banks sit in the subject group "Kazakh language", one bank per level. The Sociology bank is smaller than the others: it holds 286 questions: 186 from a known question list (options and keys written from the lectures, or from textbooks where the lectures say nothing) and 100 new original ones.

The questions are original and written for study, and they are not official exam tasks. The practice format and difficulty levels are described in each bank’s metadata. For Sociology the wording is taken from the known list, and the levels follow how hard each question is, so that bank does not fully match the test format. The keys come from lectures, textbooks and open sources, and no teacher has checked them. Psychology, Political Science, Cultural Studies and Sociology have a Kazakh and an English translation. The translation is machine-made and has not been proofread. The other banks come only in the bank's own language.
Each of the eight original banks has 100 new questions in a separate file, `questions/extra-20261124.json` (IDs 351–450, 187–286 for Sociology). They are original tasks with one correct answer out of five, with author-estimated difficulty, in the bank's own language. They have no translations and no typed-answer markup yet. Auto-check in typed-answer mode covers only some questions: 18 in Psychology, roughly every fifth in History and Kazakh language, none in Political Science, Cultural Studies and Sociology. In typed-answer mode the student checks the remaining questions themselves.

Two new banks, Introduction to information security and English Pre-Intermediate, contain 450 original questions each, with one correct answer out of five. Information security covers the 15 topics of the 2026/2027 oral offline exam programme; these tests are for practice. English follows the 15-week 2026 programme; the official test has 40 questions and a 90-minute limit. Questions are divided into `t01.json`–`t15.json`, with author-estimated difficulty. English is under the Foreign language group, `pre-intermediate` level. The new banks have no translations or automatic typed-answer markup.

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
              sociology/
              history-of-kazakhstan/
              kazakh-language/  subject group (node.json)
                a1/  a2/  b1-b2/   level banks
schema/                       JSON Schema of the format, generated
source/v1/                    source banks in the old format (version 1.0)
```

A folder with `node.json` is a tree node, and a folder with `bank.json` is a bank inside its node. A node identifier is the path of its folder from the root. A bank identifier comes from `bank.json` and does not depend on the path. The `items` list in a node's `node.json` sets the display order inside that node. The `schema` and `source` folders are not part of the tree. The full description of the format is in `docs/FORMAT.md` in the code repository.

## How to check

Install the dependencies once in the code repository (`npm install`), then run this from this folder:

```bash
node ../exam-platform/packages/content-tools/src/cli.ts validate .
```

On success the command prints `OK: 10 banks, 4336 questions`. On errors it lists them and exits with code 1. It checks the folder layout, schemas, duplicate identifiers, keys and free-input blocks. Run it before every commit.

## How to add

A new bank in an existing semester:

1. Create the bank folder inside the semester folder, for example `kaznu/fit-ai/bachelor/information-security-systems/course-1/2026/semester-1/<subject>/`.
2. Put a `bank.json` in it with a new identifier of the form `kaznu/<subject>` (lowercase Latin letters, digits, hyphen) and the question files in `questions/`. The identifier must be unique across the whole repository and does not change after publication.
3. Add the folder name to `items` in the semester's `node.json`. Otherwise the bank ends up after the listed ones.
4. Run `validate`.

You can first convert a bank in the old format with the `migrate` command:

```bash
node ../exam-platform/packages/content-tools/src/cli.ts migrate <v1.json> <bank-id> <bank-dir>
```

A subject group: create a folder with a `node.json` of kind `subject`, put the folders of the level banks in it and list them in `items`.

A new semester, year of study, academic year (for example, `course-1/2025` next to `course-1/2026`) or another level: create a folder with a `node.json` of the right kind (`semester`, `course`, `academic-year` and so on; the kinds are described in `catalog.json`) and put banks or nested nodes inside. Add a new kind to `catalog.json` first.

Put translations in `<bank>/translations/<locale>/` as separate files, then merge them with this command:

```bash
node ../exam-platform/packages/content-tools/src/cli.ts merge-translations .
```

## How to build

The `compile` command turns the tree into files with hashes in their names (`index.json`, `nodes/`, `banks/`), which the app reads:

```bash
node ../exam-platform/packages/content-tools/src/cli.ts compile . ../site
```

Before building, the command erases the destination folder, so that folder must lie outside this folder and must not contain it.

## How to connect to the app

Three ways, in order of increasing effort:

- Put this repository next to the code repository under the name `exam-content`. The interface build (`npm --prefix apps/ui run build`) picks it up on its own and embeds it in the app as a snapshot.
- Point to the folder explicitly with the `EXAM_CONTENT_DIR` variable when building the interface.
- Publish the compiled site, for example on GitHub Pages, and pass its address in the `VITE_CONTENT_URLS` variable when building the app (together with `EXAM_CONTENT_DIR=none`, if you do not need a snapshot inside the app). Then the banks update without rebuilding the app. Step-by-step instructions and workflow templates are in `docs/PUBLISHING.md` of the code repository.

The compiled site of this repository is published on GitHub Pages from the `gh-pages` branch: https://sssox.github.io/exam-content. This address is stored in `content-sources.json` of the code repository. If you publish your own copy, use your own address.

## License

The question banks and the section tree are released under the [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/) license. You can copy, change and use them, including commercially, as long as you credit the author and the source (a link to this repository) and mark your own changes. The full text is in the [LICENSE](LICENSE) file. The license covers only the contents of this repository. The code of the Exam Platform app lives in a different repository and is not covered by it.
