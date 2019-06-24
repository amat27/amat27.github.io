---
layout: post
comments: true
title:  "Performance of foreach and for On Arrays in C#"
date:   2019-06-24 16:15:00 +0800
categories: .net
---
One day I discussed with one of my teammates about the performance difference of looping an array between using `foreach` and `for` in C#. Then I did some experiments and try to explain the result I observed.

*NOTICE: All the experiments are completed in release mode*

# Plain Loop
The first experiment is to flip a boolean value inside loops. This is to prevent unwanted JIT optimization.

```csharp
int[] ints = Enumerable.Range(0, 100000).ToArray();
bool b = true;

Stopwatch st = Stopwatch.StartNew();

// Test performance of for loop
for (int j = 1; j != 10000; ++j)
{
       foreach (var i in ints)
       {              
              b = !b;
       }
}
st.Stop();
Console.WriteLine(st.ElapsedMilliseconds);

st.Restart();
// Test performance of foreach loop
for (int j = 1; j != 10000; ++j)
{
       for (int i = 0; i != ints.Length; ++i)
       {              
              b = !b;
       }
}
st.Stop();
Console.WriteLine(st.ElapsedMilliseconds);
```

And the results are like

```csharp
860 // foreach loop result
844 // for loop result
```

The `for` version is a little faster. The reason is in the IL code for both tests.

```csharp
// foreach test
IL_0025:  ldloc.s     04 // ints
IL_0027:  ldloc.s     05 // 0
IL_0029:  ldelem.i4   
IL_002A:  pop         
IL_002B:  ldloc.0     // b
IL_002C:  ldc.i4.0    
IL_002D:  ceq         
IL_002F:  stloc.0     // b
IL_0030:  ldloc.s     05 
IL_0032:  ldc.i4.1    
IL_0033:  add         
IL_0034:  stloc.s     05 
IL_0036:  ldloc.s     05 
IL_0038:  ldloc.s     04 
IL_003A:  ldlen       
IL_003B:  conv.i4     
IL_003C:  blt.s       IL_0025

// for test
IL_006B:  ldloc.0     // b
IL_006C:  ldc.i4.0    
IL_006D:  ceq         
IL_006F:  stloc.0     // b
IL_0070:  ldloc.s     07 // i
IL_0072:  ldc.i4.1    
IL_0073:  add         
IL_0074:  stloc.s     07 // i
IL_0076:  ldloc.s     07 // i
IL_0078:  ldloc.1     // ints
IL_0079:  ldlen       
IL_007A:  conv.i4     
IL_007B:  bne.un.s    IL_006B
```

As we can see, the generated code for the for loop doesn't actually load the content of the array. But `foreach` load current element in every step (`ldelem.i4`).

# Reading Array Content in Loop

So I changed the test code a little bit.

```csharp
int[] ints = Enumerable.Range(0, 100000).ToArray();
long dum = 0;
bool b = true;

Stopwatch st = Stopwatch.StartNew();

// Test performance of for loop
for (int j = 1; j != 10000; ++j)
{
       foreach (var i in ints)
       {
              dum += i; // use the value of _ints_ in loop body
              b = !b;
       }
}
st.Stop();
Console.WriteLine(st.ElapsedMilliseconds);

st.Restart();
// Test performance of foreach loop
for (int j = 1; j != 10000; ++j)
{
       for (int i = 0; i != ints.Length; ++i)
       {
              dum += ints[i]; // use the value of _ints_ in loop body
              b = !b;
       }
}
st.Stop();
Console.WriteLine(st.ElapsedMilliseconds);
```

And the results are almost the same now

```csharp
597 // foreach loop result
599 // for loop result
```

If we check the IL again, we can see the only difference now is foreach reads array element earlier.
```csharp
// foreach test
IL_0026:  ldloc.s     04 // ints
IL_0028:  ldloc.s     05 // 0
IL_002A:  ldelem.i4   
IL_002B:  stloc.s     06 // i
IL_002D:  ldloc.1     // dum
IL_002E:  ldloc.s     06 // i
IL_0030:  conv.i8     
IL_0031:  add         
IL_0032:  stloc.1     // dum
IL_0033:  ldloc.s     05 
IL_0035:  ldc.i4.1    
IL_0036:  add         
IL_0037:  stloc.s     05 
IL_0039:  ldloc.s     05 
IL_003B:  ldloc.s     04 
IL_003D:  ldlen       
IL_003E:  conv.i4     
IL_003F:  blt.s       IL_0026

// for test
IL_006E:  ldloc.1     // dum
IL_006F:  ldloc.0     // ints
IL_0070:  ldloc.s     08 // i
IL_0072:  ldelem.i4   
IL_0073:  conv.i8     
IL_0074:  add         
IL_0075:  stloc.1     // dum
IL_0076:  ldloc.s     08 // i
IL_0078:  ldc.i4.1    
IL_0079:  add         
IL_007A:  stloc.s     08 // i
IL_007C:  ldloc.s     08 // i
IL_007E:  ldloc.0     // ints
IL_007F:  ldlen       
IL_0080:  conv.i4     
IL_0081:  bne.un.s    IL_006E
```

# Conclusion
If we try to loop an array and use its values, using `for` will not provide any performance benefit. If we use an array in the loop only as looping time indicator (which is an anti-pattern itself), then using `for` will be slightly faster then `foreach`.