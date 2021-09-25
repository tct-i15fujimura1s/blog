---
layout: post
title:  "第40回TechFUL Coding Battle"
date:   2021-09-25 00:55:00 +0900
categories: jekyll update
tags:
- TechFUL
- TCB
---

今回も難しめでした。

テーマが区間ということで、区間に関係する問題がたくさん出ました。
また、なぜかXORの問題も複数出ています。

特別賞でアマギフをいただきました。ありがとうございます。

## 「区間っぽい」

1文字目と2文字目をそれぞれ判定しました。

```python
S = input()
print('Yes' if (S[0] == '(' or S[0] == ']') and (S[1] == ')' or S[1] == ')') else 'No')
```

タイムは1分20秒でした。

### 反省

後で考えると、「区間っぽい文字列」は4通りしかありません。

- `[]`: 閉区間
- `[)`: 右開区間
- `(]`: 左開区間
- `()`: 開区間

だから、全部の場合を書いた方が早かったなと思います。

```python
S = input()
print('Yes' if S == '[]' or S == '[)' or S == '(]' or S == '()' else 'No')
```

さらに `in` 演算子を使うと短く書けたでしょう。

```python
S = input()
print('Yes' if S in ['[]', '[)', '(]', '()'] else 'No')
```

## 「含まない」

どういう場合に、$[L_1, R_1]$に含まれて$[L_2, R_2]$に含まれない$N$が存在するかを考えることもできます。

しかし、値のとる範囲が小さいので、そんな面倒なことを考えなくても、$[L_1, R_1]$に含まれる$N$をすべて調べてしまえば十分です。

```python
L1, R1 = map(int, input().split())
L2, R2 = map(int, input().split())
exist = False
for N in range(L1, R1+1):
  if not L2 <= N <= R2:
    exist = True
print('Yes' if exist else 'No')
```

タイムは2分33秒でした。

### 別解

$[L_1,L_2) \cup (R_2,R_1]$が空でなければ、つまり、$L_1 < L_2 \vee R_2 < R_1$ならば$N$が存在します。

このように判定すれば値のとりうる範囲が大きくても答えることができます。

## 「含む」

個数が少ないので、単純にすべての値について$[l,r)$に含まれるか判定してしまえばよいです。

```python
N, l, r = map(int, input().split())
A = list(map(int, input().split()))
print('Yes' if all(x in range(l, r) for x in A) else 'No')
```

タイムは2分1秒でした。

## 「四捨五入」

立式に少々苦労したので、違う方法を選びました。

$X$を全探索することにします。

$\frac{X}{N}$ を小数第一位で四捨五入すると$K$になる、つまり、`(X * 10 // N + 5) // 10 == K`が成り立てばよいです。

```python
N, K = map(int, input().split())
Infinity = 10**100
ans_min = Infinity
ans_max = -Infinity
for X in range(N * K * 2):
    if (X * 10 // N + 5) // 10 == K:
        ans_min = min(ans_min, X)
        ans_max = max(ans_max, X)
print(f'{ans_min} {ans_max}')
```

タイムは6分41秒でした。

## 「XOR of XOR」

まず、$g(1)$について、XORすべき$f(i,j)$の表を描いてみます。

（ここで、$a^n = \underset{n}{\underbrace{a \oplus a \oplus \cdots \oplus a}}$とします）

```math
\begin{matrix}
A_1 \\
A_1 & A_2 \\
A_1 & A_2 & A_3 \\
\vdots&   &     & \ddots\\
A_1 & A_2 & A_3 & \cdots & A_N
\end{matrix}
```

ここから、$g(1) = {A_1}^N \oplus {A_2}^{N - 1} \oplus \cdots {A_N}^{1}$と予想できます。

つぎに、$g(2)$について、同じことをします。

```math
\begin{matrix}
A_2 \\
A_2 & A_3 \\
\vdots &     & \ddots\\
A_2 & A_3 & \cdots & A_N
\end{matrix}
```

ここから、$g(2) = {A_2}^{N - 1} \oplus {A_3}^{N - 2} \oplus \cdots \oplus {A_N}^{1}$と予想できます。

上２つの結果から、$g(i) = {A_i}^{N - i + 1} \oplus \cdots \oplus {A_N}^{1}$となりそうです。

では、$g(1) \oplus g(2) \oplus \cdots \oplus g(N)$はどうなるでしょうか。

次のような図を描くことでわかります。

```math
\begin{matrix}
{A_1}^{N} & {A_2}^{N - 1} & {A_3}^{N - 2} & \cdots & {A_N}^{1} \\
          & {A_2}^{N - 1} & {A_3}^{N - 2} & \cdots & {A_N}^{1} \\
          &               & {A_3}^{N - 2} & \cdots & {A_N}^{1} \\
          &               &               & \ddots & \vdots    \\
          &               &               &        & {A_N}^{1}
\end{matrix}
```

最終的に、答えは次のようになります。

```math
\bigoplus_{i = 1}^{N} {A_i}^{(N - i + 1) i}
```

ここで、$a^n$は以下のように計算できるため、$O(N)$となります。

```math
a^n = \begin{cases}
a & n\text{が奇数} \\
0 & n\text{が偶数}
\end{cases}
```

```python
N = int(input())
A = list(map(int, input().split()))

ans = 0
for i in range(N):
  if (N - i) * (i + 1) % 2 != 0:
    ans ^= A[i]

print(ans)
```

自信満々で提出したら、偶奇を間違えていたり、指数がオーバーフローでマイナスになってしまったりして、2回もミスをしました…。

タイムは19分14秒、失点22でした。

# 「ぬいぐるみ」

ぬいぐるみの種類が小さいので、配列で管理できます。

長さ$N+1$の配列を作って、要素は最初すべて0にします。余分な1個は番兵です。

```
0  0  0  0  0  0  0  0  0
```

ぬいぐるみ$[3, 6]$が購入されたとき、配列の3番目に1を足し、7（6+1）番目から1を引きます。

```
0  0  1  0  0  0 -1  0  0
```

ぬいぐるみ$[2, 4]$が購入されたとき、配列の2番目に1を足し、5（4+1）番目から1を引きます。

```
0  1  1  0 -1  0 -1  0  0
```

ぬいぐるみ$[8, 8]$が購入されたとき、配列の8番目に1を足し、9（8+1）番目から1を引きます。


```
0  1  1  0 -1  0 -1  1 -1
```

最後に、先頭からの累積和を取ります。

```
0  1  2  2  1  1  0  1  0
```

こうしたとき、$i$番目の要素は、$i$番目のぬいぐるみが購入された回数になっています。

$[1, N]$番目の要素の中に0が何個あるか数えばそれが答えです。
この場合、$[1, 8]$番目には0が2個あるので、答えは2です。

```python
N, Q = map(int, input().split())

count = [0] * (N + 1)
for i in range(Q):
  L, R = map(int, input().split())
  count[L - 1] += 1
  count[R] -= 1

# 累積和をとる
for i in range(N):
  count[i + 1] += count[i]

ans = 0
for i in range(N):
  if count[i] == 0:
    ans += 1

print(ans)
```

タイムは3分6秒でした。

# 「XOOOR」

先程の問題と同じ手法を使います。

長さ$N+1$の配列を用意し、各クエリでは$L_i$番目と$R_i+1$番目に$X_i$をXORし、最後に先頭からの累積XORをとります。

```python
N, Q = map(int, input().split())

A = [0] * (N + 1)
for i in range(Q):
  L, R, X = map(int, input().split())
  A[L - 1] ^= X
  A[R] ^= X

for i in range(N):
  A[i + 1] ^= A[i]

print(' '.join(map(str, A[0:N])))
```

タイムは4分7秒でした。

# 「割り切る回数」

ルジャンドルの定理を使うのですが、コツが必要です。

ルジャンドルの定理は、$M!$が素数$p$で割れる回数は$[M/p] + [M/p^2] + [M/p^3] + \cdots$という定理です。

任意の整数$N$で割り切れる回数を求めたい場合は、$N$を素因数分解して、割り切れる回数を素因数の指数で割ったものの最小値です。

ということは、$R!$が$N$で割り切れる回数から、$(L-1)!$が$N$で割り切れる回数を引けばいいなと考えました。
しかしこれは失敗でした。

なぜなら、$L=3, R=12, N=4$とすればわかりますが、これの答えは4です。しかし、$12!$が4で割り切れるのは5回、$2!$が4で割り切れるのは0回で、5という答えを出してしまいます。

実際には、$N$のそれぞれの素因数について、$R!$が何回割れるか、$(L-1)!$が何回割れるかを計算し、その差を指数で割る必要があります。

また、コーナーケースに気をつける必要があります。

- $N = 1$ の場合: `inf`
- $L = 0$ の場合: `inf`
  - 通常の階乗では、$0! = 1$になりますが、この問題の定義では$0$です。
  - あまりが0ということは、一回も割れないのではなく、無限回割り切れるということに注意しましょう。

```python
from collections import defaultdict
Infinity = 10**100

# 素因数分解
def factorize(n):
  factors = defaultdict(int)
  p = 2
  while p * p <= n:
    while n % p == 0:
      n //= p
      factors[p] += 1
    p += 1
  if n > 1:
    factors[n] += 1
  return factors

# 階乗が素数で割れる回数を求める
def factorial_div_count(n, p):
  count = 0
  while n > 0:
    n //= p
    count += n
  return count

T = int(input())
for i in range(T):
  L, R, N = map(int, input().split())
  if N == 1 or L == 0:
    print('inf')
    continue

  factors = factorize(N)
  ans = Infinity
  for p, e in factors.items():
    count = (factorial_div_count(R, p) - factorial_div_count(L - 1, p)) // e
    ans = min(ans, count)
  print(ans)
```

コーナーケースに苦しみ、$L=0$もありうること、$L=0$のときは`0`ではなく`inf`であることに気づいたとき、愕然としました。

タイムは45分11秒、失点76でした。

# 「1次元警察」

クリアできず。1時間30分11秒時点で、スコアは13でした。

ナイーブな解法で粘ればよかったかもしれません。