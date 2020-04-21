---
layout: post
title: Why databases use B+ tree for indexing? - Part II
published: false
---

## Why databases use B+ tree for indexing? - Part II

In the previous article we have seen/shortlisted three data structures that can be used for indexing purpose. If you haven't seen my previous post, you can find it [here](https://rajatgl17.github.io/blog/2020/04/19/why-b-tree-index-part-1.html).

Let us do a recap, before we begin.
| S.No. | Data Structure | Insert | Delete | Get at index | Search | Space usage |
| ----- | ------ | ------------ | ------------ | ------------ | ------------ | ------------ |
| 1 | Binary search tree (Self-balancing)| O(log n) | O(log n) | N/A | O(log n) | O(n) |
| 2 | Hash table | O(1) | O(1) | N/A | O(1) | O(n) |
| 3 | B-tree | O(log n) | O(log n) | N/A | O(log n) | O(n) |

For the sake of simplicity lets club Binary search tree and B-tree as tree, and compare tree with hash table initially.
