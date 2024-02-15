goal: change page contents and go back to a section
can't be that hard ... right?
aria-live doesn't work if text doesn't update!

be very clear about test targets:
- read things in order
- don't read things twice
- ideally read the entire page
- tab should take you to the first button
- should read the page each time
- tab and arrows should work

orca+firefox/chromium
nvda+edge,nvda+chromium,nvda+firefox
narrator+edge,narrator+chromium,narrator+firefox

approach 0 (baseline):
- static html
- click button/link
- go directly to content

approach 1:
- click button/link
- update content
- move to new content

operations 2:
- click button/link
- update content
- move to new content


- editing the DOM in place?
- links vs buttons

two pages:
- one has one button and 

buttons? links? anchors? .focus? tabindex=-1?
aria?
