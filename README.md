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
--------------------------------------

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
- NVDA with Edge does not read the page automatically at all
- NVDA with Firefox does not read the page automatically at all
- NVDA with Firefox skips text sometimes
- NVDA moves focus when reading links
- NVDA blocks link clicking if it's reading something else

The main insight here is that by dropping anchor links a lot of the
inconsistency seen in screen readers disappears. More importantly, this
behaviour matches the regular behaviour people expect in a typical browse.

I wouldn't say this passes as it skips text.

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
- Orca cancels tabbing if the reader is reading the first page
- Orca with Firefox speaks 'Document web' after a link press
- Narrator with Firefox speaks the page title after a link press
- Narrator with Chrome doesn't speak the anchored text
- NVDA moves focus when reading links
- NVDA blocks link clicking if it's reading something else
- NVDA on Edge speaks the paragraph of text twice

This is certainly cutting down on the problems, but the page now suffers from
reading duplicate text in NVDA and no text on Narrator. 

I would consider this a fail due to text being read twice in NVDA.

Approach 3: Single page, buttons
--------------------------------

Link: [Approach 3 HTML](approach3/start.html#start)

Elements that interactively change state shouldn't be links but instead
buttons. This approach is the same as approach 2 but using JavaScript to set
the window location.

Intentions:

- It should run navigate the same as approach 2

This works as expected. Setting the window location instead of an anchor gives
identical behaviour with these screen readers.

Approach 4: Focusing paragraphs
-------------------------------

Link: [Approach 4 HTML](approach4/start.html)

Elements that interactively change state shouldn't be links but instead
buttons. This approach is the same as approach 2 but using JavaScript to set
the window location.

Intentions:

- It should reliably speak the paragraph

Problems:

- Orca cancels tabbing if the reader is reading the first page
- Orca with Chromium reads the paragraph twice
- Orca with Chromium adds 'clickable' after the paragraph
- Narrator with Edge adds 'grou' after a paragraph is read
- Narrator with Firefox doesn't read the paragraph text
- NVDA moves focus when reading links
- NVDA blocks link clicking if it's reading something else

This solution has the least amount of quirks so far, but still unacceptable
as it reads text twice, not at all and announces a false clickable.

Approach 5: Focusing paragraphs, empty
--------------------------------------

Link: [Approach 5 HTML](approach5/start.html)

Decoupling focusing of the page with reading the text could help turn this
in to two issues rather than one. Though having focus with no notification is
arguably a bad idea, it's worth testing to see if it works.

Intentions:

- It should silently move the focus to above the text
- The text should be readable using line navigation

Problems:

- Orca cancels tabbing if the reader is reading the first page
- Orca with Chromium says 'clickable'
- Narrator with Edge says 'grou'
- Narrator with Chrome says 'text'
- Narrator with Firefox says 'text'
- NVDA moves focus when reading links
- NVDA blocks link clicking if it's reading something else
- NVDA reads 'blank'
- NVDA skips the next line of text in line navigation

This solution on its own is unusable, but maybe with reading other text it
would be workable. It is problematic that NVDA skips the next line of text.

TODO
----

More approaches to try:

- Approach 6: IFrames
- Approach 7: Updating content in place with anchor buttons
- Approach 8: Updating content in a specific order
- Approach 9: Buffering content updates
- Approach 10: Empty paragraphs and ARIA live regions
- Approach 11: ARIA region repeats
- Approach 12: ARIA focus grabbing

These don't work I just wanted to document them.
