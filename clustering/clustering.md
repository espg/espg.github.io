---
title: Clustering
---

I tend to focus on clustering algorithms that use distance and density.

## OPTICS

OPTICS, or 'Ordering Points To Identify Clustering Structure', is a
density based clustering algorithm that I developed for segmentation of
LiDAR points. It was also [my first pull request](https://github.com/scikit-learn/scikit-learn/pull/1984/).

My implementation of the OPTICS algorithm was [merged](https://github.com/scikit-learn/scikit-learn/pull/11547)
into sklearn for [release 0.21](https://scikit-learn.org/stable/whats_new/v0.21.html#version-0-21-1),
and is available in all current versions. You can find the module documentation [here](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.OPTICS.html), with more
[descriptive and narrative documentation here](https://scikit-learn.org/stable/modules/clustering.html#optics);
there's also [demo code](https://scikit-learn.org/stable/auto_examples/cluster/plot_optics.html#sphx-glr-auto-examples-cluster-plot-optics-py).

## Posts in this section

```{blog-posts}
:path: {ai,clustering,remotesensing,hacks,reflections,posts}/*.{md,ipynb}
:category: Clustering
:limit: 10
:sort: date-desc
:excerpts: true
```
