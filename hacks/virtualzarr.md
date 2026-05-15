---
title: Embedding zarr in myst
date: 2026-05-15
authors:
  - Shane Grigsby
tags: [myst, zarr, virtualization]
category: Hacks
location: Emeryville, CA
excerpt: 2
---

## Background

As part of my time at [Englacial](https://englacial.org), we virtualized the
Ice Sheet Model Intercomparison Project model output from the CMIP6; i.e.,
ISMIP6. You can see the virtual store here on
[source.coop](https://source.coop/englacial/ismip6), and we also built
an [interactive viewer](https://englacial.org/static/models/), which runs
client side in the browser.

One of the more common things I find myself wanting to do, is to reference
data inline for content that I produce and post. Obviously, jupyter and myst
are great for this-- we can build a notebook, call python to plot something,
and then render the output as a static page. But what if I'm primarily
*writing* and not *coding*... it would be great to be able to render data
directly in markdown.

## A myst directive for (virtual) zarr arrays

Typically, the data that I'd want to reference in a web post already exists.
It's been calculated and processed elsewhere, and is just an array that I want
to display. I took the core of the viewer above, and turned it into a generic
myst directive, which enables calling the pane in markdown:

```{ismip6-viewer}
:store_url: https://data.source.coop/englacial/ismip6/icechunk-ais/
:model: DOE_MALI
:experiment: ctrl_proj_std
:variable: lithk
:controls: time
```

(code)
````markdown
```{ismip6-viewer}
:store_url: https://data.source.coop/englacial/ismip6/icechunk-ais/
:model: DOE_MALI
:experiment: ctrl_proj_std
:variable: lithk
:controls: time
```
````

## Caveats and notes

This is still fairly recent so there are some limitations. Right now, this is
rendered as an iframe embed of another deployed html page. This is because
there's not a clean way to bring in scripts via directive, so it's an external
load... once [this PR for static files](https://github.com/jupyter-book/mystmd/pull/2770)
is merged, we can reference locally. We also share context across viewer
panes, so it's possible to embed multiple panes, but only from the same
virtual store-- this is an easy fix to update though.

The biggest restriction is on Cross-origin resource sharing (CORS), which need
to be set at the host bucket level of the data to have things work. These are
the correct settings:

  - Allowed methods: `GET`, `HEAD`
  - Allowed origins: `*` (any)
  - Allowed headers: `*`
  - Exposed headers: `Content-Length`, `Content-Range`, `Content-Type`, `ETag`, `Accept-Ranges`

These are default now on source.coop.
  
