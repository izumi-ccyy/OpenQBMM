# mappedList

1. [mappedList](#mappedlist)
   1. [mappedList.H](#mappedlisth)
      1. [Description](#description)
      2. [Include](#include)
   2. [mappedListI.H](#mappedlistih)
   3. [mappedLists.H](#mappedlistsh)
   4. [mappedList.C](#mappedlistc)

## mappedList.H

### Description

Stores a list, with a `Map<label>` that takes a label key and returns the index into the list.

    Example

        Lookup  |  Label Key | Index
         0 0 0         0         0
         1 0 0       100         1
         0 1 0        10         2
         0 0 1         1         3
         2 0 0       200         4
         2 1 0       210         5

The `operator ()` is overloaded in order to hide the above mapping.

![inherit graph of mapped list](./fig/class_foam_1_1mapped_list__inherit__graph.png)

### Include

```cpp
#include "scalar.H"
#include "vector.H"
#include "labelList.H"
#include "Map.H"
```



## mappedListI.H

## mappedLists.H

## mappedList.C