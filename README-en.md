# Study Schedule Tool

*Leia isto em outros idiomas: [Português](README.md)*

---

Static educational web application for **creating, editing, adapting, and tracking study schedules** directly in the browser.

The tool was designed to be reusable: users can work with different courses, exams, subjects, or study routines through schedules structured in JSON. MedCof's **“Semiextensivo TEP 2026 - Turma de Abril”** schedule is embedded only as a demonstration dataset and as a practical example of how the application can be used.

The project is self-contained in a single `index.html` file: the interface, styles, JavaScript logic, schedule editor, and default schedule are all embedded in the HTML itself. The `README.md` file exists only for repository documentation.

> **Independent and unofficial project.** There is no relationship, affiliation, sponsorship, or endorsement by MedCof or by other institutions whose schedules may be used with the tool.

---

## Purpose

The application was developed to turn a study schedule into an interactive planning and tracking tool. Its main goals include:

- allowing the creation and editing of structured schedules;
- tracking completed activities;
- adapting the plan to the available time;
- reorganizing priorities without deleting the original schedule;
- removing activities individually or by priority class;
- creating individual exceptions for activities that should remain in the plan;
- calculating the overall progress percentage;
- importing and exporting schedules and progress in JSON;
- keeping data locally without depending on a backend.

---

## Architecture

The web app was deliberately built to keep the entire application in a single executable file:

```text
/
├── index.html
└── README.md
```

The `index.html` file contains:

- the HTML structure of the interface;
- CSS styles for light and dark modes;
- the application's JavaScript;
- the default schedule in JSON;
- the schedule editor and validator;
- priority management;
- data import and export;
- the “Sobre o projeto” view.

The `README.md` documents the project on GitHub and is not required to run the web app.

---

## Features

### Block-based schedule

Activities are organized into expandable blocks. Each item can display:

- title;
- specialty or category;
- release date;
- priority;
- release status;
- completion status;
- inclusion status in the active plan.

### Dark mode by default

The application starts in **dark mode**. The user can switch to light mode using the corresponding button.

The preference is stored locally in the browser.

### Adjustable start date

The user can change the schedule's start date.

When this date is changed, the other release dates are shifted proportionally while preserving the relative intervals of the original schedule.

### Completed activities

Each activity has a checkbox that can be used to mark it as completed/viewed.

This automatically updates:

- number of completed activities;
- number of activities removed from the plan;
- number of pending activities;
- progress percentage;
- visual progress bar.

### Individual removal — ✂️

The **✂️** button allows an individual activity to be removed from the plan.

The activity remains visible so that the user still has a reference to the complete schedule. The action can be reversed at any time.

### Priority classes

In the internal **Gerenciar cronograma** area, the user can choose which priority classes belong to the active plan.

The current format recognizes:

| Priority | Default interpretation |
|---|---|
| Diamante | Watch first |
| Verde | Watch after Diamante items |
| Amarela | Watch after Verde items |
| Vermelha | Watch after Amarela items |
| Bônus | Additional content |

Unchecking a class **does not delete or hide its items**. Activities remain in the main schedule and receive the label:

**⏸ Fora do plano**

Even outside the plan, an activity can still be marked as completed if the user decides to do it.

### Individual exception — 📌

The **📌** button allows a specific activity to remain in the plan even when the entire priority class to which it belongs has been unchecked.

In this situation, the activity receives the label:

**📌 Mantida no plano**

This mechanism allows fine-grained adjustments without having to reactivate an entire class.

### Individual priority change

The priority of each activity can be changed directly on the main screen.

Changes are taken into account by the filters and saved in local progress.

### Progress calculation

The dashboard displays:

- Total;
- Completed/Viewed;
- Removed;
- Pending;
- Progress (%).

The system uses the concept of a **processed activity**. An activity is considered processed when:

1. it has been marked as completed/viewed;
2. it has been individually removed with ✂️; or
3. it belongs to a class temporarily outside the plan and has not been kept as an exception with 📌.

The formula is:

```text
Progress (%) = processed activities / total activities × 100
```

If an activity outside the plan is later marked as completed, it is counted among completed activities rather than removed activities.

---

## Managing the schedule

The application includes a second internal view called **Gerenciar cronograma**. It is not a separate HTML file: it remains inside the same `index.html`.

This area allows users to:

- enable and disable priority classes;
- export the current schedule as JSON;
- import a JSON schedule;
- load the schedule into the internal editor;
- validate a schedule before applying it;
- apply a schedule created by the user;
- restore the embedded default schedule.

---

## Creating your own schedule

A valid schedule can use the following structure:

```json
{
  "versao": 1,
  "cronograma_id": "meu-cronograma-2026",
  "nome": "Meu cronograma de estudos",
  "data_inicio": "2026-04-06",
  "aulas": [
    {
      "id": 1,
      "bloco": "Bloco 1",
      "data_liberacao": "06/04/2026",
      "aula": "Tema ou atividade 1",
      "especialidade": "Área A",
      "prioridade": "Diamante"
    },
    {
      "id": 2,
      "bloco": "Bloco 1",
      "data_liberacao": "06/04/2026",
      "aula": "Tema ou atividade 2",
      "especialidade": "Área B",
      "prioridade": "Verde"
    }
  ]
}
```

### Main object fields

| Field | Required | Description |
|---|---|---|
| `versao` | No | Informational format version |
| `cronograma_id` | Yes | Stable schedule identifier |
| `nome` | Yes | Schedule name |
| `data_inicio` | Yes | Start date in `YYYY-MM-DD` |
| `aulas` | Yes | Non-empty list of activities |

### Fields for each activity

| Field | Required | Description |
|---|---|---|
| `id` | Yes | Unique activity identifier |
| `bloco` | Yes | Group displayed in the interface |
| `data_liberacao` | Yes | Date in `DD/MM/YYYY` |
| `aula` | Yes | Activity title |
| `especialidade` | Yes | Category, area, or specialty |
| `prioridade` | Yes | Recognized priority class |

Priorities accepted by the current version:

```text
Diamante
Verde
Amarela
Vermelha
Bônus
```

### Practical rules for editing JSON

- use double quotes;
- do not include comments inside JSON;
- do not leave a comma after the last item;
- use a unique `id` for each activity;
- use a different `cronograma_id` for different schedules;
- preserve existing IDs when you want to keep their association with previously saved progress.

A safe way to begin is to export the current schedule, make a copy, and use it as a template.

---

## Schedule validation

Before applying an imported or edited schedule, the system checks, among other points:

- whether the top-level content is a JSON object;
- presence and type of `cronograma_id`;
- presence and type of `nome`;
- `data_inicio` format;
- presence of a non-empty activity list;
- presence of IDs;
- duplicate IDs;
- completion of required text fields;
- release-date format;
- use of recognized priorities.

A file that does not satisfy these checks is not applied by the tool.

This verification is structural and does not represent academic or scientific validation of the content entered by the user.

---

## Progress separation by schedule

The `cronograma_id` field is used to separate the state of different schedules.

Each schedule can independently maintain:

- completed activities;
- removed activities;
- changed priorities;
- 📌 exceptions;
- priority classes outside the plan;
- selected start date.

This reduces the risk of applying the progress from one schedule to a different set of activities.

---

## Local persistence

The application uses `localStorage` to automatically save the user's state in the browser.

The following can be stored locally:

- completed activities;
- activities removed with ✂️;
- priority changes;
- individual 📌 exceptions;
- priority classes outside the plan;
- start date;
- imported custom schedule;
- theme preference.

The project has no backend of its own and does not automatically synchronize this data between devices.

---

## Importing and exporting progress

The main screen provides:

- **💾 Exportar progresso**
- **📂 Importar progresso**

Exporting generates a JSON file containing the user's state for the current schedule.

When importing, the application checks the `cronograma_id` to avoid applying a progress file that belongs to another schedule.

This feature also allows state to be manually transferred between computers or browsers.

---

## Embedded example: MedCof Semiextensivo TEP 2026

To demonstrate the tool in a real-world situation, `index.html` includes MedCof's **Semiextensivo TEP 2026 - Turma de Abril** as the default schedule.

The information for this schedule was obtained from the company's own website:

[https://cronograma.grupomedcof.com.br/](https://cronograma.grupomedcof.com.br/)

The use of this dataset in the project is **solely educational and illustrative**, serving as a basis for demonstrating how a web app can be used to organize study schedules.

This inclusion may also be useful to students preparing for the TEP specifically through this course. However, **the tool does not depend on this schedule** and can receive schedules from other courses, exams, institutions, or personal study routines through JSON import or editing.

The currently embedded default schedule contains 121 activities distributed across 18 groups.

This project is independent and unofficial. There is no relationship, affiliation, sponsorship, or endorsement by MedCof. Third-party trademarks, names, and content remain the responsibility of their respective owners.

---

## About the project

The internal **Sobre o projeto** view presents:

- authorship;
- purpose of the tool;
- transparency regarding the use of generative artificial intelligence;
- Lattes CV;
- origin of the schedule used as an example;
- notice of the project's independence.

It remains integrated into the same `index.html`.

---

## How to run

There are no dependencies to install.

Simply open:

```text
index.html
```

in a modern browser.

The project can also be run through a static HTTP server, such as the **Live Server** extension for Visual Studio Code, or published using static-file hosting services.

---

## Publishing on GitHub Pages

Because the application is static and its main file is named `index.html`, the repository can be published with GitHub Pages directly from the main branch.

On GitHub:

1. open the repository's **Settings**;
2. go to **Pages**;
3. select deployment from a branch;
4. choose the main branch and the root folder;
5. save the configuration.

The README is not required to run the page, but GitHub will display it on the repository's main page when it is stored at the root. GitHub documentation confirms that root-level READMEs are recognized and displayed automatically.

---

## Privacy

The application's main functionality runs locally in the browser.

The project does not implement:

- user accounts;
- authentication;
- its own backend;
- a remote database;
- automatic cloud synchronization.

Data portability is handled manually through JSON import and export features.

---

## Technologies used

- **JavaScript** — application logic, persistence, import/export, validation, and progress calculation;
- **HTML5** — structure and interface;
- **CSS3** — presentation, responsiveness, and themes;
- **JSON** — representation of schedules and interchange files.

The project does not depend on external JavaScript frameworks or libraries for its basic operation.

---

## Compatibility

The project uses native APIs available in modern browsers, including:

- `localStorage`;
- `FileReader`;
- `Blob`;
- `URL.createObjectURL`;
- DOM manipulation.

Current versions of browsers such as Chrome, Edge, Firefox, or equivalents are recommended.

---

## Known limitations

- Progress is local and is not automatically synchronized between devices.
- Changing an activity's `id` may break its association with previously stored progress.
- Changing the `cronograma_id` intentionally creates a new progress space.
- The tool validates JSON structure, but not the academic correctness of the content entered.
- Third-party schedules may be updated at their original source without the embedded example being updated automatically.
- The project does not replace official schedules, educational guidance, or information published by the institutions responsible for the courses used by the user.

---

## 👤 Authorship and development

Educational web application independently developed by **Pablo Phillipe Cândido dos Santos**, intended for creating, editing, adapting, and tracking study schedules. The tool allows users to organize activities by blocks and priorities, track progress, and import or export schedules structured in JSON.

Generative artificial intelligence tools were used as auxiliary resources during development, while responsibility for the project's conception, implementation, integration, and verification remained with the author.

Lattes CV: [http://lattes.cnpq.br/9500873674712528](http://lattes.cnpq.br/9500873674712528)
