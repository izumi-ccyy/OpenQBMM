# Quadrature Approximations

1. [Quadrature Approximations](#quadrature-approximations)
   1. [quadratureApproximation](#quadratureapproximation)
      1. [Description](#description)
      2. [quadratureApproximation.H](#quadratureapproximationh)
         1. [Protected data](#protected-data)
         2. [Public data and member functions](#public-data-and-member-functions)
      3. [quadratureApproximationI.H](#quadratureapproximationih)
      4. [quadratureApproximations.H](#quadratureapproximationsh)
      5. [quadratureApproximation.C](#quadratureapproximationc)
         1. [Constructors](#constructors)
         2. [Destructor](#destructor)
         3. [Member functions](#member-functions)

## quadratureApproximation

### Description

Provides storage for the moments and the corresponding quadrature approximation of a distribution function. Methods to update the moments and the quadrature approximation are provided. **This is defined as class template so that it can be used  for both scalar and velocity moments.**

![inherit graph of quadrature approximation](./fig/class_foam_1_1quadrature_approximation__inherit__graph.png)

### quadratureApproximation.H

#### Protected data

```cpp
protected:
    // Protected data

        typedef momentFieldSet<momentType, nodeType> momentFieldSetType;

        //- Name of the quadrature approximation
        const word name_;

        //- Reference to the mesh
        const fvMesh& mesh_;

        const dictionary& dict_;

        //- Moment orders used to map moments
        const labelListList momentOrders_;

        //- Node indexes used to map nodes
        const labelListList nodeIndexes_;

        //- Number of nodes in each direction
        labelList nNodes_;

        //- AutoPtr to the mappedPtrList of quadrature nodes
        autoPtr<mappedPtrList<nodeType>> nodes_;

        //- List of moments
        momentFieldSetType moments_;

        //- Dimensionality of the distribution function
        const label nDimensions_;

        //- Number of moments
        const label nMoments_;

        //- Number of secondary nodes
        const label nSecondaryNodes_;

        //- Support of the distribution function
        word support_;

        //- Extended moment inversion method
        autoPtr<fieldMomentInversion> momentFieldInverter_;
```

Declare protected data: 

* `momentFieldsSet`: a moment type containing moment type and node type;
* `name_`: name of the quadrature approximation;
* `mesh_`: mesh;
* `dict_`: dictionary;
* `momentOrders`: a list containing moment orders;
* `nodeIndes_`: a list containing node indexes
* `nNodes_`: a number representing number of nodes in each directions;
* `nodes_`: an AutoPtr for nodes;
* `moments_`: a List of moments;
* `nDimensions_`: Dimensionality of the distribution function;
* `nMoments_`: number of moments;
* `nSecondaryNodes_`: number of secondary nodes;
* `suport_`: support of the distribution function;
* `momentFieldInverter_`: an AutoPtr for extended moment inversion method.

#### Public data and member functions

```cpp
public:

    //- Default name of the phase properties dictionary
    static const word propertiesName;
```

Declare name of the phase properties dictionary.

```cpp
    // Constructors

        //- Construct from mesh
        quadratureApproximation
        (
            const word& name,
            const fvMesh& mesh,
            const word& support
        );

        //- Construct from dictionary name, name, momentFieldSet
        //  Moment fields in the momentFieldSet passed as reference are
        //  used to initialise the local copy of moments. Inversion on
        //  creation is optional.
        quadratureApproximation
        (
            const word& dictName,
            const word& name,
            const momentFieldSetType& mFieldSet,
            bool calcQuadratureOnCreation = false
        );

    //- Destructor
    virtual ~quadratureApproximation();
```

Declare constructors and destructors. **It is worthy to note that there are two constructors. One of them is constructed from name, mesh and support. The other is constructed from one more vaiable calcQuadratureOnCreation.**

```cpp
    // Member Functions

        // Access

            //- Return name
            inline const word name() const;

            //- Return the nmber of nodes in each direction
            const labelList& nNodes() const;

            //- Extended moment inversion method
            inline fieldMomentInversion& momentFieldInverter();

            //- Const access to the nodes
            inline const mappedPtrList<nodeType>& nodes() const;

            //- Non-Const access to the nodes
            inline mappedPtrList<nodeType>& nodes();

            //- Const access to the moments
            inline const momentFieldSetType& moments() const;

            //- Non-const access to the moments
            inline momentFieldSetType& moments();

            //- Return the number of dimensions in the distribution
            inline label nDimensions() const;

            //- Return the number of moments
            inline label nMoments() const;

            //- Return moment orders
            inline const labelListList& momentOrders() const;

            //- return node indexes
            inline const labelListList& nodeIndexes() const;
```

Declare public member functions returning the protected data:

* `name()`: return `name_`;
* `nNodes`: return `nNodes_`;
* `momentFieldInverter()`: Extended moment inversion method;
* `nodes() const`: Const access to the `nodes_`;
* `nodes()`: Non-const access to the `nodes_`;
* `moments() const`: Const access to the `moments_`;
* `moments()`: Non-const access to the `moments_`;
* `nDimensions()`: return `nDimensions_`;
* `nMoments()`: return `nMoments_`;
* `momentOrders()`: return `momentOrders_`;
* `nodeIndexes()`: return `nodeIndex_`

```cpp

        // Edit

            //- Recalculate the quadrature nodes from the moments
            void updateQuadrature();

            //- Recalculate the quadrature nodes from the moments
            //  on boundaries
            void updateBoundaryQuadrature();
            //- Recalculate the moments from the quadrature nodes
            void updateMoments();

            //- Recalculate moments in the specified cell
            void updateLocalMoments(label celli);

            //- Recalculate the quadrature nodes from the moments in the
            //  specified cell
            bool updateLocalQuadrature
            (
                label celli,
                bool fatalErrorOnFailedRealizabilityTest = true
            );
```

Declare public member function about updating the class.

* `updateQuadrature()`: Recalculate the quadrature nodes from the moments;
* `updateBoundaryQuadrature()`: Recalculate the quadrature nodes from the moments on boundaries;
* `updateMoments()`: Recalculate the moments from the quadrature nodes;
* `updateLocalMoments(label celli)`: Recalculate moments in the specified cell;
* `bool updateLocalQuadrature(label, bool)`: Recalculate the quadrature nodes from the moments in the specified cell.

```cpp
#ifdef NoRepository
#   include "quadratureApproximationI.H"
#   include "quadratureApproximation.C"
#endif
```

Include `quadratureApproximationI.H` and `quadratureApproximation.C`

### quadratureApproximationI.H

```cpp
template <class momentType, class nodeType>
const word
Foam::quadratureApproximation<momentType, nodeType>::name() const
{
    return name_;
}

template <class momentType, class nodeType>
const labelList&
Foam::quadratureApproximation<momentType, nodeType>::nNodes() const
{
    return nNodes_;
}

template <class momentType, class nodeType>
Foam::fieldMomentInversion&
Foam::quadratureApproximation<momentType, nodeType>
::momentFieldInverter()
{
    return momentFieldInverter_();
}

template <class momentType, class nodeType>
const Foam::mappedPtrList<nodeType>&
Foam::quadratureApproximation<momentType, nodeType>::nodes() const
{
    return nodes_();
}

template <class momentType, class nodeType>
Foam::mappedPtrList<nodeType>&
Foam::quadratureApproximation<momentType, nodeType>::nodes()
{
    return nodes_();
}

template <class momentType, class nodeType>
const momentFieldSet<momentType, nodeType>&
Foam::quadratureApproximation<momentType, nodeType>::moments() const
{
    return moments_;
}

template <class momentType, class nodeType>
momentFieldSet<momentType, nodeType>&
Foam::quadratureApproximation<momentType, nodeType>::moments()
{
    return moments_;
}

template <class momentType, class nodeType>
Foam::label
Foam::quadratureApproximation<momentType, nodeType>::nDimensions() const
{
    return nDimensions_;
}

template <class momentType, class nodeType>
Foam::label
Foam::quadratureApproximation<momentType, nodeType>::nMoments() const
{
    return nMoments_;
}

template <class momentType, class nodeType>
const Foam::labelListList&
Foam::quadratureApproximation<momentType, nodeType>::momentOrders() const
{
    return momentOrders_;
}

template <class momentType, class nodeType>
const Foam::labelListList&
Foam::quadratureApproximation<momentType, nodeType>::nodeIndexes() const
{
    return nodeIndexes_;
}
```

Definitions of public member functions that returning the protected variables. **It is worthy to note that these functions are defined as template.**

### quadratureApproximations.H

```cpp
typedef quadratureApproximation<volScalarMoment, volScalarNode>
    scalarQuadratureApproximation;

typedef quadratureApproximation<volVelocityMoment, volVelocityNode>
    velocityQuadratureApproximation;
```

Define a shorter name for this class for both scalar moment and velocity moment.

### quadratureApproximation.C

```cpp
template<class momentType, class nodeType>
const Foam::word Foam::quadratureApproximation<momentType, nodeType>::
propertiesName("quadratureProperties");
```

Initialize `propertiesName` as `quadratureProperties`.

#### Constructors

#### Destructor

#### Member functions