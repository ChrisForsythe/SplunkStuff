# Better dashboards

Just because you can dashboard doesn't make the dashboard useful.  Some random
notes here.

## Identify your panels
It is a good habit to be into to put an id attribute on your panels.  Why?
So you can apply specific stylings readily to them, as well as reference that
specific panel in documentation later.

    <panel id="somelabelhere" ...>

## Styling
Stylesheets are very valuable, and can be added to your app.  They can
also readily be used to set the below recommendations without having to
think about it.  Do so.  
When creating a stylesheet there are several things to keep in mind.

### Use variables

Variables allow you to use common items throughout your stylesheet.  If
multiple stylesheets are combined, you can even reference the varaible in
another stylesheet potentially (advanced trick).

Define variables by

```
:root {
  --bg1: #303030;
  --fg-link: #9696ff;
  --fg1: #dadada;
  --bg-highlight: 37378a;
  --highlight: var(--bg-highlight);
}
```

As suggested above, you use a variable by the syntax

    var(--variablenamehere)

Yes, variables should be defined in a **:root:** section and always start with
two dashes.  

### Unique IDs

So you followed the above advice and gave a unique ID to each panel, but want
common stylings for panels with the same naming pattern?  Let us assume you
named these common panels with the pattern mygraph-*foo*, mygraph-*bar*, mygraph-*baz*...

```
[class*='mygraph-'] {
    background-color: var(--bg-mygraph) !important;
}
```

Wildcards are your friend.
### Cloud

Splunk Cloud complicates things, because Stylesheets cannot be directly (yet)
uploaded via the GUI.  So, a good habit:  Work up your styles in a single
dashboard that does a giant hidden HTML to create all the styles in one
`<style>` section.  Once done, then create a private app that is hidden but
holds your CSS.  Now, your other dashboads can reference that CSS via

    <form stylesheet="myhiddenapp:mystylesheet.css">

If you want dark theme as your basis, you can combine the attributes:

    <form stylesheet="myhiddenapp:mystylesheet.css" theme="dark">

And work from there.

## Scrolling
Horizontal scrolling should not be required.  Do not assume your user has 
any larger than a 1080 display.

Vertical scrolling should be minimized.  No more than two screenfuls, again
assuming a standard 1080 display.  

If you have more than that, you are putting too much into a single dashboard
and you have lost your focus.  Drill downs into secondary dashboards are a good
thing, as are popup panels.

## Font size
Font sizes for labels should never be smaller than 10pt.  Not everyone has
great close up vision.

Font sizes for anything other than labels should never be smaller than 12pt,
same reason.  Yes, this may require you to override the default CSS of your
theme.  It's worthwhile.

    .highcharts-data-label text tspan {
        font-size: var(--labelfontsize);
    }

## Know your audience
Management doesn't want to click a drilldown to suddenly see a raw search.
They expect another, more focused dashboard, or a pop-up panel.  So give that
to them.  If they really want the raw search, that's what "open this panel in
search" is for.

## Document your dashboards
Dashboards should be documented.  There's several ways to do it (see
[DocumentDashboards.md](DocumentDashboards.md) for several), but another way is to have a link in
your dashboard to external documentation, such as a wiki link.

You may need two types of documentation, documentation for users, and
documentation for developers.

## Choose the right visualization

Not every visualization is well suited to every data type.

### pie charts

Pie charts should be used when measuring percentages of a whole, such that
all the slices are expected to be an actual percentage.  They are good at
showing visually the big slice.

### Gauges

Gauges (radial and vertical) are best if you have an idea in advance what 
the scale is.  What are the ranges that define your color scale?  Do not
rely on auto to define them, explicitly define your color ranges.  What
are the expected threshold limits?

## Choose sensible time

Your time selector (assuming you have one) should be set to something sane for
that dashboard

## Measure loading times

The dashboard should not be one that makes the user go get a cup of something
to drink while waiting for it to finish loading.  If it takes more than a few
seconds, you need to find a way to optimize your dashboard.  That may mean
writing results to a summary index or lookup table and loading that, or using
filters that accelerate your searches significantly.

If you use pop-up panels, measure how long it takes them to load as well, from
the time you click on the drilldown to bring them up.  

A simple trick to accelerate pop-up panels is to use a base search that creates
the base results and the token that brings up the pop-up panel just filters
the results based on the token.  But if that isn't enough, then maybe you
should be rethinking your query design, or thinking about summary data. 

### Concurrent search limits

A corrollary of loading time is that you should not have the typical user
encountering the dreaded "Waiting for queued jobs to start" warning.  If
your dashboard has so many searches that they have hit the limit on
concurrent searches, then you should simplify your dashboard.  You can
use base searches if there are several panels that rely on the same 
basic search to help reduce this issue.
