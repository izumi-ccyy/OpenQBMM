# PDF Transport Model

1. [PDF Transport Model](#pdf-transport-model)
   1. [PDFTransportModel](#pdftransportmodel)
      1. [Description](#description)
      2. [PDFTransportModel.H](#pdftransportmodelh)
      3. [PDFTransportModel.C](#pdftransportmodelc)
   2. [univariatePDFTransportModel](#univariatepdftransportmodel)
      1. [Description](#description-1)
      2. [univariatePDFTransportModel.H](#univariatepdftransportmodelh)
      3. [univariatePDFTransportModelI.H](#univariatepdftransportmodelih)
      4. [univariatePDFTransportModel.C](#univariatepdftransportmodelc)
   3. [velocityPDFTransportModel](#velocitypdftransportmodel)
      1. [Description](#description-2)
      2. [velocityPDFTransportModel.H](#velocitypdftransportmodelh)
      3. [velocityPDFTransportModel.C](#velocitypdftransportmodelc)

## PDFTransportModel

### Description

Abstract class for the run-time selection of the PDF transport model.

![inherit graph of PDF transport model](./fig/class_foam_1_1_p_d_f_transport_model__inherit__graph.png)

### PDFTransportModel.H

```cpp
    // Private data

        //- Name of the PDFTransportModel
        const word name_;


protected:

    // Protected data

        //- Constant reference to the mesh
        const fvMesh& mesh_;
```

Declare private and protected data.

```cpp
    // Member Functions

        //- Solve PDF transport equation
        virtual void solve() = 0;
```

Declare public member functionis.

### PDFTransportModel.C

```cpp
Foam::PDFTransportModel::PDFTransportModel
(
    const word& name,
    const dictionary& dict,
    const fvMesh& mesh
)
:
    name_(name),
    mesh_(mesh)
{}

Foam::PDFTransportModel::~PDFTransportModel()
{}
```

Define constructor and destructor.

## univariatePDFTransportModel

### Description

Solve a univariate PDF transport equation with the extended quadrature method of moments.

### univariatePDFTransportModel.H

```cpp
    // Protected data

        //- Univariate quadrature approximation used to solve PDF transport
        scalarQuadratureApproximation quadrature_;

        //- Moment advection
        autoPtr<univariateMomentAdvection> momentAdvection_;
```

Declare protected data: `quadrature_` and `momentAdvection_`.

```cpp
    // Protected member functions


        //- Calculate implicit source terms
        virtual tmp<fvScalarMatrix> implicitMomentSource
        (
            const volScalarMoment& moment
        ) = 0;

        //- Calculate explicit source terms with realizable ODE solver
        virtual void explicitMomentSource() = 0;

        //- Are moments updated due to sources
        virtual bool solveMomentSources() const = 0;

        //- Is the realizable ode solver used to solve sources
        virtual bool solveMomentOde() const = 0;
```

Declare protected member functions as pure virtual functions

```cpp
    // Member Functions

        //- Access

            //- Returns a const reference to the quadrature approximation
            inline const scalarQuadratureApproximation& quadrature() const;

            //- Return the maximum Courant number ensuring moment realizability
            inline scalar realizableCo() const;

        //-Edit

            //- Solve univariate PDF transport equation
            virtual void solve();
```

Declare public member functions.

```cpp
#include "univariatePDFTransportModelI.H"
```

Include `univariatePDFTransportModelI.H`.

### univariatePDFTransportModelI.H

```cpp
const Foam::scalarQuadratureApproximation&
Foam::PDFTransportModels::univariatePDFTransportModel::quadrature() const
{
    return quadrature_;
}

Foam::scalar
Foam::PDFTransportModels::univariatePDFTransportModel::realizableCo() const
{
    return momentAdvection_().realizableCo();
}
```

Define member function `inline const scalarQuadratureApproximation& quadrature() const;` and `inline scalar realizableCo() const;`.

### univariatePDFTransportModel.C

```cpp
Foam::PDFTransportModels::univariatePDFTransportModel
::~univariatePDFTransportModel()
{}

// * * * * * * * * * * * * * * Member Functions  * * * * * * * * * * * * * * //

void Foam::PDFTransportModels::univariatePDFTransportModel::solve()
{
    momentAdvection_().update();

    // List of moment transport equations
    PtrList<fvScalarMatrix> momentEqns(quadrature_.nMoments());

    // Solve moment transport equations
    forAll(quadrature_.moments(), momenti)
    {
        volScalarMoment& m = quadrature_.moments()[momenti];

        momentEqns.set
        (
            momenti,
            new fvScalarMatrix
            (
                fvm::ddt(m)
              + momentAdvection_().divMoments()[momenti]
              ==
                implicitMomentSource(m)
            )
        );
    }
    // relax and solve moment equations
    forAll (momentEqns, mEqni)
    {
        momentEqns[mEqni].relax();
        momentEqns[mEqni].solve();
    }
    // update quadratures
    quadrature_.updateQuadrature();

    if (solveMomentSources())
    {
        this->explicitMomentSource();
    }
}
```

## velocityPDFTransportModel

### Description

### velocityPDFTransportModel.H

### velocityPDFTransportModel.C