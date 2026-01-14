<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# in ps , let's say I have function thousandObjects that returns 100000 objects. Is it better to store it directly to variable (array) with "$array = functionX" or to collect it into collection with "$collection = New-Object Collections.Generic.List[PSCustomObject] ; functionX | % { $collection.add($_ }" ?

**Direct assignment to an array variable is significantly better for storing the output of a function returning 100,000 objects.** The pipeline `ForEach-Object` approach with `.Add()` incurs substantial overhead from processing each object individually through PowerShell's pipeline engine, which can be 5-100x slower depending on the system and object complexity.[^1_1][^1_2][^1_3]

## Performance Comparison

Direct assignment leverages PowerShell's optimized collection of pipeline outputs into an `object[]` array with minimal overhead.[^1_4][^1_5] In contrast, `functionX | ForEach-Object { $collection.Add($_) }` invokes scriptblock execution per object, leading to high costs for large sets like 100k items.[^1_3][^1_1]


| Method | Relative Speed (Large Input) | Memory/Overhead | Notes |
| :-- | :-- | :-- | :-- |
| `$array = functionX` [^1_2][^1_6] | Fastest (baseline) | Low, fixed array | Native PowerShell optimization |
| List + pipeline `.Add()` [^1_3][^1_1] | 5-115x slower | Higher due to boxing | Avoid `ForEach-Object` on large pipelines |
| List + `AddRange($array)` [^1_2] | Near baseline | Dynamic growth | Use only if later modifications needed |

## When to Use List

Generic lists excel for dynamic operations like frequent adds/removals after initial population, avoiding array resizing costs. First capture as array (`$array = functionX`), then `$list = [Collections.Generic.List[PSCustomObject]]@($array)` or `$list.AddRange($array)` for efficiency.[^1_7][^1_2][^1_8]

## Recommendation

Stick with `$array = thousandObjects` unless planning heavy modifications—arrays offer faster iteration and lower memory for read-only or static use. Test with `Measure-Command` for your specific objects and PowerShell version.[^1_9][^1_8]
<span style="display:none">[^1_10][^1_11][^1_12][^1_13][^1_14][^1_15][^1_16][^1_17][^1_18][^1_19][^1_20][^1_21][^1_22][^1_23][^1_24][^1_25][^1_26][^1_27][^1_28][^1_29]</span>

<div align="center">⁂</div>

[^1_1]: https://www.perplexity.ai/search/bac3cb3a-a032-450c-976f-990cbe36a0a1

[^1_2]: https://learn.microsoft.com/en-us/powershell/scripting/dev-cross-plat/performance/script-authoring-considerations?view=powershell-7.5

[^1_3]: https://powershell.one/tricks/performance/pipeline

[^1_4]: https://github.com/PowerShell/PowerShell/discussions/18601

[^1_5]: https://powershell.one/tricks/performance/arrays

[^1_6]: https://www.perplexity.ai/search/c400f141-0798-4831-bf92-1c26f8086931

[^1_7]: https://www.perplexity.ai/search/71261c0f-47ca-468f-8dee-c8fcf5ad0561

[^1_8]: https://www.reddit.com/r/PowerShell/comments/ubx3n4/when_to_use_array_vs_array_list_vs_list/

[^1_9]: programming.powershell

[^1_10]: https://www.perplexity.ai/search/8b7bd55f-aee5-488b-86e0-5e300f9a71b1

[^1_11]: https://witit.blog/array-vs-arraylist-powershell/

[^1_12]: https://www.blimped.nl/quick-tip-powershell-performance/

[^1_13]: https://www.youtube.com/watch?v=W9a8NJTqQS4

[^1_14]: https://www.scriptrunner.com/blog-admin-architect/optimize-powershell-arrays

[^1_15]: https://stackoverflow.com/questions/454916/performance-of-arrays-vs-lists

[^1_16]: https://myitforum.substack.com/p/powershell-performance-optimization

[^1_17]: https://vexx32.github.io/2020/02/15/Building-Arrays-Collections/

[^1_18]: https://forums.powershell.org/t/difference-between-piping-commands-and-using-foreach-object/16992

[^1_19]: https://forums.powershell.org/t/script-performance-storing-10k-in-array/10007

[^1_20]: https://www.idera.com/blogs/using-efficient-lists-in-powershell/

[^1_21]: https://www.reddit.com/r/PowerShell/comments/1j4v1tp/operator_its_also_faster_than_using_the_listtaddt/

[^1_22]: https://www.reddit.com/r/PowerShell/comments/6yogs2/functions_that_return_arrays/

[^1_23]: https://stackoverflow.com/questions/51264867/why-does-powershell-collect-uncaptured-values-and-returns-it-as-an-array

[^1_24]: https://github.com/powershell/powershell/issues/26672

[^1_25]: https://stackoverflow.com/questions/60708578/why-should-i-avoid-using-the-increase-assignment-operator-to-create-a-colle

[^1_26]: https://netwrix.com/en/resources/blog/powershell-foreach/

[^1_27]: https://evotec.xyz/powershell-few-tricks-about-hashtable-and-array-i-wish-i-knew-when-i-started/

[^1_28]: https://www.scriptrunner.com/blog-admin-architect/working-efficiently-with-the-ps-pipeline

[^1_29]: https://theposhwolf.com/howtos/PS-Plus-Equals-Dangers/


---

# so it's better to use collection if I plan to dynamically change the collection

Yes, use a generic collection like `List[PSCustomObject]` if you plan to dynamically change it after initial population, such as adding or removing items frequently.[^2_1][^2_2]

## Why Collections for Dynamics

PowerShell arrays are fixed-size, so operations like `+=` or removal create new arrays entirely, causing quadratic time complexity (O(n²)) for many changes on large sets. Generic lists support efficient inserts/deletes (amortized O(1) appends) via capacity growth and native methods.[^2_3][^2_4][^2_5][^2_1]

## Efficient Workflow

- Capture output efficiently: `$array = thousandObjects`[^2_6][^2_1]
- Convert once: `$list = [Collections.Generic.List[PSCustomObject]]@($array)` or `$list.AddRange($array)`[^2_1]
- Modify: `$list.Add($item)` or `$list.RemoveAt(0)` as needed[^2_5]


## Array vs List for Changes

| Operation (on 100k items) | Array Cost | List Cost | Winner |
| :-- | :-- | :-- | :-- |
| Append 1k items [^2_1] | High (resizes) | Low | List |
| Remove middle items [^2_4] | High (shift/copy) | Medium | List |
| Frequent iteration [^2_7] | Low | Medium (no boxing) | Array |
| Static/read-only [^2_8] | Low | Unnecessary | Array |

<div align="center">⁂</div>

[^2_1]: https://learn.microsoft.com/en-us/powershell/scripting/dev-cross-plat/performance/script-authoring-considerations?view=powershell-7.5

[^2_2]: https://www.perplexity.ai/search/71261c0f-47ca-468f-8dee-c8fcf5ad0561

[^2_3]: https://stackoverflow.com/questions/60708578/why-should-i-avoid-using-the-increase-assignment-operator-to-create-a-colle

[^2_4]: https://www.reddit.com/r/PowerShell/comments/ubx3n4/when_to_use_array_vs_array_list_vs_list/

[^2_5]: https://www.idera.com/blogs/using-efficient-lists-in-powershell/

[^2_6]: https://www.perplexity.ai/search/c400f141-0798-4831-bf92-1c26f8086931

[^2_7]: https://www.perplexity.ai/search/8b7bd55f-aee5-488b-86e0-5e300f9a71b1

[^2_8]: https://powershell.one/tricks/performance/arrays


---

# and if I want to do select object with particular props from the array of 1000000 objects with \$array | select ? Is array still better then collection as it's just static/reading?

Yes, arrays remain better (or equivalent) for static reading operations like `$array | Select-Object` on 1 million objects, as both types feed efficiently into PowerShell pipelines.[^3_1][^3_2][^3_3]

## Pipeline Behavior

PowerShell's `Select-Object` processes input via `IEnumerable` enumeration, treating arrays (`object[]`) and generic lists similarly without boxing overhead in typed lists. Arrays often edge out due to faster indexing and lower memory footprint for pure reads.[^3_2][^3_4][^3_5][^3_6]

## Performance Nuances

For massive sets (1M objects), `Select-Object` itself can bottleneck due to property projection costs, regardless of source—test with `Measure-Command`. Direct methods like `$array.Where({$_.Prop -eq 'value'})` or `.Select()` are 10-100x faster than pipeline for filtering/projection.[^3_7][^3_2]


| Operation (1M objects) | Array Speed | List Speed | Notes |
| :-- | :-- | :-- | :-- |
| `$coll | Select-Object Prop1,Prop2` [^3_2][^3_5] | Baseline-fast | Similar | Pipeline equalizes |
| `.Where()/.Select()` [^3_2] | Faster | Faster (typed) | Skip pipeline |
| Iteration speed [^3_4][^3_3] | Faster | Slower if untyped | Use array for reads |

## Optimization Tip

Capture as array first, then convert to list only if dynamic changes follow—avoids unnecessary overhead for read-only `Select-Object`.[^3_1][^3_6] For ultimate speed on large selects, use .NET LINQ: `$selected = $array | ForEach-Object { [PSCustomObject]@{Prop=$_.Prop} }` or array methods.[^3_2]
<span style="display:none">[^3_10][^3_11][^3_12][^3_13][^3_14][^3_15][^3_16][^3_17][^3_8][^3_9]</span>

<div align="center">⁂</div>

[^3_1]: https://learn.microsoft.com/en-us/powershell/scripting/dev-cross-plat/performance/script-authoring-considerations?view=powershell-7.5

[^3_2]: https://www.reddit.com/r/PowerShell/comments/13op70j/performance_issue_reading_through_a_15k_object/

[^3_3]: https://www.perplexity.ai/search/c400f141-0798-4831-bf92-1c26f8086931

[^3_4]: https://witit.blog/array-vs-arraylist-powershell/

[^3_5]: https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/select-object?view=powershell-7.5

[^3_6]: https://www.perplexity.ai/search/8b7bd55f-aee5-488b-86e0-5e300f9a71b1

[^3_7]: https://github.com/PowerShell/PowerShell/issues/11221

[^3_8]: https://stackoverflow.com/questions/74452110/enumerating-large-powershell-object-variable-1-million-plus-members

[^3_9]: https://powershell.one/tricks/performance/arrays

[^3_10]: https://www.reddit.com/r/PowerShell/comments/1kv0dsj/powershell_751_list/

[^3_11]: https://www.youtube.com/watch?v=W9a8NJTqQS4

[^3_12]: https://www.reddit.com/r/PowerShell/comments/uiflhs/i_saw_a_note_somewhere_likely_in_microsoftdotcom/

[^3_13]: https://www.blimped.nl/quick-tip-powershell-performance/

[^3_14]: https://www.reddit.com/r/PowerShell/comments/1dn2y76/i_wish_powershell_array_was_actually_a_generic/

[^3_15]: https://lazyadmin.nl/powershell/select-object/

[^3_16]: https://jhoneill.github.io/powershell/2019/11/10/Arrays-DontSweatSmallStuff.html

[^3_17]: https://www.reddit.com/r/PowerShell/comments/y3rnnn/arraylist_vs_genericlist/


---

# and what about \$list.AddRange(thousandObjects)

Yes, `$list.AddRange(thousandObjects)` is an efficient way to populate a generic list from a function returning 100k+ objects, with performance near direct array assignment.[^4_1][^4_2][^4_3]

## How AddRange Works

`AddRange` accepts `IEnumerable`, collects `thousandObjects` into an optimized batch append (single capacity resize if needed), avoiding per-object `.Add()` overhead. Function output unrolls to individual objects or array, but .NET handles it seamlessly without extra boxing in typed lists.[^4_3][^4_4][^4_5][^4_1]

## Comparison to Alternatives

Direct `$array = thousandObjects` remains fastest for static use, but `AddRange` adds negligible cost (~5-20% slower) while enabling dynamics.[^4_6][^4_2]


| Method | Relative Speed (100k items) | Use Case | Notes |
| :-- | :-- | :-- | :-- |
| `$array = thousandObjects` [^4_2] | Fastest | Static/read-only | Baseline |
| `$list.AddRange(thousandObjects)` [^4_3][^4_1] | Near baseline | Dynamic changes | Efficient bulk add |
| `$list.Add()` in loop [^4_6][^4_4] | 10-20x slower | Never | Per-item overhead |
| `[List[PSCustomObject]]@($array)` [^4_2] | Similar to AddRange | Copy-based | Good alternative |

## Best Practice

Pre-allocate capacity for known sizes: `$list = [Collections.Generic.List[PSCustomObject]]::new(100000); $list.AddRange(thousandObjects)` to eliminate resizes. Use only if list mutations follow; otherwise, array suffices.[^4_2][^4_4][^4_7]
<span style="display:none">[^4_10][^4_11][^4_12][^4_13][^4_14][^4_15][^4_16][^4_8][^4_9]</span>

<div align="center">⁂</div>

[^4_1]: https://stackoverflow.com/questions/24678875/list-addrange-performance-of-adding-array-vs-list

[^4_2]: https://learn.microsoft.com/en-us/powershell/scripting/dev-cross-plat/performance/script-authoring-considerations?view=powershell-7.5

[^4_3]: https://vexx32.github.io/2020/02/15/Building-Arrays-Collections/

[^4_4]: https://baldsolutions.com/index.php/2022/02/27/add-vs-addrange-speed-up-your-lists-concatenation/

[^4_5]: https://www.reddit.com/r/PowerShell/comments/p1836w/lists_and_functions/

[^4_6]: https://www.reddit.com/r/PowerShell/comments/1j4v1tp/operator_its_also_faster_than_using_the_listtaddt/

[^4_7]: https://www.perplexity.ai/search/8b7bd55f-aee5-488b-86e0-5e300f9a71b1

[^4_8]: https://www.blimped.nl/quick-tip-powershell-performance/

[^4_9]: https://github.com/MicrosoftDocs/PowerShell-Docs/issues/9997

[^4_10]: https://forums.powershell.org/t/fill-array-quickly/20217

[^4_11]: https://stackoverflow.com/questions/54434601/powershell-functions-and-performance

[^4_12]: https://evotec.xyz/powershell-few-tricks-about-hashtable-and-array-i-wish-i-knew-when-i-started/

[^4_13]: https://www.reddit.com/r/PowerShell/comments/13op70j/performance_issue_reading_through_a_15k_object/

[^4_14]: https://www.youtube.com/watch?v=srhF9iwv0UM

[^4_15]: https://theposhwolf.com/howtos/PS-Plus-Equals-Dangers/

[^4_16]: https://stackoverflow.com/questions/74452110/enumerating-large-powershell-object-variable-1-million-plus-members


---

# and arraylist ? direct assignement to array instead of arraylist is still better?

Yes, direct assignment to an array (`$array = thousandObjects`) is still better than using an ArrayList for initial storage of large outputs like 100k objects.[^5_1][^5_2][^5_3]

## ArrayList Drawbacks

ArrayList (non-generic) boxes all elements to `object`, causing unboxing overhead during iteration/pipelines (5-20% slower reads than arrays). Direct function assignment produces optimized `object[]` arrays natively, with no boxing and faster enumeration.[^5_2][^5_4][^5_5][^5_1]

## Performance Breakdown

For static use, arrays win on speed and memory; ArrayList only helps dynamics but lags generics.[^5_6][^5_7]


| Aspect | Array (`object[]`) | ArrayList | Notes |
| :-- | :-- | :-- | :-- |
| Initial assignment [^5_1] | Fastest | Slower (copy + box) | Direct wins |
| Iteration/Select-Object [^5_2][^5_3] | Faster indexing | Boxing cost | Array 10-20% faster |
| Dynamic adds [^5_8] | Poor (+= resizes) | Better | But prefer generic List |
| Memory [^5_2] | Compact | Higher (references) | Array efficient |

## Guidance

Avoid ArrayList entirely—use generic `List[PSCustomObject]` for dynamics (no boxing). Capture as array first, then `$al = [ArrayList]@($array)` if needed, but generics outperform.[^5_9][^5_7][^5_10][^5_2]
<span style="display:none">[^5_11][^5_12][^5_13][^5_14][^5_15][^5_16][^5_17][^5_18]</span>

<div align="center">⁂</div>

[^5_1]: https://learn.microsoft.com/en-us/powershell/scripting/dev-cross-plat/performance/script-authoring-considerations?view=powershell-7.5

[^5_2]: https://www.reddit.com/r/PowerShell/comments/ubx3n4/when_to_use_array_vs_array_list_vs_list/

[^5_3]: https://www.perplexity.ai/search/c400f141-0798-4831-bf92-1c26f8086931

[^5_4]: https://www.youtube.com/watch?v=m7VjLcqVpMc

[^5_5]: https://www.perplexity.ai/search/8b7bd55f-aee5-488b-86e0-5e300f9a71b1

[^5_6]: https://witit.blog/array-vs-arraylist-powershell/

[^5_7]: https://github.com/MicrosoftDocs/PowerShell-Docs/issues/9997

[^5_8]: https://stealthfield.wordpress.com/2015/08/21/powershell-array-vs-arraylist-performance/

[^5_9]: https://vexx32.github.io/2020/02/15/Building-Arrays-Collections/

[^5_10]: https://www.perplexity.ai/search/bc085559-29ed-43ee-b7d1-217ff9fef949

[^5_11]: https://www.reddit.com/r/PowerShell/comments/1j4v1tp/operator_its_also_faster_than_using_the_listtaddt/

[^5_12]: https://stackoverflow.com/questions/54208763/for-powershell-arraylist-which-is-faster-add-or-operator

[^5_13]: https://www.scriptrunner.com/blog-admin-architect/optimize-powershell-arrays

[^5_14]: https://stackoverflow.com/questions/23751135/in-c-sharp-which-one-contains-boxing-and-unboxing-strategy-array-or-arraylist

[^5_15]: https://www.youtube.com/watch?v=W9a8NJTqQS4

[^5_16]: https://www.scriptrunner.com/blog-admin-architect/powershell-array-types

[^5_17]: https://www.itprotoday.com/powershell/untitled

[^5_18]: https://learn.microsoft.com/en-us/powershell/scripting/learn/deep-dives/everything-about-arrays?view=powershell-7.5


---

# When is ArrayList faster than a typed List

ArrayList is rarely faster than a typed generic `List[T]` (like `List[PSCustomObject]`) in PowerShell, with generics consistently outperforming due to no boxing overhead.[^6_1][^6_2][^6_3]

## Key Reasons Generics Win

Typed lists avoid boxing value types to `object` (ArrayList's storage), enabling faster access, iteration, and lower memory—especially noticeable on large collections or primitives (e.g., ints). Benchmarks show generics 1.5-2x faster for iteration/adds in homogeneous data.[^6_4][^6_5][^6_2][^6_6]

## Rare Edge Cases

ArrayList may appear marginally faster (~2x in isolated prime sieves) for tiny mixed-type sets or specific iteration patterns pre-.NET optimizations, but results vary by code/hardware. These cases don't hold in modern PowerShell (7+), where generics excel universally.[^6_5][^6_7][^6_3]


| Scenario | ArrayList Edge? | Winner | Notes |
| :-- | :-- | :-- | :-- |
| Large typed data adds/iteration [^6_2] | No (13-24x slower builds) | Generic List | No boxing |
| Mixed types, small sets [^6_5] | Possible (anecdotal 2x) | Test generics | Rare, inconsistent |
| Pipeline/Select-Object [^6_1] | No | Generic List | Less unboxing |
| Dynamic heterogeneous [^6_4] | Equivalent to List[object] | Generic anyway | Use typed |

## Recommendation

Default to `[Collections.Generic.List[PSCustomObject]]`—ArrayList is legacy, error-prone (`.Add()` returns index), and slower overall. Profile with `Measure-Command` for your workload; generics win 99% of cases.[^6_2][^6_3][^6_6][^6_8]
<span style="display:none">[^6_10][^6_11][^6_12][^6_13][^6_14][^6_15][^6_16][^6_9]</span>

<div align="center">⁂</div>

[^6_1]: https://www.reddit.com/r/PowerShell/comments/ubx3n4/when_to_use_array_vs_array_list_vs_list/

[^6_2]: https://github.com/MicrosoftDocs/PowerShell-Docs/issues/9997

[^6_3]: https://learn.microsoft.com/en-us/powershell/scripting/dev-cross-plat/performance/script-authoring-considerations?view=powershell-7.5

[^6_4]: https://www.reddit.com/r/PowerShell/comments/y3rnnn/arraylist_vs_genericlist/

[^6_5]: https://www.reddit.com/r/PowerShell/comments/c95v8d/arraylist_faster_than_generic_list/

[^6_6]: https://www.reddit.com/r/PowerShell/comments/8ecvbz/deleted_by_user/

[^6_7]: https://witit.blog/array-vs-arraylist-powershell/

[^6_8]: https://www.perplexity.ai/search/bc085559-29ed-43ee-b7d1-217ff9fef949

[^6_9]: https://powershellfaqs.com/powershell-array-vs-arraylist/

[^6_10]: https://qiita.com/lustm5/items/54574f3c4663dba837ef

[^6_11]: https://stackoverflow.com/questions/10815565/which-is-better-array-arraylist-or-listt-in-terms-of-performance-and-speed

[^6_12]: https://powershell.one/tricks/performance/arrays

[^6_13]: https://www.blimped.nl/quick-tip-powershell-performance/

[^6_14]: https://www.youtube.com/watch?v=m7VjLcqVpMc

[^6_15]: https://www.reddit.com/r/PowerShell/comments/916cns/creating_a_genericlist_from_an_array_or_from/

[^6_16]: https://www.sitepoint.com/community/t/array-vs-arraylist-in-vb-net/2920


---

# when to use arraylist over array>

Use ArrayList over array when building large collections (e.g., 10k+) dynamically with frequent appends/removals, as arrays incur heavy resizing costs via `+=` or similar.[^7_1][^7_2][^7_3]

## Primary Use Case

ArrayList shines for incremental population in loops (e.g., `$al.Add($item)`), avoiding array's O(n²) copy-on-grow—benchmarks show 30-60x speedup for 100k adds. Arrays excel for known-size or static data with fast indexing/iteration.[^7_2][^7_4][^7_5][^7_1]

## Trade-offs

ArrayList supports easy inserts/deletes but adds boxing overhead for reads; prefer generics (`List[T]`) unless mixing types wildly.[^7_6][^7_1]


| Scenario | ArrayList Better | Array Better | Example |
| :-- | :-- | :-- | :-- |
| Frequent `+=`/appends (large) [^7_5] | Yes (dynamic) | No (resizes) | Log collection |
| Inserts/removals [^7_3] | Yes | No (filter rebuild) | Filtered results |
| Static/iteration heavy [^7_1] | No | Yes | Pipeline input |
| Known size upfront | No | Yes | Pre-sized `@(1..100k)` |

## Modern Preference

Favor `[Collections.Generic.List[PSCustomObject]]` over ArrayList for type safety and speed; ArrayList is legacy for dynamics only when generics don't fit. Always profile large cases with `Measure-Command`.[^7_7][^7_8][^7_1]
<span style="display:none">[^7_10][^7_11][^7_12][^7_13][^7_14][^7_15][^7_16][^7_17][^7_9]</span>

<div align="center">⁂</div>

[^7_1]: https://www.reddit.com/r/PowerShell/comments/ubx3n4/when_to_use_array_vs_array_list_vs_list/

[^7_2]: https://witit.blog/array-vs-arraylist-powershell/

[^7_3]: https://www.spguides.com/powershell-arraylist/

[^7_4]: https://laurentvanacker.com/index.php/2019/12/09/powershell-array-vs-arraylist-performance/

[^7_5]: https://adamtheautomator.com/powershell-array/

[^7_6]: https://stackoverflow.com/questions/34109398/powershell-arrays-when-to-use-them-when-to-avoid-them-and-problems-using-them

[^7_7]: https://github.com/MicrosoftDocs/PowerShell-Docs/issues/9997

[^7_8]: https://learn.microsoft.com/en-us/powershell/scripting/dev-cross-plat/performance/script-authoring-considerations?view=powershell-7.5

[^7_9]: https://www.youtube.com/watch?v=kpYCCs_sgW4

[^7_10]: https://blog.enterprisedna.co/powershell-arraylist/

[^7_11]: https://powershellfaqs.com/powershell-array-vs-arraylist/

[^7_12]: https://www.reddit.com/r/PowerShell/comments/dp6s1p/build_better_scripts_with_powershell_arraylists/

[^7_13]: https://www.reddit.com/r/PowerShell/comments/47efv0/array_vs_arraylist_string_vs_stringbuilder/

[^7_14]: https://www.reddit.com/r/PowerShell/comments/pfcvn2/update_scripts_to_use_arraylist_instead_of_array/

[^7_15]: https://www.youtube.com/watch?v=AqV8FaZGebI

[^7_16]: https://powershell.one/tricks/performance/arrays

[^7_17]: https://www.reddit.com/r/PowerShell/comments/j7eesb/best_practice_for_arrays_and_powershell_objects/

