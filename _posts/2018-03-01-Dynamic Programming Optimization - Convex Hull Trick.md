---
published: false
title: Dynamic Programming Optimization - Convex Hull Trick
layout: post
comments: true
author: RezwanArefin01
category: [Dynamic Programming, Data Structure]
permalink: '/posts/convex-hull-trick'
---
Convex Hull Trick (DP Optimization), সংক্ষেপে CHT নামটা হয়ত অনেকেই শুনে থাকবেন। সবার মনেই প্রথম প্রশ্ন জাগে "আমি তো Convex Hull ই পারি না, তাহলে CHT শিখব কি করে :thinking:"। আসলে Geometry এর Convex Hull আর Dynamic Programming এর Convex Hull Trick আসলে ২টা একেবারেই ভিন্ন জিনিস।   

### **Prerequisites**
- Coordinate Geometry এর প্রাথমিক ধারণা থাকা।
- Line Equation সম্পর্কে ধারণা থাকা (ক্লাস ৯-১০ পর্যন্ত ভাল করে থাকলেই হবে)।
- 2D Dynamic Programming এর বেশ কিছু Problem Solve করা।   
- Binary / Ternary Search সম্পর্কে ধারণা থাকা।

### **What is this Convex Hull Trick?**
আগে আমরা Dynamic Programming Optimization এর দিকে না যাই। আগে দেখি Convex Hull Trick জিনিসটা কি। এর পরে এইটা ব্যবহার করে কিভাবে Dynamic Programming Optimize করা যায় সেটা দেখব।   

CHT আসলে একটা Data Structure এর মত, যেইটা কিছু Line maintain করে। এতে আমরা অনেকগুলা Line রাখতে পারি। Line বলেতে এখানে Line এর function বুঝানো হচ্ছে। যেমনঃ- $f_i(x) = m_ix + b_i$। আর Query করতে পারি কোন একটা $x$ এর জন্য $max/min\left\lbrace f_i(x)\right\rbrace$ কত হতে পারে      
যেমনঃ CHT তে যদি এই function গুলা থাকে -    
- $f_1(x) = 4x + 3$
- $f_2(x) = 3x - 2$
- $f_3(x) = -5x + 15$

তাহলে আমরা $4$ দিয়ে Query করে Maximum চেলে উত্তর পাব $max\lbrace f_1(4), f_2(4), f_3(4)\rbrace = max\lbrace 19, 10, -5\rbrace = 19$    

Convex Hull Trick এর ২টা Version আছে। একটা Semi-Offline। যেইটাতে আমরা ইচ্ছা করলেই Random line add করতে পারি না। কিছু Condition মানতে হয়, যেমনঃ $m_1 \geq m_2 \geq \cdots \geq m_i \geq m_{i+1}$ অথবা $m_1 \leq m_2 \leq \cdots \leq m_i \leq m_{i+1}$ এইরকম। এইধরনের CHT তে আমরা $O(1)$ Amortized (অর্থাৎ $O(n)$ overall) Complexity তে নতুন Line add করতে পারি। আবার $O(\log n)$, অথবা বিশেষ শর্ত সাপেক্ষে $O(1)$ Amortized Complexity তে Query করতে পারি।       
আরেক ধরনের আছে যেইটাতে ইচ্ছা মত Random Line Add করা যায়। সেইটাকে Dynamic CHT বলে। এইধরনের CHT তে $O(\log n)$ এ নতুন Line add বা Query করা যায়।    

এই Tutorial শুধুমাত্র Offline CHT এর মধ্যে সীমাবদ্ধ থাকবে।   

### **Basic Idea**    
প্রথমে আমরা যা করব, Line গুলাকে Plot করে ফেলব।     
মনে করি, আমাদের function গুলা হল -  

$$f_1(x) = -x + 5 \\ f_2(x) = 5 \\ f_3(x) = x + 4 \\ f_4(x) = \frac{-1}{2} x + 4$$   

Function গুলা Plot করে ফেলি -    
<p align="center"><img src="/img/cht/plot1.png"></p>

মনে করি আমাদের Query Point হল $1$। তাহলে কোন একটা Function $f$ এর জন্য $f(1)$ হবে $x = 1$ Line টা $f$ function এর line যে যেই বিন্দুতে ছেদ করে তার $y$ এর value.   

যেমন উপরের function গুলার জন্য $x = 1$ line যেইসব বিন্দুতে ছেদ করে -     
<p align="center"><img src="/img/cht/plot2.png"></p>

Plot থেকে দেখতে পাই যে, $f_1(1) = A_y = 4$, $f_2(1) = B_y = 3$, $f_3(1) = C_y = 5$ এবং $f_4(1) = 3.5$।   
এখানে Minimum value হল $B_y = 3$, মানে সবচেয়ে নিচের দিকে যেই ছেদবিন্দু তা হয়েছে সেইটার $y$ এর value.   

এখন একটা Observation করা যায় -     
উপরের Function গুলার জন্য, CHT তে যদি আমরা Minimum maintain করতে চাই তাহলে আমাদের কি $f_4$ এর আদৌ কোন দরকার আছে? যেকোনো Point এর জন্য আমরা যদি Query করি কখনো কি $f_4$ আমাদের Minimum Value দিতে পারবে?      

যেমন আরও কিছু Query Point যেমন $\lbrace -2, 0, 3\rbrace$ এর জন্য Line আঁকায়ে দেখতে পারি -    
<p align="center"><img src="/img/cht/plot3.png"></p>

এখানে $A, B, C$ Point গুলার কোনটাই $f_4$ এর Line এর উপরে নাই। লক্ষ্য করলে দেখব আমরা Query যাই করি না কেন, $f_4$ কখনই Minimum Value দেবেনা, যদি আমাদের $f_1, f_2, f_3$ থাকে।     

আরেকটা Observation আছে। একটা নির্দিষ্ট Range এর সব Query এর জন্য একটাই Function বার বার Minimum Value দেবে। যেমন -    
<p align="center"><img src="/img/cht/plot4.png"></p>

এইখানে সব গুলা Query এর জন্য শুধু $f_2$ Minimum value দিচ্ছে। আসলে, যেকোনো $x \in \[-1, 2\]$ এর জন্যই $f_2$-ই Minimum দেবে।      

এখন আমরা CHT এর Main Idea টা বলতে পারি, সেইটা হল প্রয়োজনীয় Line গুলা, মানে যে Line গুলা কখনো না কখনো Minimum value দিতে পারে সেইগুলা রেখে বাকি সব Line বাদ দিয়ে দেওয়া।    

আরেকটা জিনিস আমরা লক্ষ্য করতে পারি, যেই Line গুলা আমরা CHT তে রাখতে চাচ্ছি, তাদের যেই অংশ গুলা কাজে লাগে সেইগুলা একটা Half Convex Hull গঠন করে। এদের Upper Hull / Lower Hull বলে। যেমন উপরের Line গুলার জন্য Function গুলার প্রয়োজনীয় অংশ এইগুলা -     
<p align="center"><img src="/img/cht/plot5.png"></p>   

আমরা যেকোনো Query-ই করি না কেন, Minimum value এই অংশ গুলার উপরেই থাকবে। তাহলে এর উপরে কোন Line বা Line এর অংশ গেলেই আমরা সেইটাকে বাদ দিয়ে দিতে পারি। CHT এর মূল বিষয় এইটাই। :slightly_smiling_face:

### **Being more formal**
এই পর্যন্ত যা যা লিখলাম সেইটা বুঝে থাকলে সামনে যেতে পারেন।   
এখনো পর্যন্ত আমরা যেইটা জানি না সেইটা হল, একটা Line যে আমাদের লাগবে না তা বুঝব কি করে?  
আমরা ধরে নেই যে আমাদের যেই Line গুলা দেওয়া আছে সেইগুলার জন্য      

$$m_1 \geq m_2 \geq \cdots \geq m_{i - 1} \geq m_{i}$$

মনে করি, আমরা $i^{th}$ line add করছি এখন। আর আমাদের $i - 1$ টা Line আগেই Add করা হয়ে গেছে। তাহলে আমাদের Line গুলা একটা Lower Hull বানিয়ে ফেলছে নিশ্চয়ই। ধরি এইরকম -     
<p align="center"><img src="/img/cht/plot6.png"></p>     

এখন লক্ষ্য করি, এর পরের বার আমরা যেই Line টা add সেইটা কিন্তু কখনই এইরকম হবে না -   
<p align="center"><img src="/img/cht/plot7.png"></p>   

কারণ কি? এর কারণ হলও আমরা ধরে নিয়েছে যে $m_{i-1} > m_{i}$। কিন্তু এই Line এর Slope আসলে বেশি হয়ে গিয়েছে। এজন্য দেখা যায় যে, আমরা মাত্র ২ ধরনের Line পেতে পারি -  

এক রকম Line আসতে পারে যেইটা আসার পরে শুধু মাত্র শেষের Line এর একটা অংশ থেকে পরের সব নিজের করে ফেলবে, মানই শেষ Line এর একটা অংশের পরের সব Query এর জন্য ওই Line ই Minimum দিবে। যেমন এইরকম Line আসতে পারে -      
<p align="center"><img src="/img/cht/plot8.png"></p>   

এইরকম হলে কি করতে হবে সেইটা পরিষ্কার, আমরা এইটাকে CHT তে নিয়ে নিলেই হল।   

আবার, আরেক ধরনের Line হতে পারে, যেইটা আগের অনেক Line এর Range এর মধ্যেও Minimum দিতে পারে। যেমন -     
<p align="center"><img src="/img/cht/plot9.png"></p>   

এখন একটা জিনিস খেয়াল করি, যেইসব জায়গায় $f_2$ Line minimum দিচ্ছিল এখন সেইসব জায়গায় $f_4$ Line minimum দিচ্ছে -     
<p align="center"><img src="/img/cht/plot10.png"></p>   

এইখানে অবশ্যই $G$, $C$ থেকে ভাল, আবার $E$, $F$ থেকে ভাল। আসলে এখন $f_2$ এর আর দরকারই নাই। এখন আমাদের নতুন Hull হবে -     
<p align="center"><img src="/img/cht/plot11.png"></p>   

আবার আমাদের নতুন Line যদি এইরকম হত -    
<p align="center"><img src="/img/cht/plot12.png"></p>   

তাহলে কিন্তু $f_2$ আর $f_3$ ২টা Line এর-ই আর কোন দরকার থাকত না। তখন নতুন Hull হতো -   
<p align="center"><img src="/img/cht/plot13.png"></p>   

তাহলে আমাদের কি করতে হবে এখন পরিষ্কার, আমাদের আগের ২টা Line দেখে বলতে হবে যে আমাদের নতুন Line টা Hull এ থাকবে নাকি থাকবে না। আবার থাকলেও, আগের Line টা কে রাখবও নাকি রাখবও না। :thinking:   

এখন আমরা শুধু ৩টা Line নিয়ে চিন্তা করি, মনে করি এমন -    
<p align="center"><img src="/img/cht/plot14.png"></p>   

এইখানে আমাদের $f_3$ কে রাখাই ভাল। আবার এইরকম হতে পারে -   
<p align="center"><img src="/img/cht/plot15.png"></p>   

উপরের ২টার ক্ষেত্রেই, বা আমরা আরও আঁকিয়ে দেখতে পারি যে আসলে, $f_1$ এর সাথে $f_3$ এর intersection point (উপরের Plot এ Point $D$) যদি $f_1$ এর সাথে $f_2$ এর intersection point (উপরের Plot এ Point $C$) এর বামে হয় তাহলে আমাদের $f_2$ এর আর কোন দরকার নাই। আর ডানে হলে আমরা $f_3, f_2$ ২টাকেই রেখে দিতে পারি। এইটা কাজ করবে যখন আমাদের Line গুলার $m_1 \geq m_2 \geq \cdots \geq m_{i-1} \geq m_i$ থাকবে, আর আমরা Minimum maintain করতে চাই।    

আসলে আমরা ৪ ধররেন Offline CHT পেতে পারি -   
- $m_1 \geq m_2 \geq \cdots \geq m_{i-1} \geq m_i$, Minimum Query
- $m_1 \geq m_2 \geq \cdots \geq m_{i-1} \geq m_i$, Maximum Query
- $m_1 \leq m_2 \leq \cdots \leq m_{i-1} \leq m_i$, Maximum Query
- $m_1 \leq m_2 \leq \cdots \leq m_{i-1} \leq m_i$, Minimum Query   

এই ৪ ধরনের জন্যই আমরা একই ভাবে ৩টা Line আঁকিয়েই বের করতে পারি যে $f_1, f_2, f_3$ এর মধ্যে $f_2$ কে আমরা কোন শর্তে বাদ দেব। সেটা ``"Left as an exercise to the readers"``.

### **Implementation**   
এখন এইটা Implement করা যাক। এখানে খালি $m_i \geq m_{i+1}$ আর Minimum maintain করে এমন CHT Implement করব। বাকিগুলাও খালি একটা condition পরিবর্তন করেই বানানো যাবে।

শুরুতেই আমরা Line-গুলার Slope আর Constant store করার জন্য ২টা array/vector নিয়ে নিতে পারি -  
```cpp
vector<long long> m, b;
```

এখন আমাদের একটা Helper Function লাগবে, যেইটা বলবে $f_1, f_2, f_3$ এর মধ্যে $f_2$ কে আমরা বাদ দেবও নাকি। উপরের অলোচনা থেকে আমরা জানি যে, ${(f_1 \cap f_3)}_x < {(f1 \cap f2)}_x$ হলে আমরা $f_2$ কে বাদ দেব।    
আবার ২টা Line - $y = m_1x + b_1, y = m_2x + b_2$ এর intersection point $(x, y)$ হল আসলে তাদের সমাধান $(x, y)$।     
তাহলে সমাধান করা যাক -     
$\displaystyle m_1x + b_1 = m_2x + b_2$   
$\displaystyle \Rightarrow m_1x - m_2x = b_2 - b_1$   
$\displaystyle \Rightarrow (m_1 - m_2)x = b_2 - b_1$    
$\displaystyle \Rightarrow x = \frac{b_2 - b_1}{m_1 - m_2}$

তাহলে আমরা সহজেই function টা লিখতে পারি -   
```cpp
bool bad(int f1, int f2, int f3) {
  return double(b[f3] - b[f1]) / double(m[f1] - m[f3]) <=
         double(b[f2] - b[f1]) / double(m[f1] - m[f2]);  
}
```

কিন্তু এই function এ একটা সমস্যা আছে। `double` এ Compare করতে গেলে Precision error হতে পারে। এজন্য আমরা একটা কাজ করতে পারি - Inequality টাতে আড়গুণ করে ফেলতে পারি। তাহলে function টা হবে -   
```cpp
bool bad(int f1, int f2, int f3) {
  return (b[f3] - b[f1]) * (m[f1] - m[f2]) <=
         (b[f2] - b[f1]) * (m[f1] - m[f3]);  
}
```

এখন আর Precision error হওয়ার সম্ভাবনা নাই। কিন্তু এখন আরেকটা সমস্যা আছে, সেইটা হলও Overflow হতে পারে। কারণ `b, m` ২টাই সর্বচ্চ $10^{18}$ হতে পারে। তাই তাদের গুণফল $10^{36}$ হতে পারে। এই জন্য একটা Work around আছে -    

```cpp
bool bad(int f1, int f2, int f3) {
  return __int128(b[f3] - b[f1]) * (m[f1] - m[f2]) <=
         __int128(b[f2] - b[f1]) * (m[f1] - m[f3]);  // only for gnu g++
  // or compare by taking the answer in double :D
  // as double can *store* at most 10^300 (with precision error :p)
  return 1.0 * (b[f3] - b[f1]) * (m[f1] - m[f2]) <=
         1.0 * (b[f2] - b[f1]) * (m[f1] - m[f3]);  
}
```

এখন আমরা `add(m, b)` function লিখতে পারি। যেইটা একটা $f(x) = mx + b$ function add করবে CHT তে -
```cpp
void add(long long m_, long long b_) {
  m.push_back(m_); b.push_back(b_); // push in CHT
  int sz = m.size();
  // notice that f1 from discussion is in position sz - 3
  // f2 is in sz - 2, new line is in sz - 1
  while(sz >= 3 && bad(sz - 3, sz - 2, sz - 1)) {
    m.erase(m.end() - 2); // remove f2's m
    b.erase(b.end() - 2); // remove f2's b
    sz--; // size is decreased by 1
  } // we remove f2's while we can
}
```

হয়ে গেল CHT বানানো :slightly_smiling_face:। এখন বাকি Query করা।    

### **Query**  
Query অনেক ভাবে করা যেতে পারে। Minimum Query করছি ধরে নিয়ে কয়েকটা Approach বলছি -    

#### **Approach 1**
আমরা যখন Query করছি যে $x$ input দিলে কোন function সবচেয়ে ছোট মান দেয়, তখন আসলে আমরা কি করছি দেখা যাক। যেমন এইরকম CHT তে যদি আমরা $x = -1.5$ Query করি তাহলে এমন হয় -
<p align="center"><img src="/img/cht/plot16.png"></p>   

আমরা তো আসলে Query করে $D_y$ চাচ্ছি তাই না? তাহলে এই বিন্দু কোন Line এর উপরে আছে জানলেই হবে। এর পরে ওই Line কে Query এর $x$ এ Evaluate করলেই আমাদের উত্তর পেয়ে যাব।  আর সেটা সোজাও। উপরের Plot থেকে আমরা এই Approach এ আসতে পারি -   
- পাশাপাশি ২টা Line এর intersection point গুলার $x$ এর value আমরা note করে রাখব।   
- এখন একটা Query $x$ আসলে আমাদের সবচেয়ে বড় একটা intersection point বের করতে হবে যেন $P_x \leq x$ হয়, যেমন উপরের Plot এ Point $C$। এর পরে ওই intersection point এর পরের Line টা তে $x$ কে Evaluate করলেই আমাদের উত্তর পেয়ে যাব। এইখানে আমরা ওই রকম Point বের করার জন্য আমাদের আগের বানানো List এর উপরে Binary Search করতে পারি। তাহলে Complexity $O(\log n)$।    

এইটা Implement করা একটু ঝামেলা। `add()` function টা একটু Complex হয়ে যায়। Line pop করার সময় intersection point ও pop করতে হবে। আবার নতুন Line এর টাও Push করেত হবে।  

#### **Approach 2**
একটা Critical Observation এর মাধ্যমে আমরা CHT এর আরেকটা বৈশিষ্ট্য বের করতে পারি। নিচের Plot টা লক্ষ্য করি -   
<p align="center"><img src="/img/cht/plot17.png"></p>   

এখানে $x = -1.3$ এর জন্য এইটা $f_1, f_2, f_3, f_4$ কে যথাক্রমে $A, B, C, D$ Point এ ছেদ করেছে। আমাদের উত্তর হবে $B_y$ তাই না?    
এখন লক্ষ্য করি যে, $A_y > B_y < C_y < D_y$    
মানে আমাদের উত্তর যেইটা সেইটা সবথেকে ছোট, কিন্তু এর আগে পরে বাড়তে থাকে intersection point টা। আমরা আরও কিছু Plot করে দেখতে পারি, এইটা সব সময় সত্যি।     

তাহলে আমরা আরেকটা Approach পেতে পারি এই Observation থেকে। যেহেতু আমাদের Answer যেই Line টা সেইটার থেকে ২ দিকেই function এর value বাড়তে থাকে, তাই আমরা এই function এর index উপরে Ternary Search করতে পারি। :slightly_smiling_face:   
অনেকটা এইরকম -   

```cpp
ll f(int i, ll x) { return m[i] * x + b[i]; }
ll query(ll x) {
  int  lo = 0, hi = m.size() - 1;
  ll ans = -1e18;
  while(lo <= hi) {
    int mid1 = (lo + lo + hi) / 3;
    int mid2 = (lo + hi + hi) / 3;
    ll y1 = f(mid1, x), y1 = f(mid2, x);
    if(y1 <= y2) ans = y1, hi = mid2 - 1;
    else ans = y2, lo = mid1 + 1;
  } return ans;
}
```
একই রকম Observation আমরা বাকি ৩ ধরনের Offline CHT এর জন্যেও করতে পারি। এই Approach এর Complexity $O(\log_3 n)$
