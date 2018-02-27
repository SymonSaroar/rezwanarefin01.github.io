---
published: true
title: Persistent Segment Tree (Part - 02)
layout: post
comments: true
author: RezwanArefin01
category: [Data Structure]
share-img: '/img/pseg/segment-02.png'
permalink: '/posts/persistent-segment-tree-02/'
---

[পূর্বের পর্বে]({{ site.baseurl }}/posts/persistent-segment-tree-01) Persistent Segment Tree এর Basic Idea নিয়ে বলেছিলাম। আজকে এর কিছু Basic Problem নিয়ে অলোচনা করব।
#### **Problem 1**
> You are given an array with $N$ elements. You need to do $Q$ queries in form - <br/>
`l r k`: Count how many numbers from index $l$ to $r$ in the array is less than $k$.

Problem টা সহজেই Merge Sort Tree দিয়ে $O(\log^2 n)$ Complexity তে সল্ভ করা যায়। কিন্তু আমরা Persistent Segment Tree দিয়ে $O(\log n)$ এ সল্ভ করতে পারি :sunglasses:। কেউ জিজ্ঞাসা করতে পারে দরকার কি $O(\log n)$ এ করার। কারণ, কিছু Problem এ হয়ত এই কাজ টা কোন Subprocess এ থাকতে পারে! যেমন এমন হতে পারে যে এইটার উপরে আরও ২টা লুপ ঘুরছে। (কয়েকদিন আগে Codeforces এর একটা Problem এ লাগছিল, আর কোন approach মাথায় না আসায় এই কাজ করতে হইছিল। যদিও ভাল solution ছিল অন্যরকম। )

যাই হোক, এই প্রবলেমটা সল্ভ করার আগে একটা সহজ প্রবলেম সল্ভ করা যাক। মনে করি আমাদের সমস্ত Query এর Range $\[1, n\]$, তাহলে আমরা কি করতে পারি?    

একটা উপায় হল আমরা একটা `cnt[]` array নেব। আর array এর সব নাম্বার কোনটা কয়বার আছে সেইটা Count করব (`cnt[x]` এ থাকবে $x$ array তে কয়বার আছে)।    

এখন যদি জিজ্ঞাসা করা হয় Array তে কয়টা Number $k$ থেকে ছোট, তাহলে এর উত্তর হবে - $\sum_{i = 0}^{k-1} cnt\[i\]$। মানে $0$ থেকে $k-1$ পর্যন্ত সব গুলা নাম্বার এর Count এর যোগফল। এইটা সহজেই Prefix Sum / Binary Indexed Tree দিয়ে করা যায়।  

এখন আমাদের আসল প্রবলেম এ আসা যাক। $\[l, r\]$ range এ কয়টা নাম্বার $k$ থেকে ছোট।

যদি Cumulative Sum কেন Use করি আমরা সেইটা মনে থাকে তাহলে এইটা সহজেই বলা যায় যে -
> $f(x) = \[1, x\]$ range এ $k$ থেকে ছোট সংখ্যার পরিমাণ হলে, $\[l, r\]$ range এ $k$ থেকে ছোট সংখ্যার পরিমাণ $= f(r) - f(l-1)$ :smiley:  

এখন আমরা $f(x)$ কিভাবে দ্রুত বের করতে পারি? আমাদের `cnt[]` array তে তো সব নাম্বার এর Count নিয়ে ফেলছি। আমরা এমন একটা `cnt[]` array চাই যাতে আমরা শুধুমাত্র `a[1..x]` - এ কোন নাম্বার কয়বার আছে সেইটার Count পাব। তাহলে কি করা যায়?  

একটা কাজ করতে পারি আমরা। প্রত্যেকটা Prefix range এর জন্য একটা করে Segment Tree নিতে পারি। যেমনঃ- $\[1, x\]$ range এর জন্য একটা Segment Tree নিলাম আর তার মধ্যে এই নাম্বার গুলার index কে $+1$ দিয়ে Update করে দিলাম। [মানে $Update(a[i], +1)$]

তাহলে আমরা যদি $\[1, x\]$ range এ কয়টা নাম্বার $k$ থেকে ছোট বের করতে চাই সেইটা আমরা $\[1, x\]$ range এর Segment Tree এর মধ্যে $Query(1, k-1)$ থেকে জানতে পারব।

কিন্তু এইভাবে করলে আমাদের $N$ টা Segment Tree লেগে যাবে। $O(n^2)$ Memory + Time লাগবে। তাহলে কি করা যায়?    

এইখানে আসে Persistent Segment Tree। আমাদের $[1, x]$ range এর Segment Tree আর $[1, x-1]$ range এর Segment Tree এর মধ্যে কি খুব বেশি তফাৎ আছে?

মোটেও না! $\[1, x\]$ range এর Segment Tree টা আসলে পুরাই $\[1, x-1\]$ এর Segment Tree এর মত, শুধু পার্থক্য কি? এইটাতে একটা $Update(a[x], +1)$ বেশি করা হয়েছে!

তাহলে আমরা Persistent Segment Tree ব্যবহার করতে পারি। $\[1, x-1\]$ এর Version এর মধ্যে খালি আরেকটা Update করেই আমরা $\[1, x\]$ range এর জন্য Segment Tree বানাতে পারি।

সমাধান শেষ।
এইটার Implementation করা সোজাই। আশাকরি সবাই করতে পারব। পরবর্তি প্রবলেম এ এই Trick টা কাজে লাগবে।

#### **Problem 2**
> You are given an array with $N$ distinct elements. You need to do $Q$ queries in form - <br/>
`l r k`: What will be the $k^{th}$ number if elements from index $l$ to $r$ was sorted?

একটা আমার Favourite প্রবলেম গুলার মধ্যে অন্যতম। এই প্রবলেম এর $O(\log^3 n)$, $O(\log^2 n)$, $O(\log n)$ Per Query Solution (Online) আছে, আবার $O(n \sqrt{n} \log n)$ (Offline) ও আছে। :blush:

খেয়াল করি, $k^{th}$ নাম্বার এর কি বিশিষ্ট আছে? $k^{th}$ নাম্বার হল এমন একটা নাম্বার যার থেকে $(k-1)$ টা ছোট নাম্বার থাকবে। কিন্তু এইটা সব সময় সত্যি না। যেমন নাম্বার গুলা হল - $1,5,8,10,11,15$ এর মধ্যে $6^{th}$ নাম্বার হল $15$, আর $15$ থেকে ছোট নাম্বার আছে $5$ টা এখানে। কিন্তু এইখানে $12,13,14$ এই নাম্বার গুলাকেও তো তাহলে আমরা $6^{th}$ নাম্বার বলে ফেলব! কারণ এইগুলার থেকেও ছোট নাম্বার আছে $5$ টা। কিন্তু আসলে এইগুলা Array তেই নেই।

লক্ষ্য করি, যদি আমরা $15$ কে $6^{th}$ নাম্বার বলি এইটার ভিত্তিতে যে "$15$ থেকে ছোট সংখ্যা আছে $5$ টা" তাহলে আমাদের আরেকটা শর্ত লাগবে - "$15$ থেকে বড় কোন সংখ্যা $x$ নাই যাতে $x$ থেকে ছোট সংখ্যা থাকে $5$ টা"।  কারণ $x > 15$ হলে আর যদি $15$ Array তে থাকে তাহলে $x$ থেকে ছোট সংখ্যার পরিমাণ $1$ বেড়ে যাবে। তাই আমরা যদি $12, 13, 14$ কে $6^{th}$ নাম্বার বলি তাহলে সেইটা ভুল হবে। এগুলো আসলে Array তে নাই, এই জন্য $x$ কে বাড়ালেও $x$ থেকে ছোট সংখ্যার পরিমাণ বাড়ে না।

তাহলে আমাদের $k^{th}$ নাম্বার এর সংজ্ঞা কি হল?  
> সবচেয়ে বড় এমন সংখ্যা $x$, যেন $x$ থেকে ছোট সংখ্যা থাকে $k-1$ টা।

তাহলে আমরা $x$ এর উপরে Binary Search করে সহজেই এইটা Solve করতে পারি। কিন্তু সংখ্যা গুলা Distinct হতে হবে এই Appraoch কাজ করাতে হলে। Distinct না হলে আরও কিছু শর্ত লাগবে।

$\[l, r\]$ range এ $x$ থেকে ছোট সংখ্যা কয়টা আছে, এইটা যদি আমরা Merge Sort Tree দিয়ে করি তাহলে Complexity হবে $O(\log^3 n)$ আর Persistent Segment Tree based approach ব্যবহার করলে Total Complexity হবে $O(\log^2 n)$ per query.

এইবার আমরা চেষ্টা করব এই Solution টাকে আরও ভাল করার জন্য। $O(\log n)$ per query solution এর জন্য।   

আমরা আসলে Binary Search এ Exact কি করছি?  

মনে করি আমাদের Query range $[1, 5]$ আমাদের নাম্বার আছে - $\[1, 5, 7, 10, 3\]$. তাহলে আমাদের $[1, 5]$ range এর Segment Tree টা হবে Array এর উপরে - $\[1,0,1,0,1,0,1,0,0,1\]$ [মানে $cnt[]$ array আরকি, আগের Approach এ যেইভাবে করছিলাম]   

তাহলে এর $k^{th}$ নাম্বার বের করার জন্য আমরা কি করছি? আমরা একটা Position $x$ এর উপরে Binary Search করছি, যাতে $\[0, x-1\]$ range এর Sum $k-1$ হয়। বা সোজা কথায় বললে বলা যায় $k^{th}$ $1$ খুঁজছি আমরা।   

তাহলে আমাদের $k^{th}$ নাম্বার এর সংজ্ঞা পরিবর্তন করি -
> `cnt[]` array এর সবচেয়ে বামের এমন একটা Index, যেন তার আগের সব সংখ্যার যোগফল $k$ হয়। ওই Index-ই আমাদের Answer.

লক্ষ্য করি, আমাদের Segment Tree এর Query Part টা কি অনেকটা Binary Search এর মতই না? আমরা একটা Range কে আগে ২ভাগে ভাগ করি, ডান বা বাম এ যাই।   

আমরা কিন্তু আলাদা ভাবে এই Binary Search না করে Segment Tree এর Query এর সাথে Merge করে ফেলতে পারি আমাদের Binary Search Process টাকে!

আমরা কোন Node এ গিয়ে দেখব এর Left Subtree তে সব সমখ্যার Sum $k$ থেকে বড় নাকি। যদি $k$ থেকে বড় হয় তাহলে আমরা ওই দিকেই যাব। কারণ আমরা এমন একটা Leftmost Index খুঁজছি যাতে $\[1, x\]$ এর Sum $k$ হয়। তাই যদি Left Subtree তেই $k$ থেকে বেশি সংখ্যা থাকে তাহলে আমরা সেইদিকেই যাব। আর যদি Left Subtree তে $k$ থেকে কম সংখ্যা থাকে তাহলে আমাদের Index টা Right Subtree তে আছে। আমরা Right Subtree এর Range এর ($k - $ Left Subtree এর মোট সংখ্যা)-তম সংখ্যা Return করব। মানে আমরা Left Subtree তে যেই সংখ্যাগুলা আছে সেইগুলা নিয়ে নিলাম। আর Right Subtree থেকে আর যতগুলা নিলে আমাদের $k$ হয় তত গুলা নাম্বার নিতে হবে।

Pseudo Code হতে পারে এমন -

```cpp
query(node, l, r, k) {
  if l == r: return l; // This is our desired index
  if left.count >= k:
    return quert(left, l, mid, k);
  else return query(right, mid+1, r, k - left.count);
}
```

কিন্তু এইটা তো গেল যদি আমাদের $[1, x]$ range এর query থাকে। আমাদের সব Prefix Range এর জন্য Segment Tree আছে তাই Query করতে পারলাম। কিন্তু যেকোনো $\[l, r\]$ range এর জন্য কিভাবে কি করব?    

আগের উদাহরণটাই নেওয়া যাক -
আমাদের নাম্বার আছে - $\[1, 5, 7, 10, 3\]$. তাহলে আমাদের $[1, 5]$ range এর Segment Tree টা হবে Array এর উপরে - $\[1,0,1,0,1,0,1,0,0,1\]$    

কিন্তু, $\[3, 5\]$ range এর জন্য আমাদের একটা Segment Tree লাগবে যেইটা খালি এই range এর নাম্বার এর উপরে বানান। মানে $\[0,0,1,0,0,0,1,0,0,1\]$ এর উপরে।

আগের Problem করার সময় আমরা শুধু $f(r) - f(l-1)$ করেই এই সমস্যা সমাধান করছিলাম। কিন্তু এইখান কিভাবে করব? এখন তো আমাদের query করতে গেলে পুরা Segment Tree লাগবে।

লক্ষ্য করি। আসলে এখন আমাদের Segment Tree এর Leaf গুলা = $\[1, r\]$ range এর Leaf $- \[1, l-1\]$ range এর Leaf।

যেমন উপরের উদাহরণ এ আমাদের $\[1, 2\]$ range এর Segment Tree টা হবে এই Array এর উপরে - $\[1,0,0,0,1,0,0,0,0,0\]$  

তাহলে $\[3, 5\]$ range এর Segment Tree এর Leaf গুলা হবে

$\[1, 5\]$ range এর Leaf $-$ $\[1, 2\]$ range এর Leaf -
$$\{1,0,1,0,1,0,1,0,0,1\} -\{1,0,0,0,1,0,0,0,0,0\} = \{0,0,1,0,0,0,1,0,0,1\}$$

আমরা Leaf নাহয় এইভাবে পেলাম, কিন্তু Segment Tree এর Node গুলার জন্য কি করব?  

আসলে লক্ষ্য করলে দেখা যায় যে যেকোনো Node এর জন্যই এইটা সত্য! মানে $\[1, r\]$ range এর Segment Tree এর $\[x, y\]$ range এর Node এর Sum থেকে $\[1, l-1\]$ range এর Segment Tree এর $\[x, y\]$ range এর Sum বাদ দিলেই আমরা $\[l, r\]$ range এর Segment Tree এর $\[x, y\]$ range এর Sum পেয়ে যাব। (বুঝতে সমস্যা হলে আরেকবার পড়ুন, অথবা খাতায় এঁকে দেখুন)

এইভাবেই আমরা $\[l, r\]$ range এর নাম্বার গুলার জন্য একটা Segment Tree পেয়ে যাব। এখন আমরা উপরে যেইভাবে Binary Search করলাম Segment Tree এর Query করার সাথে সাথে সেইটা করতে পারি। আসলে কিন্তু আমাদের Segment Tree টা Build করার দরকার নেই। আমরা একসাথে ২টা Segment Tree থেকে Traverse করা শুরু করর। যখন আমরা $\[l, r\]$ range এর জন্য কোন Node এর Sum জানতে চাইব তখন ওই ২টা Segment Tree এর Node এর Sum এর Difference নিয়েই সেইটা বের করে নিতে পারব :slightly_smiling_face:  

এখন এই Solution এর Complexity $O(\log n)$

#### **Implementation**
Implementation না দেখার অনুরোধ রইল। নিজে চেষ্টা করলেই সম্ভব এইটা Code করা আমি মনে করি। তার পরেও না পারলে Code দেখা যেতে পারে।

```cpp
int search(node *a, node *b, int l, int r, int k) {
  if(l == r) return l;
  int cnt = a -> left -> val - b -> left -> val;
  // Find sum of left subtree. But this time we don't actually have a
  // Segment Tree Node for that range. But we have 2 segment tree.
  // and we know that the segemnt tree we want has each node value =
  // difference of other two segment tree nodes for same range.

  int mid = l + r >> 1;
  if(cnt >= k) // Our answer in in left subtree
    return search(a -> left, b -> left, l, mid, k);
  else return search(a -> right, b -> right, mid+1, r, k - cnt);
}
int kthnum(int l, int r, int k) { // Just to make things simple
  return search(root[r], root[l-1], 0, n-1, k);
}
```

Implement করা হলে এইখানে Submit দিয়ে দেখা যেতে পারে - [MKTHNUM](http://www.spoj.com/problems/MKTHNUM/)
যে ২টা Problem অলোচনা করলাম ২টাই এই একটা Problem এ Submit করে দেখা যাবে। একবার আগের Binary Search + $\[l, r\]$ range এ $k$ থেকে ছোট নাম্বার Count করার মাধ্যমে আরেকবার এই Approach এ করে Submit দিয়ে দেখতে পারেন। :slightly_smiling_face:

তবে এই Approach এ আমরা `cnt[]`array এর উপরে Segment Tree রেখেছি। কিন্তু এই প্রবলেমএ নাম্বার গুলার Range অনেক বড়। তাই আগে নাম্বার গুলাকে Compress করে নিতে হবে। এর পরে $k^{th}$ নাম্বার বের করে আবার Decompress করে দিতে হবে। Compress করা মানে হল প্রত্যেক নাম্বার কে তার Rank (Array Sorted হলে ওই নাম্বার এর Position) দিয়ে পরিবর্তন করে দেওয়া। Compress করার পরে প্রত্যেক নাম্বার $[1, n]$ range এর মধ্যে হবে। তখন এই Approach ব্যবহার করা যাবে।

আগামী পর্বে কিছু Advanced Problem নিয়ে অলোচনা করার চেষ্টা করব। ধন্যবাদ সবাইকে। :slightly_smiling_face:
