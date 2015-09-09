# Hello World


コンソールに "HelloWorld!!" と文字列を表示するプログラムを書いてみましょぅ。



#### 1. DartPadを開く
 https://dartpad.dartlang.org/
 

#### 2. プログラムを書く

```
void main() {
  print('Hello World (1) !!');
  print("Hello World (2) !!");
  print("""
  Hello
  World (3) !!""");
}
```

#### 3. RUNボタンを押す。

```
HTML OUTPUT
CONSOLE
Hello World (1) !!
Hello World (2) !!
  Hello
  World (3) !!
```

と文字列が表示されます。


# 四則演算


#### 1. DartPadを開く
 https://dartpad.dartlang.org/
 

#### 2. プログラムを書く

```
void main() {
  print("""
  1+1  = ${1+1}
  1-1  = ${1-1}
  2*2  = ${2*2}
  4/2  = ${4/2}
  5/3  = ${5/3}
  5~/3 = ${5~/3}
  5%3  = ${5%3}
  5/0  = ${5/0}
  1+2*(1+2)  = ${1+2*(1+2)}

  1==1 = ${1==1}
  1==0 = ${1==0}
  1!=1 = ${1!=1}
  1!=0 = ${1!=0}

  1<0  = ${1<0}
  0<0  = ${0<0}
  -1<0 = ${-1<0}
  1>0  = ${1>0}
  0>0  = ${0>0}
  -1>0 = ${-1>0}
  
  1<=0  = ${1<=0}
  0<=0  = ${0<=0}
  -1<=0 = ${-1<=0}
  1>=0  = ${1>=0}
  0>=0  = ${0>=0}
  -1>=0 = ${-1>=0}
  """);
}
```

#### 3. RUNボタンを押す。
```
  1+1  = 2
  1-1  = 0
  2*2  = 4
  4/2  = 2
  5/3  = 1.6666666666666667
  5~/3 = 1
  5%3  = 2
  5/0  = Infinity
  1+2*(1+2)  = 7

  1==1 = true
  1==0 = false
  1!=1 = false
  1!=0 = true

  1<0  = false
  0<0  = false
  -1<0 = true
  1>0  = true
  0>0  = false
  -1>0 = false
  
  1<=0  = false
  0<=0  = true
  -1<=0 = true
  1>=0  = true
  0>=0  = true
  -1>=0 = false
```
と文字列が表示されます。


# 変数

#### 1. DartPadを開く
 https://dartpad.dartlang.org/
 

#### 2. プログラムを書く

```
void main() {
  int a = 1;
  double b = 1.1;
  int c = (a+b).toInt();
  double d = a+b;
  var e = a+b;
  String f = "test";
  String g = "game";
  print("${c} ${d} ${e} ${f+g}");
}
```

#### 3. RUNボタンを押す。
```
2 2.1 2.1 testgame
```
と文字列が表示されます。

# スコープ

#### 1. DartPadを開く
 https://dartpad.dartlang.org/
 

#### 2. プログラムを書く

```
int a = 100;
int b = 200;
void main() {
  print("[A] ${a} ${b}");
  int a = 1;
  print("[B] ${a} ${b}");
  {
    int  b = 2;
    print("[C] ${a} ${b}");
  }
  print("[D] ${a} ${b}");
  {
    a = 1000;
    b = 2000;
  }
  print("[E] ${a} ${b}");
}
```

#### 3. RUNボタンを押す。
```
[A] 100 200
[B] 1 200
[C] 1 2
[D] 1 200
[E] 1000 2000
```
と文字列が表示されます。

# Function


#### 1. DartPadを開く
 https://dartpad.dartlang.org/
 

#### 2. プログラムを書く

```
int plus(int a, int b) {
  return a + b;
}

void main() {
  print("${plus(100,10)}");
}
```

#### 3. RUNボタンを押す。
```
110
```
と文字列が表示されます。


# Class


#### 1. DartPadを開く
 https://dartpad.dartlang.org/
 

#### 2. プログラムを書く

```
class A {
  int a = 0;
  int b = 0;
  
  printStatus() {
    print("${a} ${b}");
  }
}

void main() {
  A a = new A();
  a.printStatus();
  a.a = 100;
  a.b = 200;
  a.printStatus();
}
```

#### 3. RUNボタンを押す。
```
0 0
100 200
```
と文字列が表示されます。