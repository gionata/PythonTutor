<!--
author:   Gionata Massi
version:  0.3
language: en
comment:  LiaScript macros to open code blocks in Python Tutor (step-through visualization for C, C++, Python, Java, JavaScript).

@PythonTutor.cpp:  @PythonTutorBase(cpp, C++)
@PythonTutor.c:    @PythonTutorBase(c, C)
@PythonTutor.py:   @PythonTutorBase(311, Python)
@PythonTutor.java: @PythonTutorBase(java, Java)
@PythonTutor.js:   @PythonTutorBase(js, JavaScript)
@PythonTutorLang:  @PythonTutorBase(@0, @0)

@PythonTutorEmbed.cpp:  @PythonTutorEmbedBase(cpp)
@PythonTutorEmbed.c:    @PythonTutorEmbedBase(c)
@PythonTutorEmbed.py:   @PythonTutorEmbedBase(311)
@PythonTutorEmbed.java: @PythonTutorEmbedBase(java)
@PythonTutorEmbed.js:   @PythonTutorEmbedBase(js)
@PythonTutorEmbedLang:  @PythonTutorEmbedBase(@0)

@PythonTutorBase
<script run-once>
(() => {
  const lang = "@0".trim();
  const label = "@1".trim();
  let rawCode = "";

  if (typeof document !== "undefined") {
    const allCodes = Array.from(document.querySelectorAll("pre code"));
    const prevIdx = typeof window.__ptLastCodeIdx === "number" ? window.__ptLastCodeIdx : -1;
    const nextIdx = allCodes.findIndex((_, i) => i > prevIdx);
    const targetIdx = nextIdx !== -1 ? nextIdx : allCodes.length - 1;
    if (targetIdx >= 0 && allCodes[targetIdx]) {
      window.__ptLastCodeIdx = targetIdx;
      const el = allCodes[targetIdx];
      rawCode = (el.textContent || el.innerText || "")
        .replace(/\r\n?/g, "\n")
        .replace(/\u00a0/g, " ");
    }
  }

  const rawDocLang =
    (typeof window !== "undefined" && (window.LIA?.lang || window.LIA?.language)) ||
    (typeof document !== "undefined" && (document.documentElement?.lang || document.body?.lang)) ||
    "en";
  const isItalian = String(rawDocLang).trim().toLowerCase().startsWith("it");
  const linkText = isItalian
    ? `Apri in Python Tutor (${label})`
    : `Open in Python Tutor (${label})`;

  const code = encodeURIComponent(rawCode);
  const url =
    "https://pythontutor.com/visualize.html?via=liascript" +
    "#code=" + code + "&mode=display&py=" + encodeURIComponent(lang);

  return `HTML: <p><a href="${url}" target="_blank" rel="noopener">${linkText}</a></p>`;
})();
</script>
@end

@PythonTutorEmbedBase
<script run-once>
(() => {
  const lang = "@0".trim();
  let rawCode = "";

  if (typeof document !== "undefined") {
    const allCodes = Array.from(document.querySelectorAll("pre code"));
    const prevIdx = typeof window.__ptLastCodeIdx === "number" ? window.__ptLastCodeIdx : -1;
    const nextIdx = allCodes.findIndex((_, i) => i > prevIdx);
    const targetIdx = nextIdx !== -1 ? nextIdx : allCodes.length - 1;
    if (targetIdx >= 0 && allCodes[targetIdx]) {
      window.__ptLastCodeIdx = targetIdx;
      const el = allCodes[targetIdx];
      rawCode = (el.textContent || el.innerText || "")
        .replace(/\r\n?/g, "\n")
        .replace(/\u00a0/g, " ");
    }
  }

  const rawDocLang =
    (typeof window !== "undefined" && (window.LIA?.lang || window.LIA?.language)) ||
    (typeof document !== "undefined" && (document.documentElement?.lang || document.body?.lang)) ||
    "en";
  const isItalian = String(rawDocLang).trim().toLowerCase().startsWith("it");
  const iframeTitle = isItalian
    ? `Visualizzatore Python Tutor (${lang})`
    : `Python Tutor (${lang})`;

  const frameId = "pt-embed-" + Math.random().toString(36).slice(2, 9);

  const computeTargetWidth = (iframeEl) => {
    const vw = typeof window !== "undefined"
      ? (window.innerWidth || document.documentElement?.clientWidth || 1024)
      : 1024;
    const parentW = iframeEl && iframeEl.parentElement
      ? iframeEl.parentElement.getBoundingClientRect().width
      : vw;
    // Se il contenitore è più stretto del viewport, proviamo a usare la larghezza del viewport
    // (lasciando un piccolo margine di sicurezza di 16px per evitare scrollbar orizzontale)
    return Math.max(320, Math.round(Math.max(parentW, vw - 16)));
  };

  const initialWidth = computeTargetWidth(null);
  const codeDivWidth = Math.round(Math.min(820, Math.max(320, initialWidth * 0.44)));

  const code = encodeURIComponent(rawCode);
  const url =
    "https://pythontutor.com/iframe-embed.html" +
    "#code=" + code +
    `&codeDivHeight=420&codeDivWidth=${codeDivWidth}&curInstr=0&py=` + encodeURIComponent(lang);

  if (typeof window !== "undefined") {
    const applyResponsiveWidth = () => {
      const el = document.getElementById(frameId);
      if (!el) return;
      const targetW = computeTargetWidth(el);
      const parentRect = el.parentElement ? el.parentElement.getBoundingClientRect() : null;
      el.setAttribute("width", String(targetW));
      el.style.width = targetW + "px";
      el.style.maxWidth = "none";
      if (parentRect && parentRect.width > 0 && targetW > parentRect.width) {
        // Centra l'iframe rispetto al contenitore padre per estenderlo sui lati
        const offset = Math.round((parentRect.width - targetW) / 2);
        el.style.position = "relative";
        el.style.left = offset + "px";
      } else {
        el.style.position = "static";
        el.style.left = "0px";
      }
    };

    window.addEventListener("resize", applyResponsiveWidth);
    setTimeout(applyResponsiveWidth, 50);
    setTimeout(applyResponsiveWidth, 300);
  }

  return `HTML: <iframe id="${frameId}" width="${initialWidth}" height="480" style="width:${initialWidth}px;max-width:none;height:480px;box-sizing:border-box;display:block;" frameborder="0" src="${url}" title="${iframeTitle}"></iframe>`;
})();
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

Explore how the macros work across different languages. In LiaScript, code blocks with attached evaluator macros are interactive: you can edit the code directly and click the **Execute** button (or press <kbd>Shift</kbd>+<kbd>Enter</kbd> / <kbd>Ctrl</kbd>+<kbd>Enter</kbd>) to generate the Python Tutor visualization link or embedded iframe in the terminal output.

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

**Python**:

```python
s = "hi"
t = s
```
@PythonTutorEmbed.py

**C++**:

``` cpp
#include <iostream>

int f(int n) {
    if (n <= 1) return n;
    return f(n - 1) + f(n - 2);
}

int main() {
    std::cout << f(5) << "\n";
}
```
@PythonTutorEmbed.cpp

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

Pattern (one level of indirection, same as Pyodide):

- `@PythonTutor.cpp` → `@PythonTutorBase(cpp)` (single-line)
- `@PythonTutorBase` is a **block** macro (`@end`) containing the `<script>` evaluator
- Inside the script, `"@'input"` is replaced by LiaScript with the code block content (escaped),
  `"@0"` by the language parameter, and `console.html(...)` renders the link or iframe directly into the output terminal

---

## License

[CC0 1.0 Universal](LICENSE) — public domain dedication where applicable.

Python Tutor is a separate service by [Philip Guo](https://pg.ucsd.edu/); this
template only builds links/embeds to that service.
