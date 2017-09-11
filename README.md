# An introduction to OpenRefine

An Introduction to OpenRefine (OR)

## What is OpenRefine?

OR is a generic data swiss-army knife. Using OR, one can quickly transform structure, filter out irrelevant data, or enrich existing data. The tool itself looks like a glorified spreadsheet editor, and hence the most basic "structure" that can be overlayed over existing data is to place everything into columns and rows.

The idea behind OR is to incorporate it into a larger workflow. Perhaps you are workig with one format, but need another. Perhaps you need a complex subset of a set of data, or maybe you need to merge two separate pieces of data. Along the way, by filtering and faceting, you constrain the data into subsets, run hypotheses about the total data-set, identify and fix irregularities, before spitting the data out again and moving on.

## A first look

OR is a service that runs on your computer, and is accessible through your web-browser at http://127.0.0.1:3333. If the programs appears to stall, then first try loading OR in a new tab or window in your browser, and second, try exiting/quiting the service/application.

When opening OR, you are first prompted to open an existing project, or create a new one.

## Creating a new project

To get started, lets load a basic data source: the [language thesaurus](http://www1.aiatsis.gov.au/index.asp). Here's the example from the explanatory header:

```
Gamilaraay / Gamilaroi / Kamilaroi language (D23) (NSW SH55-12)
	UF:	D23 (language code)
	  	Kamilaroi language (D23) (NSW SH55-12)
	BT:	Wiradjuric language group
	NT:	Gawambaray language (D39) (Qld SH55-16)
	  	Gunybaray language (D15) (NSW SI55-4)
	  	Wiriyaraay language (D28) (NSW SH56-5)
	  	Yuwaalaraay / Euahlayi / Yuwaaliyaay language (D27) (NSW 
		SH55-7)

```
This file uses indentation to indicate hierarchy, abbreviations to differentiate data types (UF, BT, NT), repeats values across lines to indicate further items of the same type, and also line wraps items.

###### Steps:

* copy the text of the thesaurus
* `Create Project` > `Clipboard`
* Cut and paste the core of the document into OR.
* `Next` > `Line based text files`
* (No checked options)
* `Project name` => `Language thesaurus`

## The basic interface

### rows

Before we get started, notice at the top of the page, that the project lists the number of rows. This number will change based on context. Rember this number: 13290. Unless we do something to _add_ rows, we will generally be operating on a number of rows equal to, or less than this number.

### pagination

Display of rows is currently limited to 10 at a time. You can easily expand to up to 50 rows in a page. We can then move through the rows a page at a time, or jump to the last page and move backwards.

Pay particular attention to "rows" and "records".If we switch to "records", then we will be viewing 5-50 records per page. Right now, each record is just one row long, but if there are blank rows, or if we have multiple colums and there are blank values in the first column, this affects the size of the record. If you're not careful, the record size can be enormous. Displaying 50 records, with thousands or millions of rows can seize up OR.

### operations

At the top of each column is a downwards pointing arrow. This is a menu of operations that apply in the context of that column. The "All" column has some special operations, some of which apply across the whole project.

For the most part, operations only apply to the current _context_. The current context is the entire dataset. To change the context we must filter or facet.

## Filtering

The first, and most basic operation is to filter text. On mass, there is a lot of data in this dataset. We want to sift out parts of the file, and operate just on that.

###### steps:

* `Column 1` menu > `Text filter`

To get a feel for our data, lets search for a few things. Filters (and later Facets) all appear on the left.

There are key phrases in the text. For instance, `(language code)` or `(people)`. Searching for both phrases, we can see already that the number of each in the thesaurus don't match. Searching just for `language` or `people` also returns more results than the more restrictive search... we'll need to be careful!

## Columns

Currently, we just have a flat list of lines. But clearly there's a lot more going on in this data set. If we sift parts of this list into separate columns, we can uncover the structure within it.

To start, lets reconstruct part of the structure of the original file as indicated by indentation. We have headings, and then items grouped under those headings.

###### steps:

* `Column 1` menu > `Text filter`
* search for a tab character: `^\t` (check `regular expression`)
* `Column 1` menu > `Edit column` > `add column based on this one`
* `New column name` => `indent`
* Click `OK`
* clear the filter

This will create a new column with just the indented items in it.

## Faceting

Faceting is a kind of filter that agregates values within a column according to a criteria.

The simplest facet is to group based on the value of the cells in a column (Column menu > `Facet` > `Text facet`), but for now, we have too many unique values for this to be useful.

Facets can also be based on a calculated value. For instance, a simple, and extremely common operation is to facet based on whether a cell is blank/empty or not.

###### steps:

* `indent` menu > `Facet` > `Customized facet` > `Facet by blank`
* Click `true`

We now have the _inverse_ of the set we matched before. In other words, the values in `Column 1` correspond to the lines that _don't_ start with a tab character. Let's put this in a separate column called `heading`.

###### steps:

* `Column 1` menu > `Edit column` > `add column based on this one`
* `New column name` => `heading`
* Click `OK`
* `Column 1` menu > `Edit column` > `Remove this column`

We don't need `Column 1` any more, so we remove it.

## Undo/Redo

Don't be afraid to delete data! It's easy to step backwards and forwards through the steps in a project. In the left hand panel, there is a tab for `Undo / Redo`. We can jump back to a prior state just by choosing an earlier item in the list.

In addition to this, we can extract a list of operations and reapply them. This is an advanced topic at this stage, however.

## Records

We now have the beginnings of a "record" structure. If we switch from `rows` to `records`, items in the `indent` column are grouped under our `heading` values.

Records are extremely useful, and they are determined by the column in the left-most position. With a little care, we can construct multiply-nested structures and operate on any level in that nesting.

### bring rows into a single cell

Sometimes it makes sense to switch between items in rows and items in columns. An intermediary state between the two is to bring all the rows in a single column into a single row, separated by some kind of delimiter character.

Lets join all the indented cells togther into a single row within the same column. First check that the character(s) we use as a delimiter are not already in use. Search for the `@` symbol in the indent column. We should have zero matches, which means it'll be a suitable delimiter.

###### steps:

* `indent` menu > `edit cells` > `Join multi-valued cells...`
* enter `@` and click `OK`

We can reverse what we've done, by splitting each cell it into rows again:

###### steps:

* `indent` menu > `edit cells` > `Split multi-valued cells...`
* enter `@` and click `OK`

Note however, that we don't have blank cells at the top of every record. The blank line at the top of each record is _not_ preserved when joining. This is actually a useful feature, as it's a way of compressing the record into the structure we're looking for.

In a more complicated record structure (one with more columns, with differing numbers of rows in a record) blank cells at the end or in the middle of a record are also not preserved.

Depending on the structure, one needs to be very careful with these operations.

## Categorizing data

Not clear in the thesaurus structure is that the headings fall into several types. Labelling these different types will help us later on to operate on different parts of the whole data set.

For now, lets revert to the grouped version of the indented values. That way we can easily keep the indented values together with their heading while we sort them.

###### steps:

* In the left hand panel, under `Undo / Redo` jump back to step 4 (`Join multi-valued cells in column indent`)
* filter the `heading` column based on `language code`
* add column `heading_type` and enter `"language"` as the `expression`
* `headind_type` menu > `Facet` > `Text facet`
* clear the filter

Our text facet on the `heading_type` column now shows that there are two possible values: `language` or `(blank)`. In text facets we can change the terms in our categorization on the fly. If we hover over `language` in the facet, we can `edit` and enter `language code`.

We can also combine this with other filtering to further step through our categorization.

###### steps:

* filter on `(people)`
* edit `(blank)` in the facet, changing it to `people code`
* clear the filter

Note that you can use the facet to filter results by clicking on the relevant item. You can also sum together facet terms by clicking on `include`. It is also possible to invert the filter, by clicking on `invert`.

## Combining filters and facets

One of the truly powerful features of OR is combining multiple facets and filters. Used together, one can slice out precise subsets.

Lets combine the filter and facet to further categorize our headings.

###### steps:

* facet on `(blank)` and filter on `language`
* edit the `(blank)` facet, changing it to `language term`
* change the filter to `people`

## Cleaning mistakes in data

There is no one way to expose and fix errors in data. It's reasonable to assume though, that in the process of adding structure or transforming data, that irregularities will pop up. The general principle is to avoid hand editing individual cells, and to looks always for patterns that express the irregularities.

Careful use of facets often exposes irregularities. Filtered on `people` note that there is a single `language code` which contains `people`. Faceting on `language code` shows that there is a langauge term which contains the string `people` within it... which is not a problem. Always good to check!

Switching back to the `(blank)` facet, we can see at the top of the list that there is a formatting error for one of the values. For `A50 people` we would have expected that it should have the form `A50 (people)`. We could edit the cell, or we could just add `people code` for that one item, but it is usually better to figure out if there is something more sytematic going on.

First, lets exclude the first result by exploiting the fact that there's no space after people in `A50 people`. Changing the filter to include a space knocks out more than one result... clearly there's more going on.

###### steps:

* facet on `(blank)`, add space to end of filter `people `
* edit `(blank)`, changing it to `people term`

This is probably a good point to think "perhaps I got the language codes wrong too". Jumping back to previous transformations is an ordinary part of working with OR. You may have to Undo, or it may be possible to just edit existing terms again.

A quick way to flag items that you want to return to is to use the `All` menu > `Edit rows` > `Star rows` or `Flag rows`. When finished follow the same path to unstar rows. Use a facet on flags or stars to contrain the view.

###### steps:

* facet on `language term` and similarly filter on `language ` (with a space at the end)
* `All` menu > `Edit rows` > `Star rows`
* clear the filter, facet on `language term`
* `All` menu > `Facet` > `By star`
* Choose `false` in the facet

A quick scan of the unexpected `language term` items shows two patterns: a trailing comma, or a slightly different type: "languages".

###### steps:

* filter on `language,`
* `All` menu > `Edit rows` > `Star rows`
* filter on `languages`, facet on `langauge term`
* change facet `language term` to `language group term`

The remaining `language term` values look valid. Remove the stars.

* clear the filter
* facet `Starred Rows`
* `All` menu > `Edit rows` > `Unstar rows`
* close the `Starred Rows` facet

Now finish the people terms.

* facet on `(blank)`, filter on `people,`
* change `(blank)` to `people term`
* facet on `(blank)`, filter on `moiety`
* change `(blank)` to `people term`
* facet on `(blank)`, filter on `islanders`
* change `(blank)` to `people term`
* facet on `(blank)`, filter on `clan`
* change `(blank)` to `people term`
* facet on `(blank)`

By systematically carving up the "problem space" we can expose all sorts of problems in the data without reviewing the whole list. Just try to remember to think in patterns the whole time, and resist the urge to edit by hand.

Only when we get down to a handful of irregular forms do we edit cells by hand. Hover over the relevant cells and click edit.

## An Aside on Regular Expressions

So far, I've been working hard to avoid using regular expressions in search terms. Some of the steps above would have been much more efficient if I had used regular expressions. It is possible to avoid them if you don't know them, and if you think carefully about your use of facets and filters.

However, OR becomes much more powerful if you do use regular expressions. If you do intend to use them, then note that there is a bug in the current version of OR: an incomplete expression involving parentheses `()` or square brackets `[]` will make the interface hang. Refreshing the page will bring it back, but you will lose your filters and facets.

## Sub records
Consulting the header for the thesaurus, we can see that special codes are used to indicate a relationship between a heading and an item under a heading. Lets extract that relationship and make it more structural.

Without any filters or facets enabled, and using `rows` mode:

###### steps:

* `Indent` menu > `Edit cells` > `Split multi-valued cells...` > `@` > `OK`
* filter `Indent` column with `USE: ` (check `case sensitive`)
* `Indent` menu > `Edit column` > `Add column based on this column...`
* `New column name` => `indent_type`
* `Expression` => `"use"`
* `indent_type` menu > `Facet` > `Text facet`
* filter `Indent` column with `BT: `
* change `(blank)` facet to `broader term`
* repeat with "NT: " -> "Narrower Term" and
* "UF: " -> "Use for"

There is also an undocumented type in the thesaurus: "RT: ".

### Fill down

If we move the `Indent_type` column to the left-most position, we will change the record structure. Before we do that though, first lets "fill down" the values in the `heading` and `heading_type` columns. This looks for any blank cells and copies the last value above it.

###### steps:

* `heading` column > `Edit cells` > `Fill down`
* `heading_type` column > `Edit cells` > `Fill down`

It can be a good idea to just double check for duplicates before doing this, as when there are consecutive records with the same value, blanking down will destroy that record structure.

( menu > `Facet` > `Customized facets` > `Duplicates facet`)

(Filter out blank cells first using a blanks facet. Duplicates that are not adjacent are not a problem.)

### Restructuring data

If we move the `indent_type` column to the left-most position, we would now have inverted the relationship expressed in `records` mode.

###### steps:

* `Indent_type` menu > `Edit column` > `Move column to beginning`
* switch to `records` mode## steps

## GREL

So far, I've been avoiding touching upon the fact that under the hood is [a simple programming language](https://github.com/OpenRefine/OpenRefine/wiki/General-Refine-Expression-Language) which can be used for contructing custom facets, or for making far more complex transformations on cells.

To use OR for more advanced transformations, it's difficult to avoid occassionally constructing small snippets of code. To get a sense of these snippets, a good place to start is to look at the code underlying some of the pre-programmed facets. Facets don't actually change anything, so feel free to experiment with any facet in the Facets menu.

The most basic is the `Text facet`. This is based just on whatever the value of the cell is. By clicking on `change` for the `language type` facet, we can see that the GREL code is simply `value`.

If we modify this to `value.split(' ')` and click `OK` our facet changes to be based on the words in the categorization. Choosing `language` will now match `language term`, `language group term` and `language code` (because they all contain `language`).


## Search and replace

So far, wee've been extracting key data out of our data set, but we've hit a point where we need to modify the data that is there. A basic and common operation that we need to perform is stripping out, or replacing parts of a cell of data.

A basic and built-in transformation is the stripping of certain kinds of white space.

###### steps:

* `indent` menu > `Edit cells` > `Common transforms` > `Trim leading and trailing whitespace`
* `indent` menu > `Edit cells` > `Common transforms` > `Collapse consecutive whitespace`

Let's also remove the key words at the start of some cells (BT, NT, RT, UF and USE):

###### steps:

* `indent` menu > `Edit cells` > `Transform`
* `Expression` => `value.replace(/^([BRN]T|UF|USE): /,'')`

This regular expression (between `//`) matches BT, NT, RT, UF and USE. This could instead be expressed by multiple `replace()` operations (e.g., `value.replace('BT: ','').replace('NT: ','').

### Facet by Record size

Facets are filters that categorize the data in a column. When we make custom facets, this massively expands what is possible. For a start, we can define the calculation in whatever way we want, and we can expand beyond a calculation based on individual cells. We can, for instance, expand into neighboring columns, or we can expand a calculation into all rows in a record.

For a basic example, let's facet based on the number of rows in a record. The data in indent includes a mix of line-wrapping and separate items. We want to remove the line-wrapping so that we only deal with a single item per row.

Let's create a custom facet:

###### steps:

* `indent` menu > `Facet` > `Custom text facet...`
* `Expression` => `row.record.cells.indent.length()`

The facet will change depending on what other facets and filters are active. If we facet by `indent_type` and choose `broader term` we have a small list of 2 and 3 line records. Most broader terms only map onto one term.

## Patterns in data

(Download)[Language-Thesaurus.csv] a further transformed version of the thesaurus, and try to use facets to spot and fix further errors in the data.