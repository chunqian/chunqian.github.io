# string_nuke_static_pool

Destroys the default static string pool behind the `string_t` class implementation.

## Syntax

```cpp
void string_nuke_static_pool();
```

## Remarks

The `string_t` class uses a default static [string pool](https://github.com/RandyGaul/cute_framework/tree/master/doc/string/strpool/README.md). This function operates on that static pool.

## Related Functions

[string_defrag_static_pool](https://github.com/RandyGaul/cute_framework/blob/master/doc/string/string/string_defrag_static_pool.md)  
