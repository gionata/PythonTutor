<!--
author:   Gionata Massi
version:  0.1.1
language: en
comment:  LiaScript macros to open code blocks in Python Tutor (step-through visualization for C, C++, Python, Java, JavaScript).

@PythonTutorLang: @PythonTutorBase(@0)

@PythonTutor.cpp:  @PythonTutorLang(cpp)
@PythonTutor.c:    @PythonTutorLang(c)
@PythonTutor.py:   @PythonTutorLang(311)
@PythonTutor.java: @PythonTutorLang(java)
@PythonTutor.js:   @PythonTutorLang(js)

@PythonTutorEmbedLang: @PythonTutorEmbedBase(@0)

@PythonTutorEmbed.cpp:  @PythonTutorEmbedLang(cpp)
@PythonTutorEmbed.c:    @PythonTutorEmbedLang(c)
@PythonTutorEmbed.py:   @PythonTutorEmbedLang(311)
@PythonTutorEmbed.java: @PythonTutorEmbedLang(java)
@PythonTutorEmbed.js:   @PythonTutorEmbedLang(js)

@PythonTutorBase
<script run-once>
const code = encodeURIComponent(String.raw`@'input`);
const lang = `@0`;
const url =
  "https://pythontutor.com/visualize.html?via=liascript" +
  "#code=" + code + "&mode=display&py=" + encodeURIComponent(lang);
`HTML: <p><a href="${url}" target="_blank" rel="noopener">Open in Python Tutor (${lang})</a></p>`;
</script>
@end

@PythonTutorEmbedBase
<script run-once>
const code = encodeURIComponent(String.raw`@'input`);
const lang = `@0`;
const url =
  "https://pythontutor.com/iframe-embed.html" +
  "#code=" + code +
  "&codeDivHeight=400&codeDivWidth=350&curInstr=0&py=" + encodeURIComponent(lang);
`HTML: <iframe width="100%" height="480" frameborder="0" src="${url}" title="Python Tutor"></iframe>`;
</script>
@end
-->

# Python Tutor – LiaScript Template

Open a code block in [Python Tutor](https://pythontutor.com) so learners can
**step through execution** and inspect the call stack, variables, and heap.

Despite the name, Python Tutor visualizes **Python, C, C++, Java, and JavaScript**
on real language implementations (server-side).

**Try this template in LiaScript:**  
https://liascript.github.io/course/?https://raw.githubusercontent.com/gionata/PythonTutor/main/README.md

**Repository:**  
https://github.com/gionata/PythonTutor

---

## Why this template?

LiaScript already has templates for *running* code (Pyodide, Skulpt, CodeRunner,
JSCPP). This one does something different: it builds a **Python Tutor URL** from
the code in a block so students can **see memory and control flow**, which is
especially useful when teaching C and C++ (pointers, stack frames, references).

There was no dedicated LiaScript template for Python Tutor at the time of writing
(see [LiaTemplates](https://github.com/liaTemplates) and
[topic `liascript-template`](https://github.com/topics/liascript-template)).

---

## Installation

Add one line to your course header comment:

```text
import: https://raw.githubusercontent.com/gionata/PythonTutor/main/README.md
```

---

## Quick start

```cpp
#include <iostream>

int main() {
    int x = 3;
    int y = x;
    std::cout << y << "\n";
    return 0;
}
```
@PythonTutor.cpp

```python
x = [1, 2, 3]
y = x
y.append(4)
print(x)
```
@PythonTutor.py

---

## Macros

| Macro | Language (`py=` parameter) |
|-------|----------------------------|
| `@PythonTutor.cpp` | `cpp` |
| `@PythonTutor.c` | `c` |
| `@PythonTutor.py` | `311` (Python 3.11) |
| `@PythonTutor.java` | `java` |
| `@PythonTutor.js` | `js` |
| `@PythonTutorLang(LANG)` | custom `LANG` string |
| `@PythonTutorEmbed.cpp` (etc.) | same languages, **iframe** embed |

**Default behaviour (`@PythonTutor.*`):** opens a link in a new tab
(`visualize.html`, `mode=display`). Prefer this when iframe embedding is blocked.

**Embed behaviour (`@PythonTutorEmbed.*`):** inserts an iframe
(`iframe-embed.html`). Some browsers or school networks may restrict third-party
iframes; fall back to the link macros if needed.

---

## Examples

### C++ (link)

```cpp
#include <iostream>

int main() {
    int a = 10;
    int* p = &a;
    *p = 20;
    std::cout << a << "\n";
    return 0;
}
```
@PythonTutor.cpp

### C

```c
#include <stdio.h>

int main(void) {
    int xs[3] = {1, 2, 3};
    printf("%d\n", xs[1]);
    return 0;
}
```
@PythonTutor.c

### Python 3.11

```python
def f(n):
    if n <= 1:
        return n
    return f(n - 1) + f(n - 2)

print(f(5))
```
@PythonTutor.py

### Custom language parameter

```cpp
int main() { return 0; }
```
@PythonTutorLang(cpp)

### Embed (iframe)

```python
s = "hi"
t = s
```
@PythonTutorEmbed.py

---

## URL format

Links follow the official Python Tutor scheme
([guide for tool builders](https://pythontutor.com/for-ai-assistants.html)):

```text
https://pythontutor.com/visualize.html?via=liascript#code=<ENCODED>&mode=display&py=<LANG>
```

| Parameter | Role |
|-----------|------|
| `code` | Source, `encodeURIComponent` (newlines → `%0A`) |
| `mode` | Use `display` so visualization opens immediately |
| `py` | `cpp`, `c`, `311`, `java`, `js` |
| `via` | Optional attribution (`liascript`) |

Legacy values such as `cpp_g++9.3.0` are not required for new links; use `cpp`.

---

## Limits

- **Online only:** visualization runs on pythontutor.com (not offline / PWA-only).
- **Snippet size:** very large programs can exceed practical URL length; keep examples short.
- **Iframes:** may be blocked; the link macros remain the safer default.

---

## Implementation

Macro definitions live in the **document header comment** (the first `<!-- ... -->`).
LiaScript loads only that header when the file is imported via `import:`.

The macros are block macros (`@Name` … `@end`) that inject a small `<script run-once>`
which encodes the preceding code block (`@input`) and produces either a link or an iframe.

---

## License

[CC0 1.0 Universal](LICENSE) — public domain dedication where applicable.

Python Tutor is a separate service by [Philip Guo](https://pg.ucsd.edu/); this
template only builds links/embeds to that service.
