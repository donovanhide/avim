---
layout: post
title: "Unordered map with custom structures"
date: 2011-11-06 18:27
comments: true
categories: [C++,Code]
---

I encountered the problem of trying to put custom data structures into the key of an unordered map and found different answers all over the Internet. The same question came up on the sparsehash [mailing list](http://groups.google.com/group/google-sparsehash/browse_frm/thread/891fd35c00363ac1) so I thought I'd put an example up here:

{% gist 1343184 vector_map.cpp %}

The trick is to define both a hash and an equality function for the custom data structure. The prime numbers in the 3D vector hash function come from this [paper](http://www.beosil.com/download/CollisionDetectionHashing_VMV03.pdf). The "correct" hash function depends a lot on the distribution of the keys and only real world benchmarking will give a satisfactory answer as to what works best for your data.

The output of the above code would be something like:

{% gist 1343184 output %}

