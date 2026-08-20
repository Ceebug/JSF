# JSF

**JSF** is a JavaScript parser for the [**Ceedoku Save Format (CSF)**.](https://ceedoku.github.io/csfspec)

It takes a `.csf` file and converts it into the JSON string represented by that file.

JSF is designed to be usable by **any JavaScript project that implements the CSF specification**.

---

## Installation

Include `jsf.js` in your webpage:

```html
<script src="jsf.js"></script>
```

JSF has no external dependencies and runs entirely in the browser.

---

## Basic Usage

Pass JSF a `File` or `Blob` containing a CSF file:

```js
const jsonString = await JSF(file);
```

For example:

```js
const file = document.querySelector("#saveFile").files[0];

const jsonString = await JSF(file);

console.log(jsonString);
```

The returned value is a **JSON string**.

It can then be parsed normally:

```js
const save = JSON.parse(jsonString);
```

---

## File Input Example

```html
<input type="file" id="saveFile" accept=".csf">
```

```js
document
    .getElementById("saveFile")
    .addEventListener("change", async event => {

        const file = event.target.files[0];

        if (!file) return;

        const jsonString = await JSF(file);

        console.log(jsonString);

    });
```

---

## How It Works

```text
.csf file
    │
    ▼
   JSF
    │
    ├── Read CSF header
    ├── Read CRC-32C
    ├── Validate checksum
    ├── Parse binary fields
    ├── Decode values
    ├── Decode undo/redo JSON
    └── Reconstruct save object
            │
            ▼
       JSON string
```

JSF does **not** modify the returned JSON string.

---

## Return Value

JSF returns a Promise containing a JSON string:

```js
const jsonString = await JSF(file);
```

For example:

```json
{
    "solution": [1, 2, 9, 5, 4, 6, 7, 8, 3],
    "values": [1, 2, 0, 0, 4, 6, 7, 8, 3],
    "difficulty": "expert"
}
```

The complete save is returned as one JSON string.

---

## Using the Returned Data

If the application needs the actual JavaScript object:

```js
const jsonString = await JSF(file);

const save = JSON.parse(jsonString);
```

If the JSON string itself is required:

```js
const jsonString = await JSF(file);
```

---

## CRC-32C Validation

JSF automatically validates the CSF file's CRC-32C checksum.

If the checksum does not match the save data, JSF throws an error instead of returning potentially corrupted data.

```js
try {

    const jsonString = await JSF(file);

} catch (error) {

    console.error(
        "Failed to parse CSF file:",
        error
    );

}
```

A CRC mismatch indicates that the file was **corrupted or modified**.

---

## Supported CSF Version

JSF currently supports:

```text
CEEDOKU-CSF/1
```

which corresponds to [**CSF 1.0**](https://ceedoku.github.io/csfspec).

The parser follows the CSF 1.0 specification for:

* File headers
* CRC-32C
* Key shortcodes
* Fixed-width fields
* Sudoku cell encoding
* Given-state encoding
* Notes encoding
* Boolean values
* Difficulty values
* Integer values
* Time values
* Undo/redo JSON
* EOF handling

---

## Using JSF in Other Projects

JSF is not limited to Ceedoku.

Any JavaScript application can use JSF to read CSF files and recover their JSON representation.

For example:

```js
async function importSave(file) {

    const jsonString =
        await JSF(file);

    const save =
        JSON.parse(jsonString);

    return save;
}
```

This allows applications to use CSF as a portable binary save format while using ordinary JavaScript objects internally.

---

## Error Handling

JSF throws an error when a file is invalid.

Possible errors include:

```text
Invalid CSF header
Invalid CRC-32C checksum
Unknown CSF shortcode
Invalid field value
Invalid difficulty
Invalid Sudoku value
Invalid selected cell
Invalid undo/redo JSON
Missing undo/redo separator
Unexpected data after EOF
Unsupported CSF version
```

Applications should use `try...catch` when parsing files:

```js
try {

    const jsonString =
        await JSF(file);

} catch (error) {

    console.error(error);

}
```

---

## Design Goals

JSF is designed to be:

* **Simple** — one function parses a CSF file.
* **Portable** — CSF files are not tied to one application.
* **Reliable** — CRC-32C detects corrupted or modified files.
* **Dependency-free** — JSF requires no external libraries.
* **Browser-friendly** — works directly with `File` and `Blob` objects.
* **Predictable** — returns a JSON string rather than a custom data structure.
* **Compatible** — follows the shared CSF specification.

---

## License

JSF is an implementation of the [**Ceedoku Save Format (CSF)** specification.](https://ceedoku.github.io/csfspec)

Anyone may implement CSF-compatible serializers and parsers according to the CSF specification.
