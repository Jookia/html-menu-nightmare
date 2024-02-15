Goals
-----

I want to make a choose your own adventure game in HTML using a web of choices
with explicit support for screen readers. The choices must be defined dynamically.

This page documents how much of a nightmare it is.

Testing
-------

I'm testing using the following combinations of platforms, readers and browsers:

- Linux, Firefox, Orca
- Linux, Chromium, Orca
- Windows, Firefox, NVDA
- Windows, Chrome, NVDA
- Windows, Edge, NVDA
- Windows, Firefox, Narrator
- Windows, Chrome, Narrator
- Windows, Edge, Narrator

These are the only platforms I have available for testing.

Test success is determined based on:

- How many readers support the page
- How consistent readers read the page
- How much redundant text is read

Test failure is determined based on:

- Whether browse mode navigation works
- Whether the entire page is read correctly
- Whether elements are read in the wrong order
- Whether widgets are read multiple times
- Whether widgets are not read at all
- Whether tab order is broken
- How confusing workarounds are

Approach 0: Multiple pages
--------------------------

Link: [Approach 0 HTML](approach0/start.html)

This is the baseline approach: Just static HTML, multiple pages, no
accessibility optimizations at all.

Intentions:

- The first page should be read is in its entirety
- Subsequent reads should skip the header
- Browse mode should be used to read subsequent text
- Tab should cycle through links
- Same page links should read somewhat the same as other pages

Problems:

- The 'page is loading' notifications is a bit distracting
- Orca reads first page in its entirety
- Orca cancels tabbing if the reader is reading the first page
- Narrator with Edge does not read the page automatically at all
- Narrator with Edge reads same page links
- Narrator with Firefox does not read the page automatically at all
- Narrator with Firefox adds 'Cap Y' or 'Cap T' to page title
- Narrator with Firefox reads the first word of same page links
- Narrator with Chrome always reads the entire page on load
- Narrator with Chrome doesn't read same page links
- NVDA skips text on anchor links items sometimes
- NVDA reads some items multiple times
- NVDA moves focus when reading links
- NVDA blocks link clicking if it's reading something else
- NVDA acts the same across browsers in this test

It looks like there's three different types of behaviour in this example:

- Visiting the start page
- Visiting another from with an anchor specified
- Visiting the start page from itself with an anchor specified

First insight: Screen readers will eat tabs or clicks during reading, probably
because it's a bad idea to let people navigate to things they haven't read yet.

Second insight: Screen readers might read things twice for unclear reasons.
Possibly related to focus.

Third insight: Screen readers will skip elements for unclear reasons. This is
possibly related to reporting where the current cursor is element-wise.

This is not a pass or fail, just a baseline approach to compare other solutions
to.

Approach 1: Multiple pages, no anchors
----------------------------------------

Link: [Approach 1 HTML](approach1/start.html)

This tries to solve the problem of inconsistent behaviour by only having one
link type: Go to a new page.

Intentions:

- Each page should be read is in its entirety
- Tab should cycle through links

Problems:

- The 'page is loading' notifications is a bit distracting
- Orca reads first page in its entirety
- Orca cancels tabbing if the reader is reading the first page
- Narrator with Edge does not read the page automatically at all
- Narrator with Firefox does not read the page automatically at all
- Narrator with Chrome always reads the entire page on load
- NVDA moves focus when reading links
- NVDA blocks link clicking if it's reading something else
- NVDA acts the same across browsers in this test

The main insight here is that by dropping anchor links a lot of the
inconsistency seen in screen readers disappears. More importantly, this
behaviour matches the regular behaviour people expect in a typical browse.

I would say this passes, however there is no way to easily update the elemnts
based on client side JavaScript.

Approach 2: Single page
-----------------------

Link: [Approach 2 HTML](approach2/start.html#start)

This approach uses anchors only on a single page. Because it's a static page
it includes all available options which isn't suitable for a dynamic
application, but I will ignore that for now.

Intentions:

- Only the first paragraph of text per section should be read
- Browse mode should be used to read subsequent text
- Tab should cycle through links

Problems:
- Orca with Firefox speaks 'Document web' after a link press
- Narrator with Firefox speaks the page title after a link press
- Narrator with Chrome doesn't speak the anchored text
- NVDA moves focus when reading links
- NVDA blocks link clicking if it's reading something else
- NVDA on Edge speaks the paragraph of text twice

This is certainly cutting down on the problems, but the page now suffers from
reading duplicate text in NVDA and no text on Narrator. 

I would consider this a fail due to text being read twice in NVDA.

TODO
----

approach 1:
- click button/link
- update content
- move to new content

operations 2:
- click button/link

- move to new content


- editing the DOM in place?
- links vs buttons

two pages:
- one has one button and 

buttons? links? anchors? .focus? tabindex=-1?
aria?

can't be that hard ... right?
aria-live doesn't work if text doesn't update!
