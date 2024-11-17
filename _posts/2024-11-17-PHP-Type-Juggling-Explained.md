---
title: PHP Type Juggling - Explained
tags: PHP TypeJuggling string integer float bool
article_header:
  type: 
  image:
   #src: 
sidebar: 
  nav: blogs
---

*Type juggling is one of the feature of php, that automatically detects the datatype of a given value, this blog dicusses the hows and whys of type juggling and how hackers can abuse this feature to juggle past the admin login.*

## Introduction

In PHP, when defining variables, we don't have to specific the data type, PHP itself decides the datatype of the variable, depending upon the value it holds, this is called `Type Juggling`.

```php
php > $a="10";
php > var_dump($a);
string(2) "10"
php > $a=10;
php > var_dump($a);
int(10)
```

When it comes to comparing two values, PHP offers two operators.
- `==` : Loose Comparison : Only the values are compared
- `===` : Strict Comparison : Values and the data type is compared

Now when comparing values, the PHP type juggling behaves differently in different contexts, the context differs based on the datatype.

- If a string and integer is compared, integer is preferred and the string is converted into integer datatype.

```php
php > var_dump(1=="1")
bool(true)
```

- If a string and float is compared, float is preferred and the string is converted into float datatype.

```php
php > var_dump(1.1=="1.1")
bool(true)
```

- If a string and boolean is compared, boolean is preferred and the string is converted into boolean datatype, where it checks the string if it's `NULL` or `!NULL`. If the string is `NULL`, it's boolean conversion will be `false/0` and if it's `!NULL` meaning a value is assigned to it, it's boolean conversion will be `true/1`.

```php
php > $a = 'anything'
php > var_dump($a==true)
bool(true)
```

## Comparing Alphanumerics

This is where things start to get crazy, if we try to compare an integer/float with a alphanumeric string (numeric+alphabet), we get some pretty confusing results.

```php
php > var_dump(1=='1anything');
bool(true)
```

In the above comparison, the output comes out to be true, PHP converts the string into an integer, which turns out to be `1` and the condition is evaluated as true. We can try to do the conversion manually by type casting.

```php
php > var_dump((int) '1anything');
int(1)
```

But if we try to convert an alphanumeric string which does not start with a numeric, we get integer `0`.

```php
php > var_dump(0=='anyth1ng');
bool(true)
```

This means, the php will convert the alphanumeric string to integer `0`, if the string doesn't start with a numeric value.
 
```php
php > var_dump(10=='10anyth1ng');
bool(true)

php > var_dump(0=='1anything');
bool(false)
```

Exponents are also converted into integer before comparison, an exponent is represented using character `e` followed by the exponent value.

```php
1e2 -> (1)^10*10 -> 1
0e4 -> (0)^10*10*10*10 -> 0
```

So if we compare an exponent with an integer.

```php
php > var_dump(0=='0e32r4sadefxwef');
bool(true)
```
## Comparing Strings

When it comes to comparing strings in PHP, there's some weirdness to it.

```php
if ($input < "30") {
        print("true");
}
else {
    print("false");
}

# Numeric Input
$input = "222"
php> false

# Alphanumeric Input
$input = "222a"
php> true
```

- In case of numeric input, PHP compares both the strings as numbers and we get the expected output.
- In the second case, we get output as `true`, meaning `222a` is less than `30`. In this case, PHP doesn't compare them as numbers, but it compares them as strings in ASCII Order (Dictionary Order/lexicographical Order).

```txt
Dec  Char                           Dec  Char     Dec  Char     Dec  Char
---------                           ---------     ---------     ----------
  0  NUL (null)                      32  SPACE     64  @         96  `
  1  SOH (start of heading)          33  !         65  A         97  a
  2  STX (start of text)             34  "         66  B         98  b
  3  ETX (end of text)               35  #         67  C         99  c
  4  EOT (end of transmission)       36  $         68  D        100  d
  5  ENQ (enquiry)                   37  %         69  E        101  e
  6  ACK (acknowledge)               38  &         70  F        102  f
  7  BEL (bell)                      39  '         71  G        103  g
  8  BS  (backspace)                 40  (         72  H        104  h
  9  TAB (horizontal tab)            41  )         73  I        105  i
 10  LF  (NL line feed, new line)    42  *         74  J        106  j
 11  VT  (vertical tab)              43  +         75  K        107  k
 12  FF  (NP form feed, new page)    44  ,         76  L        108  l
 13  CR  (carriage return)           45  -         77  M        109  m
 14  SO  (shift out)                 46  .         78  N        110  n
 15  SI  (shift in)                  47  /         79  O        111  o
 16  DLE (data link escape)          48  0         80  P        112  p
 17  DC1 (device control 1)          49  1         81  Q        113  q
 18  DC2 (device control 2)          50  2         82  R        114  r
 19  DC3 (device control 3)          51  3         83  S        115  s
 20  DC4 (device control 4)          52  4         84  T        116  t
 21  NAK (negative acknowledge)      53  5         85  U        117  u
 22  SYN (synchronous idle)          54  6         86  V        118  v
 23  ETB (end of trans. block)       55  7         87  W        119  w
 24  CAN (cancel)                    56  8         88  X        120  x
 25  EM  (end of medium)             57  9         89  Y        121  y
 26  SUB (substitute)                58  :         90  Z        122  z
 27  ESC (escape)                    59  ;         91  [        123  {
 28  FS  (file separator)            60  <         92  \        124  |
 29  GS  (group separator)           61  =         93  ]        125  }
 30  RS  (record separator)          62  >         94  ^        126  ~
 31  US  (unit separator)            63  ?         95  _        127  DEL
```

There are two rules when comparing strings.
1. Compare the first two digits, if they are different, output the result. Rest of the string are not compared.
2. Compare the first two digits, if they are same, move to the second one, if second digit from both the compared values are different, output the result.

So when we compare `222a` with `30`, the only comparison that takes place is `2<3?`, and since `2` comes before `3` in the ASCII table, the result is `true`. 

If we were to compare `anyth1ng` with `anything`, PHP will have to compare the first 5 digits, before it can decide the answer i.e. `true`. 

*This way of comparing strings is not limited to PHP.*

## Juggling Bug

The issue here is how PHP is comparing a string with a numeric value, i.e. converting the string into an integer/float and then comparing them. To see, how this is an issue, let's look at the following code.

![t](/Images/juggling/1.png){:.shadow}

### Understanding The Code

- **Line 3** : A function `file_get_contents` is used on `php://input` i.e. the input stream which basically means everything in the request body, gets stored it the variable `$body_data`.
- **Line 5** : `json_decode` function runs over `$body_content`, which returns a class object with the values specified in json body.

![t](/Images/juggling/2.png){:.shadow}

- **Line 7** : An associative array is defined, with key username and password. This is used for storing credentials in this case.
- **Line 9-10** : The user object variable is passed to variable `user` and same happens for password.
- **Line 12** : Finally username and password are compared, to authenticate the user.
- If the credentials are correct, the flag is displayed, otherwise `eh....` is displayed.

![t](/Images/juggling/3.png){:.shadow}

### Exploiting The Vulnerability

Since we already know how the type juggling and string comparison works in PHP, in order to bypass authentication, we can simply supply an integer value `0` in the password field, instead of a string.

![t](/Images/juggling/4.png){:.shadow}

PHP will compare integer `0` with string `0e5c3b57b7c94242a9ed18430f1fc579`, according to the rules, it will convert the string into an integer and `0e<anything-here>` is always 0. Hence, true.

*You can play with the code here https://onlinephp.io/c/b9dd8*


The above scenario might not look very realistic, but there are other scenarios as well, where type juggling can be exploited.

![t](/Images/juggling/5.png){:.shadow}

In the above example, we do not know the password, but by specifying a boolean datatype, we are able to get the result as true. One thing to keep in mind is, we won't be able to do the same for `$_POST` or `$_GET` variables, as their return type is a string, until or unless there's some kind of type casting done my the developer. That's why we either need JSON input, or serialised data.

*If you find an application accepting data in any other format, you can try submitting the data in JSON format and check if the application processes it the same way or not.*

## Juggle Safe 

Enough of exploitation, let's talk about how you can secure your php application from type juggling bugs. The above mentioned behaviour was true only until version 8, from PHP version 8 onwards, the fundamental rules of comparing a string with an integer have changed, now it converts the integer into a string, instead of converting a string into an integer.

![t](/Images/juggling/6.png){:.shadow}

That's pretty much everything you need to know about Type Juggling, i hope you enjoyed reading it as much as i loved writing it, we recently hit 100+ subscribers on our youtube channel, and we did a small QnA, PFA video.

<div>{%- include extensions/youtube.html id='B5dUg8GEIRY' -%}</div>

*Stay safe, keep hacking.*

