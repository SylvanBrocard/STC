# STC Container [csset](../stc/csmap.h): Sorted Set
![Set](pics/sset.jpg)

A **csset** is an associative container that contains a sorted set of unique objects of type *Key*. Sorting is done using the key comparison function *keyCompare*. Search, removal, and insertion operations have logarithmic complexity. **csset** is implemented as a AA-tree. See [std::set](https://en.cppreference.com/w/cpp/container/set) for a similar c++ class.

## Declaration

```c
#define using_csset(X, Key, keyCompare=c_default_compare,
                            keyDestroy=c_default_del,
                            keyFromRaw=c_default_clone,
                            keyToRaw=c_default_to_raw,                           
                            RawKey=Key)
#define using_csset_str()                           
```
The macro `using_csset()` can be instantiated with 2, 3, 5, or 7 arguments in the global scope.
Default values are given above for args not specified. `X` is a type tag name and
will affect the names of all csset types and methods. E.g. declaring `using_csset(my, int);`, `X` should
be replaced by `my` in all of the following documentation.

`using_csset_str()` is a predefined macro for `using_csset(str, cstr_t, ...)`.

## Types

| Type name            | Type definition                        | Used to represent...     |
|:---------------------|:---------------------------------------|:-------------------------|
| `csset_X`            | `struct { ... }`                       | The csset type           |
| `csset_X_rawkey_t`   | `RawKey`                               | The raw key type         |
| `csset_X_rawvalue_t` | `csset_X_rawkey_t`                     | The raw key type         |
| `csset_X_key_t`      | `Key`                                  | The key type             |
| `csset_X_value_t`    | `csset_X_key_t`                        | The value type           |
| `csset_X_result_t`   | `struct { Key first; bool second; }`   | Result of insert/emplace |
| `csset_X_iter_t`     | `struct { csset_X_value_t *ref; ... }` | Iterator type            |


## Header file

All csset definitions and prototypes may be included in your C source file by including a single header file.

```c
#include "stc/csmap.h"
```
## Methods

```c
csset_X             csset_X_init(void);
csset_X             csset_X_clone(csset_x set);
void                csset_X_clear(csset_X* self);
void                csset_X_swap(csset_X* a, csset_X* b);
void                csset_X_del(csset_X* self);

bool                csset_X_empty(csset_X m);
size_t              csset_X_size(csset_X m);

void                csset_X_push_n(csset_X* self, const RawKey arr[], size_t size);

csset_X_result_t    csset_X_emplace(csset_X* self, RawKey rkey);
csset_X_result_t    csset_X_insert(csset_X* self, RawKey rkey);

size_t              csset_X_erase(csset_X* self, RawKey rkey);
csset_X_iter_t      csset_X_erase_at(csset_X* self, csset_X_iter_t pos);

csset_X_value_t*    csset_X_find(const csset_X* self, RawKey rkey, csset_X_iter_t* it);
bool                csset_X_contains(const csset_X* self, RawKey rkey);

csset_X_iter_t      csset_X_begin(csset_X* self);
csset_X_iter_t      csset_X_end(csset_X* self);
void                csset_X_next(csset_X_iter_t* it);
csset_X_value_t*    csset_X_itval(csset_X_iter_t it);

csset_X_value_t     csset_X_value_clone(csset_X_value_t val);
void                csset_X_value_del(csset_X_value_t* val);
```

## Example
```c
#include <stdio.h>
#include <stc/cstr.h>
#include <stc/csmap.h>

using_csset_str();

int main ()
{
    csset_str first = csset_str_init(); // empty
    c_init (csset_str, second, {"red", "green", "blue"});
    c_init (csset_str, third, {"orange", "pink", "yellow"});

    csset_str fourth = csset_str_init();
    csset_str_emplace(&fourth, "potatoes");
    csset_str_emplace(&fourth, "milk");
    csset_str_emplace(&fourth, "flour");

    csset_str fifth = csset_str_clone(second);
    c_foreach (i, csset_str, third)
        csset_str_emplace(&fifth, i.ref->str);
    c_foreach (i, csset_str, fourth)
        csset_str_emplace(&fifth, i.ref->str);

    c_del(csset_str, &first, &second, &third, &fourth);

    printf("fifth contains:\n\n");
    c_foreach (i, csset_str, fifth) 
        printf("%s\n", i.ref->str);

    csset_str_del(&fifth);
    return 0;
}
```
Output:
```
fifth contains:

red
green
flour
orange
blue
pink
yellow
milk
potatoes
```