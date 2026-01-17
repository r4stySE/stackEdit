<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# \$array = thousandObjects vs \$arrayList = [System.Collections.ArrayList]@(thousandObjects )

Direct assignment `$array = thousandObjects` is faster and more efficient than `$arrayList = [System.Collections.ArrayList]@(thousandObjects)` for initial capture of large outputs.[^1][^2][^3]

## Key Differences

`$array` stores as optimized `object[]` natively from PowerShell's pipeline collection, with zero extra steps. The ArrayList cast first creates the array via `@()`, then copies elements into ArrayList (boxing non-objects), adding 10-30% time/memory overhead for 100k+ items.[^2][^4][^1]

## Performance Impact

Benchmarks confirm arrays faster for reads/iteration; cast incurs copy cost but enables dynamics if needed later.[^5][^1]


| Method | Time Overhead (100k objs) | Memory | Iteration Speed |
| :-- | :-- | :-- | :-- |
| `$array = thousandObjects` [^2] | Baseline | Low | Fastest |
| `$al = [ArrayList]@(thousandObjects)` [^4][^1] | +10-30% (copy/box) | Higher | Slower (boxing) |
| Later cast `$al = [ArrayList]$array` [^6] | Similar | Same | Use post-capture |

## When to Cast

Use ArrayList cast only if immediate frequent inserts/deletes follow—otherwise, array suffices and outperforms. Prefer generic `List[PSCustomObject]` for type safety/speed over ArrayList.[^7][^8][^1]
<span style="display:none">[^10][^11][^12][^13][^14][^15][^16][^17][^18][^19][^9]</span>

<div align="center">⁂</div>

[^1]: https://www.reddit.com/r/PowerShell/comments/ubx3n4/when_to_use_array_vs_array_list_vs_list/

[^2]: https://learn.microsoft.com/en-us/powershell/scripting/dev-cross-plat/performance/script-authoring-considerations?view=powershell-7.5

[^3]: https://www.perplexity.ai/search/c400f141-0798-4831-bf92-1c26f8086931

[^4]: https://www.sharepointdiary.com/2021/11/powershell-arraylist-beginners-guide.html

[^5]: https://witit.blog/array-vs-arraylist-powershell/

[^6]: https://www.perplexity.ai/search/8e72de3d-74c6-4fd6-a22c-05a479b82d7d

[^7]: https://www.perplexity.ai/search/8b7bd55f-aee5-488b-86e0-5e300f9a71b1

[^8]: https://github.com/MicrosoftDocs/PowerShell-Docs/issues/9997

[^9]: https://stackoverflow.com/questions/54208763/for-powershell-arraylist-which-is-faster-add-or-operator

[^10]: https://vexx32.github.io/2020/02/15/Building-Arrays-Collections/

[^11]: https://www.scriptrunner.com/blog-admin-architect/optimize-powershell-arrays

[^12]: https://www.varonis.com/blog/powershell-array

[^13]: https://learn-powershell.net/2014/09/21/quick-hits-adding-items-to-an-array-and-a-look-at-performance/

[^14]: https://stackoverflow.com/questions/42413265/powershell-comparison-operator-contain-vs-large-array

[^15]: https://www.youtube.com/watch?v=m7VjLcqVpMc

[^16]: https://quizzesforyou.hashnode.dev/array-vs-arraylist-in-powershell

[^17]: https://forums.powershell.org/t/system-collections-arraylist-overload/1078

[^18]: https://evotec.xyz/powershell-few-tricks-about-hashtable-and-array-i-wish-i-knew-when-i-started/

[^19]: https://forums.powershell.org/t/arraylist-c-vs-powershell-is-it-the-same-thing-or-not/7443

