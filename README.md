<!--This README is auto-generated from `docs/README.md`. Do not edit this file directly.-->

-   [`pantable`](#pantable)
    -   [Example](#example)
    -   [Install and Use](#install-and-use)
    -   [Syntax](#syntax)
-   [`pantable2csv`](#pantable2csv)
-   [Related Filters](#related-filters)

[![Build Status](https://travis-ci.org/ickc/pantable.svg?branch=master)](https://travis-ci.org/ickc/pantable) [![GitHub Releases](https://img.shields.io/github/tag/ickc/pantable.svg?label=github+release)](https://github.com/ickc/pantable/releases) [![PyPI version](https://img.shields.io/pypi/v/pantable.svg)](https://pypi.python.org/pypi/pantable/) [![Development Status](https://img.shields.io/pypi/status/pantable.svg)](https://pypi.python.org/pypi/pantable/) [![Python version](https://img.shields.io/pypi/pyversions/pantable.svg)](https://pypi.python.org/pypi/pantable/) <!-- [![Downloads](https://img.shields.io/pypi/dm/pantable.svg)](https://pypi.python.org/pypi/pantable/) --> ![License](https://img.shields.io/pypi/l/pantable.svg) [![Coveralls](https://img.shields.io/coveralls/ickc/pantable.svg)](https://coveralls.io/github/ickc/pantable) <!-- [![Scrutinizer](https://img.shields.io/scrutinizer/g/ickc/pantable.svg)](https://scrutinizer-ci.com/g/ickc/pantable/) -->

The pantable package comes with 2 pandoc filters, `pantable.py` and `pantable2csv.py`. `pantable` is the main filter, introducing a syntax to include CSV table in markdown source. `pantable2csv` complements `pantable`, is the inverse of `pantable`, which convert native pandoc tables into the CSV table format defined by `pantable`.

Some example uses are:

1.  You already have tables in CSV format.

2.  You feel that directly editing markdown table is troublesome. You want a spreadsheet interface to edit, but want to convert it to native pandoc table for higher readability. And this process might go back and forth.

3.  You want lower-level control on the table and column widths.

4.  You want to use all table features supported by the pandoc’s internal AST table format, which is not possible in markdown for pandoc &lt;= 1.18.[1]

[1] In pandoc 1.19, grid-tables is improved to support all features available to the AST too.

`pantable`
==========

This allows CSV tables, optionally containing markdown syntax (disabled by default), to be put in markdown as a fenced code blocks.

Example
-------

Also see the README in [GitHub Pages](https://ickc.github.io/pantable/). There’s a [LaTeX output](https://ickc.github.io/pantable/README.pdf) too.

    ```table
    ---
    caption: '*Awesome* **Markdown** Table'
    alignment: RC
    table-width: 0.7
    markdown: True
    ---
    First row,defaulted to be header row,can be disabled
    1,cell can contain **markdown**,"It can be aribrary block element:

    - following standard markdown syntax
    - like this"
    2,"Any markdown syntax, e.g.",$E = mc^2$
    ```

becomes

<table style="width:70%;">
<caption><em>Awesome</em> <strong>Markdown</strong> Table</caption>
<colgroup>
<col width="8%" />
<col width="27%" />
<col width="34%" />
</colgroup>
<thead>
<tr class="header">
<th align="right"><p>First row</p></th>
<th align="center"><p>defaulted to be header row</p></th>
<th><p>can be disabled</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="right"><p>1</p></td>
<td align="center"><p>cell can contain <strong>markdown</strong></p></td>
<td><p>It can be aribrary block element:</p>
<ul>
<li>following standard markdown syntax</li>
<li>like this</li>
</ul></td>
</tr>
<tr class="even">
<td align="right"><p>2</p></td>
<td align="center"><p>Any markdown syntax, e.g.</p></td>
<td><p><span class="math inline"><em>E</em> = <em>m</em><em>c</em><sup>2</sup></span></p></td>
</tr>
</tbody>
</table>

(The equation might not work if you view this on PyPI.)

Install and Use
---------------

Install:

``` bash
pip install -U pantable
```

Use:

``` bash
pandoc -F pantable -o README.html README.md
```

Syntax
------

Fenced code blocks is used, with a class `table`. See [Example](#example).

Optionally, YAML metadata block can be used within the fenced code block, following standard pandoc YAML metadata block syntax. 7 metadata keys are recognized:

-   `caption`: the caption of the table. If omitted, no caption will be inserted. Default: disabled.

-   `alignment`: a string of characters among `L,R,C,D`, case-insensitive, corresponds to Left-aligned, Right-aligned, Center-aligned, Default-aligned respectively. e.g. `LCRD` for a table with 4 columns. Default: `DDD...`

-   `width`: a list of relative width corresponding to the width of each columns. e.g.

    ``` yaml
    - width
        - 0.1
        - 0.2
        - 0.3
        - 0.4
    ```

    Default: auto calculated from the length of each line in table cells.

-   `table-width`: the relative width of the table (e.g. relative to `\linewidth`). default: 1.0

-   `header`: If it has a header row or not. True/False/yes/NO are accepted, case-insensitive. default: True

-   `markdown`: If CSV table cell contains markdown syntax or not. Same as above. Default: False

-   `include`: the path to an CSV file, can be relative/absolute. If non-empty, override the CSV in the CodeBlock. default: None

When the metadata keys is invalid, the default will be used instead.

`pantable2csv`
==============

This one is the inverse of `pantable`, a panflute filter to convert any native pandoc tables into the CSV table format used by pantable.

Effectively, `pantable` forms a “CSV Reader”, and `pantable2csv` forms a “CSV Writer”. It allows you to convert back and forth between these 2 formats.

For example, in the markdown source:

    +--------+---------------------+--------------------------+
    | First  | defaulted to be     | can be disabled          |
    | row    | header row          |                          |
    +========+=====================+==========================+
    | 1      | cell can contain    | It can be aribrary block |
    |        | **markdown**        | element:                 |
    |        |                     |                          |
    |        |                     | -   following standard   |
    |        |                     |     markdown syntax      |
    |        |                     | -   like this            |
    +--------+---------------------+--------------------------+
    | 2      | Any markdown        | $$E = mc^2$$             |
    |        | syntax, e.g.        |                          |
    +--------+---------------------+--------------------------+

    : *Awesome* **Markdown** Table

running `pandoc -F pantable2csv -o output.md input.md`, it becomes

    ``` {.table}
    ---
    alignment: DDD
    caption: '*Awesome* **Markdown** Table'
    header: true
    markdown: true
    table-width: 0.8055555555555556
    width: [0.125, 0.3055555555555556, 0.375]
    ---
    First row,defaulted to be header row,can be disabled
    1,cell can contain **markdown**,"It can be aribrary block element:

    -   following standard markdown syntax
    -   like this
    "
    2,"Any markdown syntax, e.g.",$$E = mc^2$$
    ```

Related Filters
===============

The followings are pandoc filters written in Haskell that provide similar functionality. This filter is born after testing with theirs.

-   [baig/pandoc-csv2table: A Pandoc filter that renders CSV as Pandoc Markdown Tables.](https://github.com/baig/pandoc-csv2table)
-   [mb21/pandoc-placetable: Pandoc filter to include CSV data (from file or URL)](https://github.com/mb21/pandoc-placetable)
-   [sergiocorreia/panflute/csv-tables.py](https://github.com/sergiocorreia/panflute/blob/1ddcaba019b26f41f8c4f6f66a8c6540a9c5f31a/docs/source/csv-tables.py)

<table>
<colgroup>
<col width="7%" />
<col width="26%" />
<col width="15%" />
<col width="13%" />
<col width="36%" />
</colgroup>
<thead>
<tr class="header">
<th></th>
<th>pandoc-csv2table</th>
<th>pandoc-placetable</th>
<th>panflute example</th>
<th>pantable</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>caption</td>
<td>caption</td>
<td>caption</td>
<td>title</td>
<td>caption</td>
</tr>
<tr class="even">
<td>aligns</td>
<td>aligns = LRCD</td>
<td>aligns = LRCD</td>
<td></td>
<td>aligns = LRCD</td>
</tr>
<tr class="odd">
<td>width</td>
<td></td>
<td>widths = &quot;0.5 0.2 0.3&quot;</td>
<td></td>
<td>width: [0.5, 0.2, 0.3]</td>
</tr>
<tr class="even">
<td>table-width</td>
<td></td>
<td></td>
<td></td>
<td>table-width: 1.0</td>
</tr>
<tr class="odd">
<td>header</td>
<td>header = yes | no</td>
<td>header = yes | no</td>
<td>header: True | False</td>
<td>header: True | False | yes | NO</td>
</tr>
<tr class="even">
<td>markdown</td>
<td></td>
<td>inlinemarkdown</td>
<td></td>
<td>markdown: True | False | yes | NO</td>
</tr>
<tr class="odd">
<td>source</td>
<td>source</td>
<td>file</td>
<td>source</td>
<td>include</td>
</tr>
<tr class="even">
<td>others</td>
<td>type = simple | multiline | grid | pipe</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td></td>
<td></td>
<td>delimiter</td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td></td>
<td></td>
<td>quotechar</td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td></td>
<td></td>
<td>id (wrapped by div)</td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>Notes</td>
<td></td>
<td></td>
<td></td>
<td>width are auto-calculated when width is not specified</td>
</tr>
</tbody>
</table>
