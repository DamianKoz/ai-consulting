# SkillPath AI

A single-page German-language web app that generates a personalised AI training path for corporate employees. No backend, no build step — open `index.html` in any browser.

## Tech Stack

| Layer | Library |
|---|---|
| UI framework | [Alpine.js](https://alpinejs.dev/) 3.14 (CDN) |
| Styling | [Tailwind CSS](https://tailwindcss.com/) (CDN) |
| Runtime | Vanilla JS, no build tool |

---

## Screens

### 1. Landing (`screen = 'landing'`)

The entry point. Presents the value proposition with three benefit cards and a single CTA button that launches the configuration wizard.

### 2. Wizard (`screen = 'wizard'`)

A three-step form that collects the user profile needed to generate a training plan.

| Step | Field(s) | Validation |
|---|---|---|
| 1 — Company | Industry (`branche`) dropdown + Role (`rolle`) card selector | Both required to proceed |
| 2 — AI Level | Knowledge level selector: Einsteiger / Grundkenntnisse / Fortgeschritten | Always valid (defaults to Einsteiger) |
| 3 — Goals | Multi-select goal chips (`ziele`) + optional free-text (`freitext`) | At least one goal required |

Navigation: **Weiter →** advances the step; **← Zurück** goes back; from step 1 "back" returns to the landing screen.

### 3. Results (`screen = 'results'`)

Displays the generated learning path as a numbered vertical timeline. Each card shows module name, difficulty badge, duration, format, and scheduled week. The header summarises the total module count and total hours.

A "Why this plan?" block (`begründung`) explains the selection in plain language and echoes the user's optional free-text note.

CTAs: **← Neuen Plan erstellen** resets the wizard; **Schulung anfragen →** triggers a placeholder alert (full-version hook).

A **🖨️ Drucken** button triggers `window.print()`; navigation and CTA elements are hidden in print view via `.no-print`.

### 4. Module Detail Modal

Clicking any timeline card opens a modal overlay with:
- Module icon, name, difficulty, duration, and format
- Full description
- Bulleted learning objectives (`lernziele`)

Closes via the × button, **Schließen**, or clicking the backdrop.

---

## Module Catalogue

Seven predefined training modules, each tagged with target roles and learning goals:

| ID | Name | Level | Duration | Format |
|---|---|---|---|---|
| `ai-basics` | KI-Grundlagen | Einsteiger | 2 h | Online-Kurs + Quiz |
| `prompt-engineering` | Prompt Engineering | Grundkenntnisse | 3 h | Workshop (praktisch) |
| `use-cases` | KI-Use Cases nach Branche | Einsteiger | 2 h | Online-Kurs + Fallstudien |
| `datenschutz` | Datenschutz & KI-Ethik | Grundkenntnisse | 2 h | Online-Kurs |
| `tools` | KI-Tools im Arbeitsalltag | Einsteiger | 1.5 h | Live-Demo + Übungen |
| `strategie` | KI-Strategie & ROI | Fortgeschritten | 3 h | Workshop + Beratung |
| `workflows` | KI-Workflows & Automatisierung | Fortgeschritten | 4 h | Technischer Workshop |

---

## Learning Path Generation Algorithm

`generatePfad()` scores every module against the user's inputs and picks the top 5:

```
score per module
  +3  if module's target roles include the selected role
  +3  if module level exactly matches selected level
  +1  if module level is one step away from selected level
  +2  per matched learning goal (additive)
```

The top-5 modules by score are then re-sorted by `levelIdx` (0→2) so the final path follows a logical easy-to-advanced progression.

---

## Running Locally

```bash
open index.html        # macOS
xdg-open index.html    # Linux
start index.html       # Windows
```

No install, no server required. Both CDN libraries load from jsDelivr / the Tailwind CDN.

---

## Project Structure

```
ai-consulting/
└── index.html   # entire app — HTML, CSS, and JS in one file
```

All application state and logic lives in the `skillpath()` Alpine component defined at the bottom of the file.
