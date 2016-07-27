# book-manifests

This repository contains the book manifests, including the book class maps,
to be used for new instances of Tutor, or to add a new book to an existing instance.

# Documentation

## Splitting content

Most of these options (except for `[]` and `node`) will generate a new step in a reading assignment. The process for creating a new step is that when the element is found, all the open HTML elements are immediately closed (for the previous step) and then all the HTML elements are reopened (for the next step).

## YAML format

Example:

```yaml
- archive_url: https://archive-staging-tutor.cnx.org
  cnx_id: 334f8b61-30eb-4475-8e05-5260a4866b4b
  reading_processing_instructions:
  - css: .ost-reading-discard, .os-teacher, [data-type="glossary"]
    except: snap-lab
    fragments:
    - node
    - optional_exercise
```

### CSS

This contains a comma-separated set of selectors that match an HTML element. There is a special extension `:has-descendants(".os-exercise",2)` which is used to test if an element contains a certain type of descendant.

### Fragments

This defines what will happen when an element is matched. It contains an array of enumerated values. The possible values are described below. Most of them result in splitting the HTML content and injecting an additional step (an `exercise`, `interactive`, or `video`). `[]` and `node` do not add an additional step and instead describe where the selected element should go. See `node` for more details.

- `[]`: Remove this element but do not create a new step
- `reading step`: Create a new step which contains this element
- `exercise`: Create a new step which contains an exercise. There are 2 ways the exercise is chosen
  - if the target is a link, then parse the URL to find an exid and search for the exid in exercises
  - OR, the exercise is chosen by querying exercises for a cnxmod tag that matches the page UUID as well as the HTML fragment id that the selector matches (usually a `<cnx:note>` element)
- `optional_exercise`: Adds an optional "Try Another" exercise. The way the exercise is looked up is the same as for `exercise`. This should probably always come after an `exercise`
- `interactive`: The CSS should target an `<iframe>`. This replaces the `<iframe>` with a new step that contains the iframe and has a little interactive icon
- `video`: The CSS should target an `<iframe>`. This replaces the `<iframe>` with a new step that contains the iframe and has a little video icon
- `node`: The option with the most corner-cases. This describes whether the selected element should go with the HTML that came before the element or the HTML after. It must be either the 1st element in the array or the last.
  - **Example**: `[node, exercise]` will create a new exercise step but keep the selected element with the step just before the exercise
