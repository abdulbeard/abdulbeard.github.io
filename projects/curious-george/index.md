---
layout: project
title: Curious George
navbarlocation: projects
---

# About
This project is about a c#/.NET monkey-testing library I wrote that lets you test your code by including an attribute on your tests. Supports MSTest, NUnit and XUnit.

There's a lot more detail and use-cases to be found on the [project readme]("https://github.com/abdulbeard/monkey_testing/blob/master/README.md").

> [Monkey Testing](https://en.wikipedia.org/wiki/Monkey_testing) and Random Testing are not new concepts. They are useful for testing your code beyond your cognitive blindspots and assumptions for how it is meant to be used/executed.
This library allows you to test your functions with lots of random, ideally troublesome inputs. It tries to approximate easy-to-overlook values of primitive and complex types, that are arguments to the functions you want to test.


# Problem

A while back, I was writing a c# library that would take in all sorts of value and reference types, and do some generic, valuable logic on them. And I realized that I wanted an easy way to test my library, with lots of different kinds of inputs. Like edge-case values for the arguments so I could test if my code was making assumptions.
I had trouble finding an adequate ["Monkey Testing"]("www.google.com") library, and so decided to write it myself (a library for a library - I do it a lot apparently).

# Solutions
I did a [nuget search]("https://www.nuget.org/packages?q=monkey+testing") for "monkey testing" that would let me find similar packges, but I couldn't find any that seemed to fit my needs.

# Where Curious George fits in
Curious George uses reflection to figure out the types of arguments for the function. The four basic variations of values for a given type are the following:

* Null
* Default
* Min
* Max

It uses [AutoFixture](https://github.com/AutoFixture/AutoFixture) for creating random instances of user-defined classes. Here's a table that sheds more light on the types of values generated for different types:
| Types        | Null          | Default       | Min           | Max       |
| ------------- |:-------------:|:-------------:|:-------------:|:-------------:|
| byte           | 0 | default(byte) |byte.MinValue|byte.MaxValue|
| sbyte           | 0 | default(sbyte) |sbyte.MinValue|sbyte.MaxValue|
| int           | 0 | default(int) |int.MinValue|int.MaxValue|
| uint           | 0 | default(uint) |uint.MinValue|uint.MaxValue|
| short           | 0 | default(short) |short.MinValue|short.MaxValue|
| ushort           | 0 | default(ushort) |ushort.MinValue|ushort.MaxValue|
| long           | 0 | default(long) |long.MinValue|long.MaxValue|
| ulong           | 0 | default(ulong) |ulong.MinValue|ulong.MaxValue|
| float           | 0.0f | default(float) |float.MinValue|float.MaxValue|
| double           | 0.0d | default(double) |double.MinValue|double.MaxValue|
| char           | '\0' | default(char) |char.MinValue|char.MaxValue|
| bool           | false | default(bool) |false|false|
| string           | null | default(string) |string.Empty|[GetStringMax()](https://github.com/abdulbeard/monkey_testing/blob/c3337a3240fae6e4fca573f24f968cc5195b4f83/MonkeyTesting/DataVariationsByType.cs#L135)|
| decimal           | 0.0M | default(decimal) |decimal.MinValue|decimal.MaxValue|
| DateTime           | DateTime.MinValue | default(DateTime) |DateTime.MinValue|DateTime.MaxValue|

<br/>

| Rating    | Security            | Explanation                             |
|-----------|---------------------|-----------------------------------------|
| Excellent | Very secure         | Criteria is met optimal                 |
| High      | Secure              | Criteria is met                         |
| Mid       | Insufficient secure | Criteria is met partly                  |
| Low       | Insecure            | Criteria is no met                      |
| N/A       | Not rated           | Criteria is not available or irrelevant |

<br/>

In addition to that, where it finds lists/enumerables, it tries to make null, and empty versions of them to trigger any hidden null reference exceptions, or list length assumptions.

## All Possible Combinations
Curious George also tries to generate all possible combinations of inputs so you can test things in more detail. It will try to generate `distinct` combinations of all your inputs.

For example, for the input argument type `int`, 
* Null => `0`, 
* Default => `default(int)`, 
* Min => `int.MinValue` 
* Max => `int.MaxValue`. 

Now for a function like `FunctionToTest1(int a, int b, int c)` which takes in three ints, each of which can have one of 4 values (Null, Default, Min, Max) - all possible combinations of values is a total of 64 combinations (4 x 4 x 4 = 64).
Since int is a [value type](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/value-types), the Null and Default values for it are both `0`. 
CuriousGeorge will make sure that the result set returned is distinct, and that any given result set is never repeated - which brings the total number of combinations down to 27 (3 x 3 x 3 = 27).
`allPossibleCombinations` is the last argument for the monkey test dataset creation.