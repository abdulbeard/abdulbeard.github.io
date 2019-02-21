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
<table class="ui celled striped table">
  <thead>
    <tr>
        <th>Types</th>
        <th>Null</th>
        <th>Default</th>
        <th>Min</th>
        <th>Max</th>
    </tr>
  </thead>
  <tbody>
      <tr>
        <td>byte</td>
        <td> 0 </td>
        <td> default(byte) </td>
        <td>byte.MinValue</td>
        <td>byte.MaxValue</td>
    </tr>
    <tr>
        <td>sbyte</td>
        <td> 0 </td>
        <td> default(sbyte) </td>
        <td>sbyte.MinValue</td>
        <td>sbyte.MaxValue</td>
    </tr>
    <tr>
        <td>int</td>
        <td> 0 </td>
        <td> default(int) </td>
        <td>int.MinValue</td>
        <td>int.MaxValue</td>
    </tr>
    <tr>
        <td>uint</td>
        <td> 0 </td>
        <td> default(uint) </td>
        <td>uint.MinValue</td>
        <td>uint.MaxValue</td>
    </tr>
    <tr>
        <td>short</td>
        <td> 0 </td>
        <td> default(short) </td>
        <td>short.MinValue</td>
        <td>short.MaxValue</td>
    </tr>
    <tr>
        <td>ushort</td>
        <td> 0 </td>
        <td> default(ushort) </td>
        <td>ushort.MinValue</td>
        <td>ushort.MaxValue</td>
    </tr>
    <tr>
        <td>long</td>
        <td> 0 </td>
        <td> default(long) </td>
        <td>long.MinValue</td>
        <td>long.MaxValue</td>
    </tr>
    <tr>
        <td>ulong</td>
        <td> 0 </td>
        <td> default(ulong) </td>
        <td>ulong.MinValue</td>
        <td>ulong.MaxValue</td>
    </tr>
    <tr>
        <td>float</td>
        <td> 0.0f </td>
        <td> default(float) </td>
        <td>float.MinValue</td>
        <td>float.MaxValue</td>
    </tr>
    <tr>
        <td>double</td>
        <td> 0.0d </td>
        <td> default(double) </td>
        <td>double.MinValue</td>
        <td>double.MaxValue</td>
    </tr>
    <tr>
        <td>char</td>
        <td> '\0' </td>
        <td> default(char) </td>
        <td>char.MinValue</td>
        <td>char.MaxValue</td>
    </tr>
    <tr>
        <td>bool</td>
        <td> false </td>
        <td> default(bool) </td>
        <td>false</td>
        <td>false</td>
    </tr>
    <tr>
        <td>string</td>
        <td> null </td>
        <td> default(string) </td>
        <td>string.Empty</td>
        <td>[GetStringMax()]
    </tr>    
  </tbody>
</table>

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

