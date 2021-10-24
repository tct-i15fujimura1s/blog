---
layout: post
title:  "第41回TechFUL Coding Battle「クエリ形式」"
date:   2021-10-25 08:53:00 +0900
categories: jekyll update
tags:
- TechFUL
- TCB
---

噛みごたえのある難問揃いでしたが、非常に楽しめました。最後の一問を解けなくて悔しいです。

## 「アップandダウン」

周期$2$で$0$と$1$を行き来するので、答えは$Q$を$2$で割ったあまりになります。

正答まで42秒でした。

```python
Q = int(input())
print(Q % 2)
```

## 「Kth Alphabet」

クエリごとに文字列の $K$ 番目の文字を出力します。

クエリの量も少なく、また単純な操作なので、そのまま書けばよいです。

クエリは1-based indexで与えられるのに対して、多くの言語ではインデックスは0-based indexであることに注意しましょう。

正答まで51秒でした。

```python
N, Q = map(int, input().split())
S = input()
for i in range(Q):
    K = int(input())
    print(S[K - 1])
```

## 「問い合わせ」

前問に引き続き、言われたとおりにクエリを処理する問題です。

入力に文字列と整数が混在してるのが少し曲者です。

正答まで2分34秒でした。

```python
N = int(input())
A = input().split()
Q = int(input())
for i in range(Q):
    query = input().split()
    q = int(query[0])
    if q == 1:
        A[int(query[1]) - 1] = query[2]
    elif q == 2:
        A.append(query[1])
    else:
        print(' '.join(A))
```

## 「何番目？」

やはり言われたとおりにクエリを処理する問題です。

今回は、配列や辞書などのデータ構造を使う必要があります。

- 配列なら、 $a_i$ 番目に最初に与えられたクエリ番号を保存しておくことで、両方のクエリに答えることができます。

- 辞書なら、適切な関数を呼び出すとよいです。

正答まで1分33秒でした。

```python
Q = int(input())
d = {}

for i in range(1, Q + 1):
    q, a = map(int, input().split())
    if q == 1:
        d.setdefault(a, i)
    else:
        print(d.get(a, -1))
```

## 「繰り上がり」

クエリ形式ですが、クエリ形式でなくても解き方は同じです。

繰り上がりがあるということは、どこかの桁の和が$K$を越えるということです。

なので、$K$進数変換と同様にすることができます。

正答まで2分29秒でした。

```python
Q, K = map(int, input().split())
for i in range(Q):
    N, M = map(int, input().split())
    carry = False
    while N > 0 and M > 0:
        if N % K + M % K >= K:
        carry = True
        N //= K
        M //= K
    print('Yes' if carry else 'No')
```

## 「自販機」

有意義なクエリ形式です。

自販機の気持ちになって解きます。
4つのクエリを処理するために、記録する必要がある値は以下のもの。

- 飲料ごとの価格と残りの本数
- 投入された金額

普通の自販機ならおつり切れということもあるのですが、この自販機には無尽蔵なほどのおつりが用意されているので、硬貨が何枚入っているかは記録する必要がありません。

### クエリ1

金額さえ覚えておけばいいので、単に投入された金額に $2^a \times b$を加算します。

### クエリ2

飲料の補充です。素直に書けば処理できます。
$B_c$ に $d$ を加算すればよいです。

### クエリ3

現在投入されている金額 $C$ で、飲料 $e$ を $f$ 本以下買えるだけ買います。

買う本数 $x$ は、以下のすべての条件を満たします。

- $f$ 本以下
- $\lfloor\frac{A_e}{C}\rfloor$ 本以下
- $B_c$ 本以下

つまり、$x = \min(f, \lfloor\frac{A_e}{C}\rfloor, B_e)$です。

この本数分、 $B_e$ と $C$ を更新します。

### クエリ4

枚数が最小になるように $C$ 円のおつりを払い、硬貨の枚数を出力します。

実は、硬貨の額に関して倍数の関係が成り立つ（どの硬貨の額面もがそれより小さい額の硬貨の額面の倍数となっている）とき、大きい額の硬貨から使えるだけ使うようにする（貪欲をする）と、枚数が最小になります。
この問題では倍数の関係が成り立つので、貪欲をすればよいです。

また、これは$2$進変換とほぼ同じなので、$2^0$円硬貨から$2^{29}$円硬貨までは$\lfloor\frac{C}{2^j}\rfloor$を$2$で割ったあまり、$2^{30}$硬貨は$\lfloor\frac{C}{2^{30}}\rfloor$枚としてもよいです。

注意するべきは、$2^{31}$円のように、$2^{30}$より大きいときは、$2^{30}$円硬貨の枚数で表す必要があることです。$2^{31}$円硬貨は存在しないためです。

### コード

正答まで6分26秒でした。

```python
N, Q = map(int, input().split())
A = list(map(int, input().split()))
B = list(map(int, input().split()))
C = 0
for i in range(Q):
    q, *query = list(map(int, input().split()))
    if q == 1:
        a, b = query
        C += b << a
    elif q == 2:
        c = query[0] - 1
        d = query[1]
        B[c] += d
    elif q == 3:
        e = query[0] - 1
        f = query[1]
        x = min(f, C // A[e], B[e])
        B[e] -= x
        C -= A[e] * x
    else:
        coins = 0
        for j in range(0, 29 + 1):
            coins += (C // 2**j) % 2
        coins += C // 2**30
        C = 0
        print(coins)
```

## 「数列の和」

もし、総和ではなくて個数を求めるのなら簡単だったでしょう。階差を取れば、$K$個の区別しない玉を$N$個の区別する箱に入れる場合の数の問題になるからです。その場合の答えは<span title="日本放送協会" style="cursor:help">$_N H _K$</span>です。

残念ですが、この問題はそうではありません。求めるのは個数ではなくて総和です。すると、階差を取ったところで、どうすればいいのか？

そこまで考えて、結局何もわかりませんでした。しかし、なにもわからないなりに、正答を取りました。方法は<span title="OEIS" style="cursor:help">秘密</span>です。

```math
\frac{N (N + 1)}{2} {_N H _{K - 1}}
```

正直、こんな簡単な式とは思っていなかったので、理解できなかったことも含め、とても悔しいです。

正答まで3時間49分48秒、3回提出して、失点50でした。

```python
class Combination:
    def __init__(self, max, p):
        fact = [1, 1]
        fact_inv = [1, 1]
        inv = [0, 1]
        for i in range(2, max + 1):
            fact.append(fact[i - 1] * i % p)
            inv.append(-inv[p % i] * (p // i) % p)
            fact_inv.append(fact_inv[i - 1] * inv[i] % p)
        self.p = p
        self.fact = fact
        self.fact_inv = fact_inv
        self.inv = inv
  
    def nCk(self, n, k):
        if n < k or n < 0 or k < 0:
            return 0
        return self.fact[n] * self.fact_inv[k] % self.p * self.fact_inv[n - k] % self.p

P = 998244353
com = Combination(2 * 10**5, P)

T = int(input())
for i in range(T):
    N, K = map(int, input().split())
    print(N * (N + 1) // 2 % P * com.nCk(N + K, K - 1) % P)
```

## 「Range of XOR」

***めちゃくちゃ難しかった！！！***

1日がかりで解きました。

クエリ形式であることは特に解き方に影響しません。

$L$ 以上 $R$ 以下の整数の間に、 $N \oplus M = K$ となる、異なる整数のペア $(N, M)$ がいくつ存在するかという問題です。

XORといえば、桁ごとに考えるのが鉄則ですが、今回はそれは難しそうです。というのも、桁ごとに考えるには、異なる桁の間で干渉が発生しないことが必要だからです。今回は大小関係を考える必要があり、大小関係には他の桁も影響します。そのため、別のやり方を考えました。

まずは式変形をしました。XORといえば、$X = X^{-1}$という等式が成り立つことが有名です。なので、$M = N \oplus N$としてみます。

すると、少しだけ見えてきた感じがしました。$N$と$N\oplus K$のどちらも$L$以上$R$以下におさまるような$N$の個数です。

ただ、これだけでは$N < N \oplus K$のときと$N > N\oplus K$のときが重複してしまいます。しかしこれは心配ありません。単純に$2$で割れば良いからです。$N = N \oplus K$のときはどうするのかと思うかもしれませんが、そのときは異なる整数のペアではないので数えません。つまり、$K = 0$のときは$0$を答え、それ以外のときは先程の条件を満たす$N$の個数を$2$で割って答えれば良いのです。

では、どのようにしてその$N$を数えるか？これが難題でした。まず考えたことは、区間なのでセグメント木を使ってみることです。しかし、どう使えばいいのか全く分からないので、すぐに諦めました。次に考えたことは、以前のTCBでも出たらしいBinary Trieなるものを使うことでした。一見これは筋が良さそうに思えますが、ただ問題はどう使えばいいか分からないことです。使ったこともないデータ構造を使うのは難しい。最後に思いついたのが、枝刈りをしながら探索することです。結局、最後の考え方で正答を取ることができました。

最後に考えた方法は、$N$と$M$を同時に、上の桁から数えていくというものです。ある桁において$K=0$なら$N$と$M$に$(0, 0)$または$(1, 1)$を割り当て、$K = 1$なら$(0, 1)$と$(1, 0)$を割り当てます。このとき、$N$と$M$のどちらかが$L$より小さくなってしまったり、$R$より大きくなってしまった分岐は捨てます。逆に、どうやっても$L$以上$R$以下に収まることがわかりきっている分岐は、そこで打ち切って、答えを出してしまいます。このようにすると、上下の端だけを考えればいいので、$O(ビット数)$程度で探索することができそうです。結局、一度は捨てた考えであるBinary Trieのような感じになってしまいました。いやちょっと違うかもしれません。

<!-- 図解の写真をつけたい -->

正答まで6時間13分24秒、失点42でした。

```python
Q = int(input())
for i in range(Q):
    L, R, K = map(int, input().split())

    if K == 0:
        print(0)
        continue

    Lbit = []
    Rbit = []
    Kbit = []
    for i in range(30):
        Lbit.append(L >> i & 1)
        Rbit.append(R >> i & 1)
        Kbit.append(K >> i & 1)
    
    def f(i, nl, nr, ml, mr):
        if i == -1: return 1
        x = 0
        for n2 in range(2):
            m2 = n2 ^ Kbit[i]
            if Lbit[i] == 1 and (nl and n2 == 0 or ml and m2 == 0): continue
            if Rbit[i] == 0 and (nr and n2 == 1 or mr and m2 == 1): continue
            nl2 = nl and Lbit[i] == n2
            nr2 = nr and Rbit[i] == n2
            ml2 = ml and Lbit[i] == m2
            mr2 = mr and Rbit[i] == m2
            if nl2 or nr2 or ml2 or mr2:
                x += f(i - 1, nl2, nr2, ml2, mr2)
            else:
                x += 1 << i
        return x
    
    print(f(29, True, True, True, True) // 2)
```

## 「重要な要素」

自分より前に同じ要素が存在しないか、自分より後に同じ要素が存在しないのどちらかであれば重要な要素です。

非常に、クエリ形式であることが活きる問題だなと思いました。

### $O(NQ)$解

最初は、$O(NQ)$解を試してみました。というのも、「実行時間制限に注意して実装してください。」という意味深な文言があったからです。「これは、もしかすると、$O(NQ)$でもいけるのではないか…？」ということで、`bitset`を使って実装してみました。

それぞれのクエリごとに、左右からそれぞれ見て、初出の要素のインデックスを足すということをします。

注意するべきは、その要素が$1$つしか存在しない場合、左右で$2$回数えてしまう点です。これについては、$1$つしか存在しないという判定をして、そのような要素については片方では飛ばすようにすればよいです。

この解法では、テストケースは9個通ったものの1つだけTLE（時間切れ）になりました。

### $O((N + Q) \sqrt N)$解

いろいろ考え、調べてみた結果、[Mo's Algorithm](https://ei1333.hateblo.jp/entry/2017/09/11/211011)というものが一番あっているのではないかということを見つけました。

> うしのアルゴリズムです. もーもー.

アルゴリズムの内容についてはよくわかりませんでした。しかし、どうも区間をのばしたりちぢめたりすることが書ければ、あとはフレームワーク的に使えそうだったので、使ってみることにしました。

そうなれば、あとはどうやって伸ばしたり縮めたりするかだけです。ここまでくれば大丈夫、私の得意ポイントです。まずは、ある区間のクエリを見るとき、どうなっているかを考えてみます。それぞれの要素についての最も左と右のインデックスがわかっていれば大丈夫でしょうか。いや、実はそうではありません。縮めるときに左端と右端だけがわかっても、その一つ前がわからないので困ります。ここで天才的なひらめきが降りてきました。`Deque`を使えばよいのです！これで解くことができました。

正答まで1時間8分51秒、3回提出して、失点68でした。

```python
from math import sqrt
from collections import deque

class Mo:
  def __init__(self, n):
    self.n = n
    self.lr = []
  
  def add(self, l, r):
    self.lr.append((l, r))
  
  def build(self, add_left, add_right, erase_left, erase_right, out):
    q = len(self.lr)
    bs = self.n // min(self.n, int(sqrt(q)))
    ord = list(range(q))
    ord.sort(key = lambda i: self.lr[i])
    l = r = 0
    for idx in ord:
      while l > self.lr[idx][0]:
        l -= 1
        add_left(l)
      while r < self.lr[idx][1]:
        add_right(r)
        r += 1
      while l < self.lr[idx][0]:
        erase_left(l)
        l += 1
      while r > self.lr[idx][1]:
        r -= 1
        erase_right(r)
      out(idx)

N = int(input())
A = [int(x) - 1 for x in input().split()]
Q = int(input())
queries = []
for i in range(Q):
  L, R = map(int, input().split())
  L -= 1
  queries.append((L, R))

class Solver:
  def solve(self, A, queries):
    self.A = A
    self.queries = queries
    self.count = 0
    self.sum = 0
    self.indices = [deque() for i in range(30000)]
    self.ans = [None] * len(queries)
    mo = Mo(len(A))
    for L, R in queries:
      mo.add(L, R)
    mo.build(self.add_left, self.add_right, self.erase_left, self.erase_right, self.out)
    return self.ans

  def add_left(self, i):
    idxs = self.indices[self.A[i]]
    if len(idxs) <= 1: self.count += 1
    else: self.sum -= idxs[0]
    idxs.appendleft(i + 1)
    self.sum += i + 1
  
  def add_right(self, i):
    idxs = self.indices[self.A[i]]
    if len(idxs) <= 1: self.count += 1
    else: self.sum -= idxs[-1]
    idxs.append(i + 1)
    self.sum += i + 1
  
  def erase_left(self, i):
    idxs = self.indices[self.A[i]]
    self.sum -= idxs.popleft()
    if len(idxs) <= 1: self.count -= 1
    else: self.sum += idxs[0]
  
  def erase_right(self, i):
    idxs = self.indices[self.A[i]]
    self.sum -= idxs.pop()
    if len(idxs) <= 1: self.count -= 1
    else: self.sum += idxs[-1]

  def out(self, i):
    self.ans[i] = self.sum - self.queries[i][0] * self.count

solver = Solver()
ans = solver.solve(A, queries)
print('\n'.join(map(str, ans)))
```

## 「最大値はいくつ？」

正直、ここまでセグメント木のセの字も見当たらなかったので、そろそろ出るのではないかと思っていました。クエリといえばセグメント木、セグメント木といえばクエリ、そうではないですか？しかし、非常に残念なことに、この問題はセグメント木ではないように見えます。

この問題は、最大の内積を計算する問題のようです。$N$個のベクトル$(X_i, Y_i)$があり、そこからベクトル$(A_j, B_j)$との内積の最大値を計算します。

まず考えたことは、内積が最大になる$(X_i, Y_i)$は凸包の頂点のひとつであるだろうということです。実際、これは正しいようでした。2次元の凸包の頂点はグラハムスキャンによって$O(N\log N)$で計算することができます。凸包の中に含まれる頂点はこれによって取り除くことができるので、いくらか計算量を落とせるのではないかと考えました。しかし、凸包をとってみても、TLE（時間切れ）は直りませんでした。

このあたりで、もう眠かったので、寝てしまいました。

2時間57分56秒時点の提出で6ケース通過、43点でした。