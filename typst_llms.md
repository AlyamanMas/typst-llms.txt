This document provides a condensed guide to writing documents using the Typst typesetting language. It is designed to be a comprehensive starting point for a user or Large Language Model (LLM) with no prior knowledge of Typst, focusing on core syntax and concepts to enable effective document creation.

## 1. Introduction to Typst

Typst is a modern, markup-based typesetting system designed to be a powerful alternative to tools like LaTeX and simpler applications like Word. You write your document in a plain text file (`.typ`) using simple syntax and function calls, which a compiler then typesets into a PDF. No special preamble or boilerplate is required to start a document.


## 2. Core Concepts: Modes

Typst operates in three distinct syntactical modes. Understanding how to switch between them is fundamental.

  * **Markup Mode**: This is the **default mode** for writing prose and using basic formatting syntax.
  * **Code Mode**: Activated with a hash symbol (`#`), this mode is for calling functions, defining variables, and using logic. Once inside a code context (like a function's parentheses), you don't need additional hashes.
  * **Math Mode**: Activated by enclosing expressions in dollar signs (`$`), this mode is for typesetting mathematical formulas.

```typst
Markup text... then enter #code(mode) to call a function.
An inline equation looks like this: $E = m c^2$.
```

Note that since $ and # are used ot switch modes, they can't be used as is to write out the dollar or hashtag symbols in markup mode as the compiler would think you are trying to go into code or math mode. Instead, you can escape them with \. Same goes for ~, *, /, and @

Also note that we can switch from code mode to markup mode using square brackets []. So if for example we would like to have a multiline list item, one way to do it is to indent all the lines as follows:
```typst
- item 1
- item 2 line 1
  item 2 line 2
```
or another way is to use #[], which allows us to have one content/markup block as the list item. This would look like this:
```typst
- item 1
- #[ item 2 line 1
item 2 line 2
]
```
Note that, more programmatically, square brackets in code mode can be used to represent a `content` type. So when calling a function for example, if the function expects a parameter of type content, we can use [] to have that. An example can be seen in section 6, 'Creating Tables,' where Apple is put inside [] since the table function expects content variadic arguments, and we use [] instead of #[] since inside a function call we are already inside a code mode, while outside of it like in the list example above, we would first need to use # to go into code mode.

## 3. Basic Markup Syntax

Typst uses simple, intuitive syntax for common formatting tasks.

### Paragraphs and Headings

  * **Paragraphs**: Separate paragraphs with a **blank line**.
  * **Headings**: Create headings using one or more equals signs (`=`) followed by a space. The number of equals signs determines the heading level.

```typst
= Level 1 Heading

This is the first paragraph.

== Level 2 Heading

This is a paragraph under a subheading.
```

### Text Formatting

  * **Bold**: Enclose text in single asterisks: `*strong emphasis*`.
  * **Italics**: Enclose text in single underscores: `_emphasis_`.
  * **Line Break**: End a line with a backslash `\` to force a line break.
  * **Links**: URLs starting with `http://` or `https://` are automatically converted into links.

### Lists

  * **Bulleted List**: Start each item with a hyphen (`-`) and a space.
  * **Numbered List**: Start each item with a plus sign (`+`) and a space.
  * **Term List**: Use the format `/ Term: Description`.
  * **Nesting**: Indent items to create nested lists.

```typst
- Bullet item 1
  + Numbered sub-item 1
  + Numbered sub-item 2
- Bullet item 2

/ Typst: A modern typesetting system.
```


## 4. Using Functions

Functions are used for everything beyond basic markup, from inserting images to complex styling.

  * **Function Calls**: To call a function in markup, start with a hash (`#`), followed by the function name and parentheses: `#function()`.
  * **Arguments**:
      * **Positional**: Unnamed values passed in order. Example: `#image("photo.jpg")`.
      * **Named**: Key-value pairs. Example: `width: 70%`.
  * **Content Blocks**: Pass formatted markup to a function by enclosing it in square brackets (`[...]`). A content block can be a positional or named argument.

### Example: Images and Figures

To insert a captioned image, use the `figure` function, which takes the `image` function call as a positional argument and the caption as a named argument.

```typst
// A figure with a caption and a label for cross-referencing.
#figure(
  image("glacier.jpg", width: 70%),
  caption: [A photo of a glacier.],
) <glacier-fig>

As seen in @glacier-fig, glaciers are large bodies of ice.
```

  * The `image()` function takes the file path (a **string** in double quotes) and a named `width` argument.
  * The `figure()` function wraps the image and adds a `caption` using a content block `[...]`.
  * A **label** `<glacier-fig>` is attached to the figure to make it referenceable.
  * A **reference** `@glacier-fig` is used to refer to the figure in the text.


## 5. Writing Mathematics

Typst has a clean and powerful syntax for math.

  * **Inline vs. Display Math**:
      * For inline math, enclose the formula in dollar signs: `$a^2$`.
      * For a centered, block-level equation, ensure there is a space after the opening `$` and before the closing `$`: `$ a^2 + b^2 = c^2 $`.
  * **Common Syntax**:
      * **Superscripts**: Use `^`. Example: `$x^2$`.
      * **Subscripts**: Use `_`. Example: `$a_1$`.
      * **Fractions**: Use `/`. Example: `$x/2$`.
      * **Grouping**: Use parentheses `()` for multi-character scripts. Example: `$N_(i+1)$`.
  * **Symbols and Functions**: Common symbols are available by name (`pi`, `alpha`, `sum`, etc.). Single letters are typeset as variables, while multi-letter words are interpreted as symbols or functions. Therefore, if you would like to have a sequence of single letter variables, like abc, you need to have it spelled as $a b c$ since $abc$ would be interpreted as one symbol bound to the `abc` identifier.

```typst
The Pythagorean theorem is $a^2 + b^2 = c^2$.

The sum of the first n integers is given by the formula:
$ sum_(i=1)^n i = (n(n+1)) / 2 $
```


## 6. Creating Tables

Use the `#table()` function to arrange content in a grid.

  * **Columns**: The `columns` argument defines the number and size of columns. It can be an integer for equal-width columns or an array of sizes (`auto`, `1fr`, `2cm`, `40%`).
  * **Cells**: Provide the content for each cell as a positional argument in row-major order.
  * **Headers**: It's good practice to wrap header cells in `table.header()`.

```typst
#table(
  columns: (auto, 1fr),
  stroke: 0.5pt, // Add lines to the table
  align: (left, right),
  table.header[*Item*, *Price*],
  [Apples], [1.50],
  [Oranges], [2.25],
)
```


## 7. Global Styling with Set Rules

Set rules allow you to define styles that apply to all subsequent elements of a certain type, similar to CSS or LaTeX declarations.

  * **Syntax**: Use `#set` followed by a function call: `#set function(argument: value)`.
  * **Usage**: Apply document-wide styles for pages, headings, text, and more.

### Common Set Rules

  * **Page Layout**: `#set page(margin: (x: 2cm, y: 2.5cm), paper: "us-letter")`.
  * **Text Defaults**: `#set text(font: "New Computer Modern", size: 11pt)`.
  * **Heading Numbering**: `#set heading(numbering: "1.a")`.
  * **Paragraph Style**: `#set par(justify: true)`.

```typst
// Apply styles at the top of the document.
#set page(paper: "a5", margin: 1.5cm)
#set heading(numbering: "1.")

= Introduction
This document uses A5 paper and has numbered headings.
```

## 8. Summary: Key Syntax Differences

To avoid mixing Typst with other languages, remember these key distinctions:

| Feature | Typst Syntax | Common Incorrect Syntax (from Markdown/LaTeX) |
| :--- | :--- | :--- |
| **Comments** | `// Line` or `/* Block */`  | `% Line` |
| **Bold** | `*Bold*`  | `**Bold**` or `\textbf{Bold}` |
| **Italics** | `_Italics_`  | `*Italics*` or `\emph{Italics}` |
| **Headings** | `= L1`, `== L2`  | `# L1`, `## L2` or `\section{L1}` |
| **Functions** | `#function(arg: val)`  | `\command{arg}` |
| **Math** | `$ a $` (display)  | `$$a$$` or `\[ a \]` |
| **Document** | Starts immediately.  | Requires preamble and `\begin{document}`. |
