---
layout: post
title:  "BisonでTechFULプラクティス問題「ブーリアンハッキング3」を解く"
date:   2021-10-25 08:53:00 +0900
categories: jekyll update
tags:
- TechFUL
- 構文解析
- Bison
- TechFUL難易度8
- プラクティス問題
- 掃き出し法
---

ここでは、Bisonというツールを使って、構文解析を楽に実装する方法を紹介します。

構文解析を自分で実装するのはめんどうなものです。書く量は多いし、実装を間違えたときのデバッグも大変です。さらに、時間制限付きの問題ならなおさらでしょう。

Bisonはパーサジェネレータといって、構文のBNFとそれに付随する処理―つまり本質部分のみ―を書くだけで構文解析器を生成してくれるツールです。Bisonは、TechFULでたまに出てくるような形式の構文解析問題には非常にうまく当てはめることができます。

その例として、プラクティス問題の[「ブーリアンハッキング3」](https://techful-programming.com/user/practice/problem/coding/17664)を解いてみましょう。この問題にはそこそこ面倒な構文解析が出てきますが、Bisonを使うことで非常に楽に書くことができます。

なお、ここでは対象の言語としてC++を扱います。しかし、Bisonは他のいくつかの言語にも対応しており、その他の言語でも類似のツールは存在するため、おそらく参考になるでしょう。

## Bisonをインストールする

Bisonが入っていない方は、まずBisonをインストールしてください。以下のいずれかの方法が使えるでしょう。

### cygwin

setup.exeが使えます。

### apt

```bash
$ sudo apt-get update 
$ sudo apt-get upgrade 
$ sudo apt install bison
```

### yum

```bash
# yum install bison
```

### brew

```bash
$ brew install bison
```

### ソースから

予め[m4をインストール](http://www.bnote.net/linux/compile.shtml)しておく必要があります。

```bash
$ wget http://ftp.gnu.org/gnu/bison/bison-3.8.tar.gz
$ tar x bison-3.8.tar.gz
$ cd bison-3.8
$ ./configure
$ make
$ sudo make install
```

## 全体の流れ

Bisonでは、自動的に定義される`yy::parse`関数を通して構文解析器を呼び出すことで構文解析を行います。
構文解析器は`yy::yylex`関数を呼び出すことで入力を受け取ります。

構文解析を行うためには、BNFで構文解析器を書き、さらに`yylex`関数を定義して入力を与える必要があります。

## .yyファイルを書く

`なんとか.yy`といった名前のファイルを作ります。ここでは、TechFULの問題番号から`17664.yy`としておきます。

`yy`ファイルの構造は、次のようになっています。

```text
%{

構文解析器の中で使う関数など (C++)

%}

%%

構文解析器 (BNF)

%%

構文解析器を使うコード (C++)
```

このファイルの中に、まずは普段使うテンプレートなどを`%{`と`%}`で囲んで書いてしまいます。

```cpp
%{

#include <bits/stdc++.h>
using namespace std;

%}

%%

%%

```

## 構文解析器を書く

さらに、`%%`でファイルを区切って、BNFを書きます。問題のEBNFが与えられているので、それをまずはBNFに変換してしまいます。

```cpp
%{

#include <bits/stdc++.h>
using namespace std;

%}

%%

formula   : '(' clause ')' | '(' clause ')' '&' formula;
clause    : literal | literal '^' clause;
literal   : '!' var | var;
var       : '$' varID;
varID     : digit1to9;
          | varID digit;
digit     : '0' | digit1to9;
digit1to9 : '1' | '2' | '3' | '4' | '5' | '6' | '7' | '8' | '9';

%%
```

これで構文解析器の骨格が完成しました。

## スキャナを書く

構文解析器を使うには、入力を与える必要があります。入力はスキャナによって与えることができます。

ここでは、文字列を構文解析するためのスキャナを書きます。そのために、`yy::yylex`関数と`yy::parser`のコンストラクタを定義します。

```cpp
namespace yy {
  class parser {
    string input;
    int offset;

    parser(string input) : input(input), offset(0) {}

    // 文字または-1を返す
    int read() {
      if (offset == input.size()) return -1;
      return input[offset++];
    }
  }

  auto yylex() -> parser::symbol_type {
    int c = parser::read();
    
  }
}
```

入力処理の中では、



```bison
%require "3.0.4"
%language "c++"

%{

#include <bits/stdc++.h>
using namespace std;


namespace yy {
  auto yylex() {
    
  }
}

%}

%%

formula   : '(' clause ')' | '(' clause ')' '&' formula;
clause    : literal | literal '^' clause;
literal   : '!' var | var;
var       : '$' varID;
varID     : digit1to9;
          | varID digit;
digit     : '0' | digit1to9;
digit1to9 : '1' | '2' | '3' | '4' | '5' | '6' | '7' | '8' | '9';

%%

```