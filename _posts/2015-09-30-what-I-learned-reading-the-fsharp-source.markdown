---
layout: post
title:  "What I Learned Reading the FSharp Source Part One"
date:   2015-09-30
categories: fsharp .net
comments: true
image: /assets/images/cover.jpg
---
As part of my learning experience into the F# language, I’ve taken the time to begin reading through parts of the source code that catches my eye to gain a stronger foothold and understanding of the language that I would usually skim over when reading the MSDN docs.  The nice thing about reading the source code is that they provide tests to describe the functionality.  So most of my time will be spent exploring the tests, some of the source code, and MSDN documentation. Lets begin!

## Arrays:
Arrays are not immutable according to the tests where the Array.set method is used to modify an index within the array.  Once I saw that I immediately went to the MSDN docs and discovered that “Arrays are fixed-size, zero-based, mutable collections of consecutive data elements that are all of the same type.”  I haven’t had a use case for arrays in my work so far, but I can forsee it being useful for collections that will never change size.

{% gist andredublin/40e1f0012bed59ac80ad %} 

## Concat
Arrays can be built from sequence expressions, which I find quite nice to look at. 

{% gist andredublin/90ee3393680cbbe0e654 %} 

We can also combine expressions to build multidimensional arrays, and that Array.concat will concatenate (flatten) an array of arrays the documentation states and provides and example that it only concatenates a sequence of arrays. This confused me at first that the documentation would state a sequence of arrays, but I was reminded that array implements ```IEnumerable<T>```. Looking at the signature of Array.concat:

{% gist andredublin/674f13f5a0152bd51ac6 %} 

We see that it indeed does take a sequence of arrays then Seq.ToArray is called before the concat operation happens.  If we are given an array of arrays then we go straight to the concatArrays method thanks to the match expression.  Where we see the operator ```:?``` that matches the value to the type expression ```('T[][])```

{% gist andredublin/4806e25a5773c6bf5d5f %}
 
Which then calls an internal methods concatArrays, concatAddLengths, and concatBlit to return a result. 

{% gist andredublin/8f6dfd12586f8d66754e %}
 

There is a whole lot of goodness going on in these three functions so let’s dive in!  The first thing that catches my attention is this line: 

{% gist andredublin/a753c0e936c08527b2c1 %}

Where we call zeroCreateUnchecked hiding at:

{% gist andredublin/8993fbd2efdc01be001c %} 

That calls a common Intermediate language instruction (cil) ```# newarr``` that creates a new array of type ‘T of the concatenated array length of all zeros.  Then we call concatBlit with the array of arrays, two counters, and the combined length of the final array.  Within that method we have a check for the ```i``` counter is greater than the number of arrays in the array, assign ```h``` to the “i”th array assign ```len``` to the length of ```h```. Then we call the class method Array.Copy.

{% gist andredublin/fe6e7f4d17400c7358a9%}

Array.Copy then uses the class method Array.Clone to create a shallow copy of the one-dimensional array that is down casted using the operator ```:?>``` to a type of array ```‘T[]```, where originally they were using zeroCreate and a for loop to push the values. Finally the new array ```tgt``` is built recursively until the ```i``` counter equals the number of arrays in the array. Whew! that’s a lot for just Array.concat!

## Sub
Next on to Array.sub where we see some tests for it here:

{% gist andredublin/614b78c27f45248ab328 %}

 Then the module definition here: 

{% gist andredublin/0e93b4871bf98d158c9d %}

Where we first do some validation of the array to make sure it is not null and within the array bounds.  Next we call subUnchecked located at with the startIndex, count, and the array:

 {% gist andredublin/17dd9b0a1a47f3dbf4cc %}

Which it then calls on zeroCreateUnchecked to initialize an empty array, then we check if the size of the desired subarray is less than 64 indexes, and use a for loop if it is or the class method Array.Copy if it is greater.  The reasoning behind this is probably due to the efficiency that a for loop has over arrays less than 64 indexes.

## Folding and Optimized Closures
Now taking a look at module definitions for folding:

{% gist andredublin/ccf620d60d9b9de9dbbc %} 

What really catches my eye is “OptimizedClosures.FSharpFunc”.  So looking at the documentation [here](https://msdn.microsoft.com/en-us/library/ee340450.aspx) I learned that it is used to invoke the implementation of a function.  It reminds me of a C# Func<> delegate, but I imagine there is more going on here.  So we find the optimized closures and fsharp func located [here](https://github.com/andredublin/visualfsharp/blob/0f514efe25899ba59778b5bb522e2724aec44a3d/src/fsharp/FSharp.Core/prim-types.fs#L3236-L3391)
Going back to fold module definition 

{% gist andredublin/bbd7dd7c7bbbdeb9b60f %}

We see the FSharpFunc is invoked to update the mutable state.

Well that's it for part one of my exploration of the fsharp source code.  Please let me know if I missed anything interesting or explanations within my current search.

Comments can be left on [hackernews](https://news.ycombinator.com/item?id=10315733).

Thanks to [poizan42](https://news.ycombinator.com/user?id=poizan42) for the constructive feedback.
