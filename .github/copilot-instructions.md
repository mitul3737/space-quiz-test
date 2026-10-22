# Beyond Earth space quiz

## Running and checking the app

Open `index.html` directly in a browser using a `file://` URL. The app must remain a single, offline-capable HTML file with inline CSS and JavaScript, no server, dependencies, or external assets.

There are no build, lint, or automated test commands. Validate changes in the browser:

- For a focused correct-answer check, reload, select Neil Armstrong on question 1, and verify score 1, progress 1/10, green feedback, and disabled answer buttons.
- For a focused incorrect-answer check, reload, select Buzz Aldrin, and verify score 0, progress 1/10, red shake feedback, the highlighted correct answer, and the explanation.
- For a full smoke test, finish all 10 questions, check the final score and emoji, then restart and verify that score, progress, feedback, and question position reset. Results tiers are 0-4, 5-7, and 8-10.
- Check real Tab/Shift+Tab navigation and Enter/Space activation, both color schemes, reduced motion, and a 320px viewport. Programmatic `.focus()` or `.click()` checks do not establish that actual keyboard input works.

## Architecture and state

`index.html` contains the entire app: theme and layout styles, static quiz/results containers, and a bottom-of-body classic script. The script builds answer buttons dynamically; there is no routing, storage, or network access.

The `questions` array has 10 ordered entries with `category`, `question`, four `answers`, a zero-based `correct` index, and an explanatory `fact`. Question totals also appear as literal text and attributes in the markup, results, and reset handler; changes to the question count must keep those surfaces consistent.

State consists of `current` (zero-based question index), `score`, and `answered`. `renderQuestion()` resets the answer lock and rebuilds the buttons. `chooseAnswer()` locks the question, updates score and answered-question progress, reveals feedback, and enables Next. The Next handler advances or calls `showResults()`; the restart handler resets state and calls `renderQuestion(true)`.

## UI and accessibility conventions

- Use the existing `get(id)` helper and DOM construction with `textContent`/`replaceChildren()` for question and feedback content. Existing fixed markup in the question counter uses `innerHTML`.
- Use `.correct`/`.wrong` on answer buttons and `.good`/`.bad` on feedback. Correct answers animate with `celebrate`; wrong selections use `shake`. Always reveal the correct answer after a selection and prevent repeat scoring.
- Progress counts answered questions, not correct answers. Keep Next disabled until an answer is chosen.
- Preserve native buttons, `:focus-visible` outlines, live score/feedback announcements, and textual/symbolic feedback in addition to color. Answer selection focuses Next; advancing/restarting focuses the question heading; results focus the results heading. Headings use `tabindex="-1"` for programmatic focus.
- Toggle quiz/results and feedback using `hidden`; the `[hidden]` CSS rule must override layout display rules. Decorative letters/readouts are `aria-hidden`; results emoji use `role="img"` and a descriptive `aria-label`.
- Define theme colors as semantic custom properties in the light `:root` and dark `prefers-color-scheme` override. The mission-control heading uses `--console-*` tokens. Keep the centered column capped at 520px and body text at 15px with system sans-serif fonts.
- Preserve the `prefers-reduced-motion` override when modifying animation or transitions. Icons are inline SVG/HTML entities, and script symbols/emoji use Unicode escapes.
