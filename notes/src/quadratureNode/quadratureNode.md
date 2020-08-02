# Quadrature nodes

1. [Quadrature nodes](#quadrature-nodes)
   1. [quadratureNode](#quadraturenode)
      1. [quadratureNode.H](#quadraturenodeh)
         1. [Description](#description)
         2. [Include](#include)
         3. [Template](#template)
         4. [Protected](#protected)
            1. [Typedefs](#typedefs)
            2. [Protected data](#protected-data)
         5. [Public](#public)
            1. [Constructors](#constructors)
            2. [Clone](#clone)
            3. [iNew](#inew)
            4. [Destructor](#destructor)
            5. [Member functions](#member-functions)
      2. [quadratureNodeI.H](#quadraturenodeih)
      3. [quadratureNodes.H](#quadraturenodesh)
      4. [quadratureNode.C](#quadraturenodec)
   2. [velocityQuadratureNode](#velocityquadraturenode)

## quadratureNode

### quadratureNode.H

#### Description

Extended version of the quadrature node, which stores the list of secondary weights and abscissae associated to the primary weight and abscissa, and the sigma parameter.

![inherit graph of quadrature node](./fig/class_foam_1_1quadrature_node__inherit__graph.png)

#### Include

```cpp
#include "fvMesh.H"
#include "PtrList.H"
#include "dictionary.H"
#include "dictionaryEntry.H"
#include "mappedList.H"
```

#### Template

```cpp
template<class scalarType, class vectorType>
class quadratureNode
```

#### Protected

##### Typedefs

```cpp
//- Typedefs

typedef scalarType weightType;
typedef PtrList<PtrList<weightType>> secondaryWeightsType;

typedef scalarType abscissaType;
typedef PtrList<abscissaType> abscissaeType;
typedef PtrList<abscissaeType> secondaryAbscissaeType;

typedef vectorType velocityAbscissaeType;

typedef scalarType sigmaType;
typedef PtrList<sigmaType> sigmasType;
```

##### Protected data

```cpp
// Protected data

    //- Name of the quadrature node
    word name_;

    //- Primary weight of the node
    weightType weight_;

    //- Primary abscissae of the node
    abscissaeType abscissae_;

    //- Indicies of abscissa components
    labelList scalarIndexes_;

    //- Indicies of velocity components
    labelList velocityIndexes_;

    //- Index of size component
    label sizeIndex_;
    // determine internal coordinate
    //- Is the size length based
    bool lengthBased_;

    //- Is the size mass based
    bool massBased_;

    //- Pointer to density field
    const volScalarField* rhoPtr_;

    //- Are weights in volume fraction
    bool useVolumeFraction_;

    //- List of secondary weights of the node
    secondaryWeightsType secondaryWeights_;

    //- List of secondary abscissae of the node
    secondaryAbscissaeType secondaryAbscissae_;

    //- Parameter of the kernel density function used in EQMOM
    sigmasType sigmas_;

    //- Number of secondary nodes
    label nSecondaryNodes_;

    //- Boolean variable to identify extended nodes
    bool extended_;
```

Declare 15 variables.

1. `name_`: Name of the quadrature node;
2. `weight_`: Primary weight of the node;
3. `abscissae_`: Primary abscissae of the node, a PtrList;
4. `scalarIndexes_`: Indicies of abscissa components, a labelList;
5. `velocityIndexes_`: Indicies of velocity components, a label list;
6. `sizeIndex_`: Index of size component;
7. `lengthBased_`: Is the size length based;
8. `massBased_`: Is the size mass based;
9. `rhoPtr_`: Pointer to density field;
10. `useVolumeFraction_`: Are weights in volume fraction;
11. `secondaryWeights_`: List of secondary weights of the node, a PtrList of PtrList;
12. `secondaryAbscissae_`: List of secondary abscissae of the node, a PtrList of PtrList;
13. `sigmas_`: Parameter of the kernel density function used in EQMOM, a PtrList;
14. `nSecondaryNodes_`: Number of secondary nodes;
15. `extended_`: Boolean variable to identify extended nodes.



#### Public

##### Constructors

```cpp
// Constructors

//- Construct from name, mesh and dimensions
quadratureNode
(
    const word& name,
    const word& distributionName,
    const fvMesh& mesh,
    const dimensionSet& weightDimensions,
    const PtrList<dimensionSet>& abscissaeDimensions,
    const wordList& boundaryTypes,
    const bool extended = false,
    const label nSecondaryNodes = 0
);

//- Construct from name, number of secondary nodes, mesh and dimensions
quadratureNode
(
    const word& name,
    const word& distributionName,
    const fvMesh& mesh,
    const dimensionSet& weightDimensions,
    const PtrList<dimensionSet>& abscissaeDimensions,
    const bool extended = false,
    const label nSecondaryNodes = 0
);
```

Declare two constructors.

##### Clone

```cpp
//- Return clone
        autoPtr<quadratureNode<scalarType, vectorType>> clone() const;
```

Declare a `clone` function.

##### iNew

```cpp
//- Return a pointer to a new quadrature node created on freestore
//  from Istream
class iNew
{
    const word distributionName_;
    const fvMesh& mesh_;
    const dimensionSet& weightDimensions_;
    const PtrList<dimensionSet>& abscissaeDimensions_;
    const wordList& boundaryTypes_;
    const bool extended_;
    const label nSecondaryNodes_;

public:
    // constructor for iNew
    iNew
    (
        const word& distributionName,
        const fvMesh& mesh,
        const dimensionSet& weightDimensions,
        const PtrList<dimensionSet>& abscissaeDimensions,
        const wordList& boundaryTypes,
        const bool extended,
        const label nSecondaryNodes
    )
    :
        distributionName_(distributionName),
        mesh_(mesh),
        weightDimensions_(weightDimensions),
        abscissaeDimensions_(abscissaeDimensions),
        boundaryTypes_(boundaryTypes),
        extended_(extended),
        nSecondaryNodes_(nSecondaryNodes)
    {}
    // return a pointer to a new quadratureNode from an input
    autoPtr<quadratureNode<scalarType, vectorType>>
    operator()
    (
        Istream& is
    ) const
    {
        labelList ent(is);
        
        return autoPtr<quadratureNode<scalarType, vectorType>>
        (
            new quadratureNode<scalarType, vectorType>
            (
                "node" + mappedList<scalar>::listToWord(ent),
                distributionName_,
                mesh_,
                weightDimensions_,
                abscissaeDimensions_,
                boundaryTypes_,
                extended_,
                nSecondaryNodes_
            )
        );
    }
};
```

Declare and define a new class `iNew` to return a pointer to a new quadratureNode from an input.

##### Destructor

```cpp
//- Destructor
virtual ~quadratureNode();
```

Declare a destructor.

##### Member functions

Declare 24 member functions.




### quadratureNodeI.H

### quadratureNodes.H

### quadratureNode.C

## velocityQuadratureNode