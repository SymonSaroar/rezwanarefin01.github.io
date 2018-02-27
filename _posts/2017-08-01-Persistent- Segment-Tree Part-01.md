---
published: true
title: Persistent Segment Tree (Part - 01)
layout: post
comments: true
author: RezwanArefin01
category: [Data Structure]
share-img: '/img/pseg/segment-02.png'
permalink: '/posts/persistent-segment-tree-01/'
---
এই Tutorial এ Persistent Segment Tree এর কিছু Basic Idea নিয়ে আলোচনা করব।   
#### **Prerequisites**
+ Segment Tree সম্পর্কে ভাল Idea থাকা।
+ Segment Tree এর অন্তত ২০~৩০টি প্রব্লেম সল্ভ করা।
+ C/C++ Pointer এবং Reference আর Dynamic Memory Allocation সম্পর্কে ভাল ধারনা থাকা  তবে খুব বেশি জানার দরকার নাই।
+ এই পোষ্ট সম্পুর্ণ বুঝে পড়ার ধৈর্য থাকা।

#### **Persistency কি জিনিস?**
> কোন Data Structure যখন বিভিন্ন State এ তার অবস্থা মনে রাখতে পারে, তখন থাকে Persistent Data Structure বলে।

যেমন এই প্রব্লেম টা দেখা যাক -
> You are given an initial array with $N$ elements. You need to do $Q$ queries of two types -
- `x, v`: Add $v$ to first $x$ elements of the array.
- Print the array as it was after $i^{th}$ update.

Constrains: $N,Q \leq 10^5$, কিন্তু $\sum x \leq 1e5$.   
একটা Bruteforce Solution হতে পারে, একটা Update এর পরে আগের Version থেকে নতুন করে একটা Version Create করা, তার প্রথম $x$টা element update করা।  
কিন্তু এইভাবে করলে সমস্যা কি? আমাদের $Q$ টা Array লাগবে, $N$ Size এর। মোট $O(NQ)$ Memory লেগে যাবে। তাহলে Memory Limit Excceed হবে বুঝাই যাচ্ছে। $O(10^{10})$ টা $32$-bit `int` এর জন্য $40$-GB Memory লাগবে।

তাহলে কি আমরা $\sum x \leq 10^5$ কে কাজে লাগাতে পারি?
#### **Observation**
মনে করি শুরুতে আমাদের Array = $\[1, 2, 3, 4, 5, 6, 7, 8\]$.  
Update গুলা হল -
+ $6, 1 \implies \[2, 3, 4, 5, 6, 7, 7, 8\]$
+ $3, 1 \implies \[3, 4, 5, 5, 6, 7, 7, 8\]$
+ $7, 1 \implies \[4, 5, 6, 6, 7, 8, 8, 8\]$

আসলে আমাদের কি বারবার সম্পুর্ণ Array Create করার দরকার আছে? যদি কোনভাবে শুধু প্রথম $x$ টা Element Update করে বাকিগুলা Skip করা যায় তাহলেই হত।    
কিন্তু কিভাবে? আমরা Linked List ব্যবহার করতে পারি!

আসলে আমরা $Q$ টা Linked List ব্যবহার করব। শুরুতে Initial Array এর জন্য Linked List বানাই। আর `head[]` নামের একটা Array নেব। যেইটাতে প্রত্যেক Version এর Linked List এর শুরুর Node এর Pointer থাকবে।

মনে করি প্রথম Linked List টা এমন হল -     
![Linked List Picture 01](/img/pseg/linked-list-01.png)

এখন প্রথম $6$ টা Element কে Update করতে হলে আমাদের খালি $6$ টা নতুন Node Create করলেই হবে। কিভাবে?     
![Linked List Picture 02](/img/pseg/linked-list-02.png)

এর পরে আবার প্রথম $3$ টা Update করলে শুধু $3$ টা নতুন Node Create করলেই হবে, এইরকম -    
![Linked List Picture 03](/img/pseg/linked-list-03.png)

অর্থাৎ, আমরা শুরুর $x$ টা নতুন Element এর জন্য নতুন Node Create করব। আর $x^{th}$ element এর সাথে এর আগের version এর $(x+1)^{th}$ element এর Link করে দেব   
এখন যদি বলে $2^{nd}$ Update এর পরের Array Print করতে, তাহলে `head[2]` থেকে Traverse করে Number গুলা Print করলেই হবে।   

লাস্ট Update এ $7$ টা Node Update করতে বলছে। তাহলে আগের Version থেকে কিভাবে করব?

লক্ষ্য করি - `head[2]` এর Element কিন্তু শুধু $\[3, 4, 5\]$ না, `head[2]` থেকে Traverse করলে আমরা এইরকম পাব -      
![Linked List Picture 04](/img/pseg/linked-list-04.png)

এর $x$ টা Element Update মানে আসলে আগের Version বা `head[2]` এর First $x$ টা Element Update করে নতুন Node Create করা, আর নতুন Version এর $x^{th}$ element কে আগের Version (`head[2]`) এর $(x+1)^{th}$ এর সাথে Link করা -       
![Linked List Picture 05](/img/pseg/linked-list-05.png)

**Memory Complexity Analysis**   
প্রথমে আমদের $O(n)$ টা Node প্রয়োজন হবে। আর প্রত্যেক Update এ নতুন $x$ টা Node Create হবে। তাই Memory Complexity $O(n + \sum x)$.

এই হল Persistent Data Structure এর "Basic"। ভাল করে বোঝার জন্য এইটার কোড লিখে ফেলি। নিজে অন্তত ৩০ মিনিট চেষ্টা করি।

#### **Persistent Segment Tree**
উপরে এতক্ষণ যেইটা আলোচনা করলাম সেইটাকে বলা যায় Persistent Linked List. আচ্ছা একটা Linked List আর Segment Tree এর মধ্যে পার্থক্য কই?
- Linked List এর প্রত্যেক Node এর সাথে একটা Node Conected থাকে, কিন্তু Segment Tree এর প্রতি Node এর সাথে Left Child আর Right Child থাকে।
- Linked List এর Node গুলাতে কোন static value থাকে, কিন্তু Segment Tree এর Node গুলাতে ওই Range এর Element গুলার উপরে কোন Associative Function $[f(a, f(b, c)) = f(f(a, b), c)]$থাকে।

আমরা আসলে Persistent Segment Tree দিয়ে কি করতে চাচ্ছি? আমরা Segment Tree তে যখন Update করব, তখন যেন আগের Version টাও Memorize হয়ে থাকে! যদি পুরা Segment Tree আবার Build করে আরেকটা নতুন Version এর জন্য তাহলে $O(NQ)$ Memory লাগবে, একই সাথে $O(NQ)$ Time ও লাগবে সব গুলা Update করতে!

#### **Observation**
মনে করি, আমাদের শুরুতে একটা Array আছে - $A[] = \[1, 1, 5, 2, 6, 1, 3, 5\]$, এর উপরে আমরা একটা Range Sum Segment Tree চাই। এর পরে Index $5$ এ $+4$ Update করব। কিন্তু আমাদের আগের Version এর Segment Tree কেও Memorize করে রাখতে হবে! তাহলে আমরা একটা নতুন Segment Tree Create করে তার মধ্যে Update করতে পারি! এইরকম হবে -     
![Segtree 01](/img/pseg/segment-01.png)

লক্ষ্য করি, আমরা যদি Segment Tree তে একটা Index Update করি, তাহলে মোট $O(\log n)$ টা Node Change হয়। যেমন উপরের চিত্রে মাত্র $4$ টা Node Change হয়েছে। কিন্তু আগের Version কে Memorize করতে গেলে আমাদের পুরা $O(2N)$ টা Node Change/Create করতে হল! কোনভাবে কি এই Extra Node গুলাকে Skip করা যায়?

এইখানেই আসে Persistent Segment Tree! Segment Tree কে সাধারণত আমরা যেইভাবে Build করি সেইভাবে না করে যদি Linked List এর মত করে করি? Linked List এ আমরা একটা next pointer রাখি, কিন্তু Segment Tree এর জন্য আমাদের লাগবে left pointer আর right pointer।

যখন আমরা নতুন Version Create করলাম, উপরের ছবি তে, $[0, 7]$ Node থেকে আমরা খালি Right Subtree তে গেছি, কিন্তু Left Subtree তে যাইনি। তাহলে কি আমরা আগের Version এর $[0, 7]$ Node এর Left Child কে নতুন Version এর $[0, 7]$ Node এর Left Child হিসাবে ব্যবহার করতে পারি না? আর যেই Subtree তে আমরা যাব সেই Subtree এর জন্য একটা নতুন Node Create করব! এভাবে করলে মাত্র $O(\log n)$ টা Node Create করলেই হবে।  

এইটা Visualize করা যেতে পারে এইভাবে -   
![Segtree 02](/img/pseg/segment-02.png)

Dashed Edge পুরানো Version এর কোন Node এর সাথে Linked, আর non-dashed গুলা নতুন।    

Persistent Linked List এর মত এইখানেও যদি আমরা এখন `Version[1]` থেকে Traverse করা শুরু করি তাহলে পুরা একটা আলাদা Segment Tree পাব বলে মনে হবে! আবার `Version[0]` থেকে Traverse করলেও একটা পুরা Segment Tree পাব! কিন্তু আমাদের নতুন Node লাগল মাত্র $O(\logn)$ টা।    
![Segtree 03](/img/pseg/segment-03.png)

এই হয়ে গেলও Persistent Segment Tree. :slightly_smiling_face:

**Complexity Analysis**   
শুরুতে আমাদের Segment Tree তে Node থাকে $O(N)$ টা। আর প্রতি Update এ নতুন Node Create হয় $O(\log n)$ টা। তাহলে $Q$ টা Update হলে মোট Node $O(Q\; \log n)$ টা। এইটাই Memory Complexity.  

আগের Approach এ আমাদের $O(NQ)$ Time Complexity ছিল! কিন্তু এখন খালি $O(\log n)$ টা Node Create করলেই হচ্ছে। তাই Time Complexity ও $Q$ টা Query তে মোট $O(Q\; \log n)$.


#### **Real Part - Implementation!**  
আমরা অনেকটা Linked List এর মত করে Persistent Segment Tree Implement করব। তাহলে Node গুলাকে শুধু প্রয়োজনের সময় Dynamically Allocate করে নেওয়া যাবে। শুরুতে দরকার আমাদের Node কে Represent করার জন্য একটা class / struct। এর মধ্যে থাকবে Left Child আর Right Child এর Node এর Pointer, আর ওই Node এর Range এর Sum বা অন্য কিছু রাখার জন্য কোন Variable। এইভাবে লেখা যায় -

```cpp
struct node{
  node *left, *right;
  int val;
  node(int a = 0, node *b = NULL, node *c = NULL) :
    val(a), left(b), right(c) {} // ** Constructor
};
```

শুরুতে আমাদের $\[0, n-1\]$ range এর জন্য যে যে Node লাগে সেইগুলাকে Initialize করতে হবে। মানে পুরা Range এর জন্য আমাদের Segment Tree তে যেই Node গুলা লাগে সেইগুলার Link গুলা Initialize করতে হবে। কিভাবে করা যায়?    

শুরুতে আমরা Root Node নিয়ে শুরু করব। আর সম্পুর্ণ Range কে ২ ভাগে ভাগ করব। আর যেই Node এ আছি সেই Node এর Left / Right Child এর জন্য একটা নতুন Node Create করব। এখন আমরা Left Node কে $\[0, mid\]$ আর Right Node কে $\[mid+1, r\]$ Range এর জন্য একটা Segment Tree হিসাবে কল্পনা করতে পারি। এদেরকে Recurrsively build করলেই হবে   


```cpp
struct node{
  ..........
  void build(int l, int r) {  // We are not  initializing values for now.
    if(l == r) return; // We reached leaf node, No need more links
    left = new node(); // Create new node for Left child
    right = new node();// We are creating nodes only when necessary!
    int mid = l + r >> 1;
    left -> build(l, mid);
    right -> build(mid+1, r);
  }
};    
// Note that this build function only creates the initial nodes
// and links between the nodes of segment tree.
```

আমাদের Root Node কয়টা লাগবে? $O(Q)$ টা! তাই একটা `root[]` Array নিয়ে নেই যেইগুলা একেক Version এর Root এর Pointer Store করবে। যেমন Linked List এর জন্য `head[]` array তে বিভিন্ন Version এর শুরুর Pointer রেখেছিলাম।

```cpp
struct node{
  .......
} *root[100000];
```

তাহলে Initial Segment Tree এর Node/Link গুলা Build করার জন্য আমরা এইটা করব -   
```cpp
  root[0] = new node();
  root[0] -> build(0, n-1);
```

এবার, Update কিভাবে করব? আমরা সাধারণ Segment Tree এর Update এর থেকে একটু আলাদা ভাবে কাজ করি। আমাদের Update Function একটা Point Update করবে। আমরা যেই Version থেকে Update করছি সেই Version এ Update Function Call করব, আর Update Function নতুন Version এর জন্য যেই সব Node প্রয়োজন সেইগুলা Create করে এবং নিজের Node গুলার সাথে Link করে নতুন Segment Tree root return করবে। আর সেই root কে আমরা প্রয়োজন মত কোন Pointer এর Array তে রেখে দেব।

মোটামুটি Update এর কার্যপদ্ধতি এইরকম -
+ আমরা যদি এমন কোন Node এ থাকি যে তার মধ্যে আমাদের Update point নাই, তাহলে আমরা Direct এই Node টাই Return করে দিতে পারি! এই যে Node টা Return করলাম এইটাকে Parent Node (নতুন Node) এর Left / Right Child হিসাবে সংযুক্ত করা হবে।  Confusing? Read Ahead) <br/>
+ যদি আমরা Leaf Node এ থাকি তাহলে আমরা একটা নতুন Node Create করব, যা Value রাখার রাখব। এর পরে এই নতুন Node টা Return করে দেব। এই Node টাও উপরের কোন নতুন Node এর সাথে যুক্ত হয়ে যাবে।
+ আর কোন Case বাকি থাকল? আমাদের Update Point যখন বর্তমান Node এর Range এর মধ্যে আছে। তখন আমরা একটা নতুন Node Create করব। নতুন Node এর Left Child এ কি থাকবে? আমরা বর্তমানে যেই Node এ আছি এর Left Subtree তে যেই Range এর Segment Tree আছে তার মধ্যে যদি Update করা হয় তাহলে যেই Node টা পাব সেই Node টা! এই Node নতুন হতে পারে আবার পুরানোও হতে পারে! একই ভাবে Right Child ও হবে। যেমন মনে করি আমরা পুরানো একটা Node এর $\[4, 8\]$ Range এর একটা Node এ আছি। আমরা Update করতে চাই $7$ index। তাহলে আমরা যেই পুরানো Range এ আছি এর Left Subtree তে মানে $\[4, 6\]$ Range এ $7$ Update করলে আমরা একটা Node Return পাব সেইটাকে নতুন Node এর Left Child হিসাবে ব্যবহার করব। একই ভাবে Right Subtree তে $\[7, 8\]$ Range এ $7$ Update করলে একটা Node Return পাব, সেইটাকে নতুন Node এর Right Child হিসাবে ব্যবহার করব। শেষে এই নতুন Node টা Return করে দেব।  
+ দেখি, এই উদাহরণ এ যখন আমরা $\[4, 6\]$ Range এ $7$ Update করলে যেই নতুন Node হয় সেইটা চাইলাম তখন কিন্তু কোন নতুন Node Create হবে না! কারণ পুরানো Node দেখবে যে $7$ আসলে ওর যেই Range $\[4,6\]$ তার বাইরে। তাহলে তো কোন নতুন Node এর প্রয়োজন নাই, তাহলে সে নিজেকেই Return করে দেবে, এইটাকে আমরা উপর এ যে নতুন Node Create করলাম তার Left Child হিসাবে লাগিয়ে দেব, তাহলে Left Subtree তে যত Node দরকার ছিল সব Node কেই কিন্তু আমরা পুরানো Tree থেকে নিয়ে নিলাম! (একটা Root Node নিয়েছি মনে হচ্ছে, কিন্তু যখন আমরা Traverse করতে যাব তখন তো আমরা এর সাথে Linked সব Node এই যেতে পারব)
+ এভাবেই খুবই কম সংখ্যক নতুন Node আর বেশিরভাগ পুরানো Node এর Link নিয়ে একটা নতুন Segment Tree এর Root তৈরি হবে। এই Root কে আমরা root[] array তে রেখে দেব পরে কাজে লাগানোর জন্য।  

Update এর Implementation এইরকম হতে পারে -  

```cpp
struct node{
  node *left, *right;                            
  int val;
  ..........
  node *update(int l, int r, int idx, int v) {    
    if(r < idx || l > idx) return this; // Out of range, use this node.
    if(l == r) {  // Leaf Node, create new node and return that.
      node *ret = new node(val, left, right)      
      ret -> val += v;
      return ret;
      // we first cloned our current node, then added v to the value.
    }
    int mid = l + r >> 1;
    node *ret = new node(val); //Create a new node, as idx in in [l, r]  
    ret -> left = left -> update(l, mid, idx, v);
    ret -> right = right -> update(mid+1, r, idx, v);
    // Note that 'ret -> left' is new node's left child,
    // But 'left' is current old node's left child.
    // So we call to update idx in left child of old node.
    // And use it's return node as new node's left child. Same for right.
    ret -> val = ret -> left -> val + ret -> right -> val; // Update value.
    return ret; // Return the new node to parent.
  }
};  
{
  // in main()
  root[0] = new node();
  root[0] -> build(0, n-1);
  root[1] = root[0] -> update(0, n-1, idx, v);
  // So, we're gonna update idx position in root[0]'s segment tree
  // and it will return a pointer to the root of new segment tree.
  // We store that pointer in root[1] for later use.

  // If we want to update in a perticular version,
  // but not create new version then -
  root[0] = root[0] -> update(0, n-1, idx, v);
  // We again store the new version's root in the same position or root[] array!
}
```
<!--- ** -->

আর কি বাকি থাকল? Query? এইটা সবচেয়ে Easy Part। Left Subtree এর Query + Right Subtree এর Query Return করলেই হবে -

```cpp
struct node{
  .........
  // [l, r] node range, [i, j] query range.
  int query(int l, int r, int i, int j) {
    if(r < i || l > j) return 0; // out of range
    if(i <= l && r <= j) { // completely inside
      return val;  // return value stored in this node
    } int mid = l + r >> 1;
    return left -> query(l, mid) +
          right -> query(mid+1, r);
  }
};
```

#### **Summary**
Persistent Segment Tree তে আমরা আগে থেকে সব Node Create করি না।ও যখন প্রয়োজন হয় তখন Create করি। একটা Point Update এর সময় সাধারণ Segment Tree তে Root থেকে Leaf পর্যন্ত Path এর সব Node এর Value Change হয়। Persistent Segment Tree তে আমরা খালি এই Node গুলা Create করে Update করি। বাকি Node গুলাকে Link করে দেই।   

#### **Implementation Practice**
এই প্রব্লেমটাতে Implementation Submit করে দেখতে পারেন। Straight-Forward Persistent Segment Tree -
<a href = "http://www.spoj.com/problems/PSEGTREE/">PSEGTREE - RezwanArefin01</a>

#### **চিন্তা করার জন্য প্রশ্ন**
1. আমরা কি Persistent Segment Tree তে Range Update, Point query করতে পারি? $O(Q\; \log n)$ Memory + Time এ?
2. আমরা কি Persistent Segment Tree তে Range Update, Range Query করতে পারি? মানে Lazy-Propagation? এটা কি $Q$ টা Segment Tree Create করা থেকে ভাল হবে?

[পরবর্তি পর্বে]({{ site.baseurl }}/posts/persistent-segment-tree-02) Persistent Segment Tree এর কিছু Basic Application নিয়ে আলোচনা করব। :slightly_smiling_face:
