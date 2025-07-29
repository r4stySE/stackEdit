### Attribute Access in Python with AttrDefault vs PowerShell Hashtables

Yes, using `AttrDefault` from the `attrdict` library allows you to retrieve (and set) key values in a way that's very similar to PowerShell hashtable dot-access, just using dot notation instead of the `$h.key` style used in PowerShell.

#### PowerShell Example

```powershell
$h = @{}
$h.one = 1
$h.two = $h.one
Write-Output $h.two   # Outputs: 1
```
You access or set keys as `$h.one`, `$h.two`.

#### Python with AttrDefault Example

```python
from attrdict import AttrDefault
d = AttrDefault(AttrDefault)
d.one = 1
d.two = d.one
print(d.two)          # Outputs: 1
```
You access or set keys as `d.one`, `d.two`.

#### Key Similarities

- **Dot notation** to access and set keys at all levels, no quoting or brackets needed for valid names.
- Both approaches allow for readable, concise assignment and retrieval of dictionary/hashtable values.

#### Notable Differences

- **Python’s AttrDefault is not built-in**; it must be installed as an external package.
- Attribute access works only for keys that are valid Python attribute names (i.e., no spaces, cannot start with digits, etc.).
- Dictionary methods (like iteration, `d.items()`) still work as expected.

#### Table: Behavior Comparison

| Feature                         | PowerShell Hashtable          | Python AttrDefault                     |
|----------------------------------|------------------------------|----------------------------------------|
| Attribute/key access             | `$h.one`, `$h["one"]`        | `d.one`, `d['one']`                    |
| Nested dot access                | `$h.one.two = 1`             | `d.one.two = 1`                        |
| Auto-creation of sublevels       | Not automatic                | Automatic with `AttrDefault`           |
| Standard in the language         | Yes                          | No (third-party package)               |
| Restriction on key names         | Fewer                         | Must be valid Python identifiers       |

**Summary:**  
`AttrDefault` gives you "hashtable-like" structures in Python that can be accessed using dots, letting you work very similarly to PowerShell’s style for retrieving and setting values, especially for simple and nested keys. The key difference is that this feature isn’t in Python’s standard library—you need the `attrdict` package to get it.

Sources
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE3OTg4NTI0ODRdfQ==
-->