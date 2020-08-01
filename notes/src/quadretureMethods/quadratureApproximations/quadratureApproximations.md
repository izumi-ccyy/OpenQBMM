# Quadrature Approximations

1. [Quadrature Approximations](#quadrature-approximations)
   1. [quadratureApproximation](#quadratureapproximation)
      1. [Description](#description)
      2. [quadratureApproximation.H](#quadratureapproximationh)
         1. [Include](#include)
         2. [Protected data](#protected-data)
         3. [Public data and member functions](#public-data-and-member-functions)
      3. [quadratureApproximationI.H](#quadratureapproximationih)
      4. [quadratureApproximations.H](#quadratureapproximationsh)
      5. [quadratureApproximation.C](#quadratureapproximationc)
         1. [Constructors](#constructors)
            1. [Constructor 1](#constructor-1)
            2. [Constructor 2](#constructor-2)
         2. [Destructor](#destructor)
         3. [Member functions](#member-functions)

## quadratureApproximation

### Description

Provides storage for the moments and the corresponding quadrature approximation of a distribution function. Methods to update the moments and the quadrature approximation are provided. **This is defined as class template so that it can be used  for both scalar and velocity moments.**

![inherit graph of quadrature approximation](./fig/class_foam_1_1quadrature_approximation__inherit__graph.png)

### quadratureApproximation.H

#### Include

```cpp
#include "fvCFD.H"
#include "IOdictionary.H"
#include "mappedPtrList.H"
#include "fvMesh.H"
#include "volFields.H"
#include "surfaceFields.H"
#include "fieldMomentInversion.H"
#include "volVelocityNode.H"
#include "surfaceVelocityNode.H"
```

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

##### Constructor 1

```cpp
template<class momentType, class nodeType>
Foam::quadratureApproximation<momentType, nodeType>::
quadratureApproximation
(
    const word& name,
    const fvMesh& mesh,
    const word& support
)
:
    // define IO object of dictionary
    IOdictionary
    (
        IOobject
        (
            IOobject::groupName("quadratureProperties", name),
            mesh.time().constant(),
            mesh,
            IOobject::MUST_READ,
            IOobject::NO_WRITE
        )
    ),
    // initialize name_, mesh_, dict_ with argument and current object
    name_(name),
    mesh_(mesh),
    dict_(*this),
    // initialize momentOrders_ by the entry of moments in the dict_
    momentOrders_
    (
        const_cast
        <
            const quadratureApproximation<momentType, nodeType>&
        >(*this).lookup("moments")
    ),
    // initialize momentOrders_ by the entry of nodes in the dict_
    nodeIndexes_
    (
        const_cast
        <
            const quadratureApproximation<momentType, nodeType>&
        >(*this).lookup("nodes")
    ),
    // initialize nNodes_, nodes_, moments_, nDimensions_, nMoments_ with momentOrders_ etc.
    nNodes_(momentOrders_[0].size(), 1),
    nodes_(),
    moments_(name_, *this, mesh_, nodes_, support),
    nDimensions_(moments_[0].cmptOrders().size()),
    nMoments_(moments_.size()),
    // initialize nSecondaryNodes_ with dict_ or default value
    nSecondaryNodes_
    (
        lookupOrDefault<label>("nSecondaryNodes", nMoments_ + 1)
    ),
    // initialize support with argument
    support_(support),
    // initialize momentFieldInverter_
    momentFieldInverter_()
// function body
{
    // obtain number of nodes nNodes_
    forAll(nodeIndexes_, nodei)
    {
        forAll(nNodes_, dimi)
        {
            nNodes_[dimi] = max(nNodes_[dimi], nodeIndexes_[nodei][dimi] + 1);
        }
    }
    // define and initialize abscissaeDimensions
    PtrList<dimensionSet> abscissaeDimensions(momentOrders_[0].size());
    // define zeroOrder and velocityIndexes
    labelList zeroOrder(momentOrders_[0].size(), 0);
    labelList velocityIndexes;
    // for every abscissaeDimension
    forAll(abscissaeDimensions, dimi)
    {
        // define and initialize firstOrder with zeroOrder
        labelList firstOrder(zeroOrder);
        // firstOrder[dimi] = 1
        firstOrder[dimi] = 1;
        // obtain the dimension of moments and store it in dimi
        abscissaeDimensions.set
        (
            dimi,
            new dimensionSet
            (
                moments_(firstOrder).dimensions()/moments_(0).dimensions()
            )
        );
        // determine if the dimi is dimension of velocity
        if (abscissaeDimensions[dimi] == dimVelocity)
        {
            // if so, append dimi to velocityIndexes
            velocityIndexes.append(dimi);
        }
    }
    // if velocityIndexes is empty
    if (velocityIndexes.size() == 0)
    {
        // append -1 to it
        velocityIndexes.append(-1);
    }
    // allocate space for momentFieldInverter_
    momentFieldInverter_ =
        fieldMomentInversion::New
        (
            (*this),
            mesh_,
            momentOrders_,
            nodeIndexes_,
            velocityIndexes,
            nSecondaryNodes_
        );

    // Allocating nodes
    nodes_ = autoPtr<mappedPtrList<nodeType>>
    (
        new mappedPtrList<nodeType>
        (
            lookup("nodes"),
            typename nodeType::iNew
            (
                name_,
                mesh_,
                moments_[0].dimensions(),
                abscissaeDimensions,
                moments_[0].boundaryField().types(),
                momentFieldInverter_().extended(),
                nSecondaryNodes_
            )
        )
    );
    // set node map
    nodes_().setMap(mappedPtrList<scalar>(nodes_().size(), nodeIndexes_).map());
    // update quadrature
    updateQuadrature();
}
```

##### Constructor 2

```cpp
// the parameters are different, so the initialization method is different
template<class momentType, class nodeType>
Foam::quadratureApproximation<momentType, nodeType>::
quadratureApproximation
(
    // different parameters
    const word& dictName,
    const word& name,
    const momentFieldSetType& mFieldSet,
    bool calcQuadratureOnCreation
)
:   // define IO object of dictionary with mFieldSet
    IOdictionary
    (
        IOobject
        (
            dictName,
            mFieldSet[0].mesh().time().constant(),
            mFieldSet[0].mesh(),
            IOobject::MUST_READ,
            IOobject::NO_WRITE,
            false
        )
    ),
    // mFieldSet is used to initialize variables
    name_(name),
    mesh_(mFieldSet[0].mesh()),
    dict_(*this),
    momentOrders_
    (
        const_cast
        <
            const quadratureApproximation<momentType, nodeType>&
        >(*this).lookup("moments")
    ),
    nodeIndexes_
    (
        const_cast
        <
            const quadratureApproximation<momentType, nodeType>&
        >(*this).lookup("nodes")
    ),
    nNodes_(momentOrders_[0].size(), 1),
    nodes_(),
    // another method to construct moments
    moments_
    (
        name_,
        mFieldSet.size(),
        nodes_,
        mFieldSet.nDimensions(),
        mFieldSet.map(),
        mFieldSet.support()
    ),
    nDimensions_(mFieldSet.nDimensions()),
    nMoments_(mFieldSet.size()),
    nSecondaryNodes_
    (
        lookupOrDefault<label>("nSecondaryNodes", nMoments_ + 1)
    ),
    support_(mFieldSet.support()),
    momentFieldInverter_()
// function body 
{
    // same method to obtain nNodes_
    forAll(nodeIndexes_, nodei)
    {
        forAll(nNodes_, dimi)
        {
            nNodes_[dimi] = max(nNodes_[dimi], nodeIndexes_[nodei][dimi] + 1);
        }
    }
    // for every moments
    forAll(moments_, mi)
    {
        // set moments_
        moments_.set
        (
            mi,
            new momentType
            (
                name_ + Foam::name(mi),
                mFieldSet[mi].cmptOrders(),
                nodes_,
                mFieldSet[mi]
            )
        );
    }
    // same method to declare abscissaeDimensions etc.
    PtrList<dimensionSet> abscissaeDimensions(momentOrders_[0].size());
    labelList zeroOrder(momentOrders_[0].size(), 0);
    labelList velocityIndexes;
    // same
    forAll(abscissaeDimensions, dimi)
    {
        labelList firstOrder(zeroOrder);
        firstOrder[dimi] = 1;

        abscissaeDimensions.set
        (
            dimi,
            new dimensionSet
            (
                moments_(firstOrder).dimensions()/moments_(0).dimensions()
            )
        );
        
        if (abscissaeDimensions[dimi] == dimVelocity)
        {
            velocityIndexes.append(dimi);
        }
    }
    // same
    momentFieldInverter_ =
        fieldMomentInversion::New
        (
            (*this),
            mesh_,
            momentOrders_,
            nodeIndexes_,
            velocityIndexes,
            nSecondaryNodes_
        );
    // if there is secondary nodes, extended quadrature should be adopted
    if (nSecondaryNodes_ != 0 && !momentFieldInverter_().extended())
    {
        WarningInFunction
            << "The number of secondary nodes in the quadrature" << nl
            << "    approximation is not zero, but the selected" << nl
            << "    inversion algorithm is not of extended type." << nl
            << "    Proceeding with nSecondaryNodes = 0." << nl
            << "    No extended quadrature will be computed." << nl;
    }
    // same
    // Allocating nodes
    nodes_ = autoPtr<mappedPtrList<nodeType>>
    (
        new mappedPtrList<nodeType>
        (
            lookup("nodes"),
            typename nodeType::iNew
            (
                name_,
                mesh_,
                moments_[0].dimensions(),
                abscissaeDimensions,
                moments_[0].boundaryField().types(),
                momentFieldInverter_().extended(),
                nSecondaryNodes_
            )
        )
    );
    // same
    nodes_().setMap(mappedPtrList<scalar>(nodes_().size(), nodeIndexes_).map());
    // determine whether to update
    if (calcQuadratureOnCreation)
    {
        updateQuadrature();
    }
}
```

The difference between constructor 1 and constructor 2 is that they have different parameters which makes the initialization a little different. Additionally, the method of construction of moments is different in constructor 1 and constructor 2.

#### Destructor

```cpp
template<class momentType, class nodeType>
Foam::quadratureApproximation<momentType, nodeType>
::~quadratureApproximation()
{}
```

Define destructor.

#### Member functions

```cpp
template<class momentType, class nodeType>
void Foam::quadratureApproximation<momentType, nodeType>
::updateQuadrature()
{
    momentFieldInverter_().invert(moments_, nodes_());
    updateMoments();
}
```

Update quadrature by update moments.

```cpp
template<class momentType, class nodeType>
void Foam::quadratureApproximation<momentType, nodeType>
::updateBoundaryQuadrature()
{
    momentFieldInverter_().invertBoundaryMoments(moments_, nodes_());
    moments_.updateBoundaries();
}
```

Update quadrature at boundaries.

```cpp
template<class momentType, class nodeType>
void Foam::quadratureApproximation<momentType, nodeType>
::updateMoments()
{
    moments_.update();
}
```

Update Moments.

```cpp
template<class momentType, class nodeType>
void Foam::quadratureApproximation<momentType, nodeType>
::updateLocalMoments(label celli)
{
    moments_.updateLocalMoments(celli);
}
```

Update moment at cell $i$.

```cpp
template<class momentType, class nodeType>
bool Foam::quadratureApproximation<momentType, nodeType>
::updateLocalQuadrature(label celli, bool fatalErrorOnFailedRealizabilityTest)
{
    bool realizable = momentFieldInverter_().invertLocalMoments
    (
        moments_, nodes_(), celli, false
    );

    if (!realizable && fatalErrorOnFailedRealizabilityTest)
    {
        return realizable;
    }

    moments_.updateLocalMoments(celli);

    return realizable;
}
```

Recalculate quadrature nodes from the moments in the specified cell.

**All these above member functions contain operations of moments, So what is the relation between quadrature and moment?**