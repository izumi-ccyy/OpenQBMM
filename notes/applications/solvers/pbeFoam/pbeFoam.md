# pbeFoam

1. [pbeFoam](#pbefoam)
   1. [createSingleCellMesh.H](#createsinglecellmeshh)
   2. [createFields.H](#createfieldsh)
   3. [readControls.H](#readcontrolsh)
   4. [output.H](#outputh)
   5. [pbeFoam.C](#pbefoamc)

## createSingleCellMesh.H

```cpp
Info<< "Constructing single cell mesh" << nl << endl;

Foam::simplifiedMeshes::hexCellFvMesh mesh(runTime);
```

Generate mesh with single cell.

## createFields.H

```cpp
if (mesh.nCells() != 1)
{
    FatalErrorIn(args.executable())
        << "Solver only applicable to single cell cases"
        << exit(FatalError);
}

Info<< nl << "Reading thermophysical properties\n" << endl;
```

Check for snigle cell mesh.

```cpp
autoPtr<psiThermo> pThermo
(
    psiThermo::New(mesh)
);
psiThermo& thermo = pThermo();
thermo.validate(args.executable(), "h", "e");
```

Define psiThermo.

```cpp
volScalarField rho
(
    IOobject
    (
        "rho",
        runTime.timeName(),
        mesh,
        IOobject::NO_READ,
        IOobject::AUTO_WRITE
    ),
    thermo.rho()
);

volScalarField& p = thermo.p();

volVectorField U
(
    IOobject
    (
        "U",
        runTime.timeName(),
        mesh,
        IOobject::NO_READ,
        IOobject::NO_WRITE
    ),
    mesh,
    dimensionedVector("zero", dimVelocity, vector::zero),
    p.boundaryField().types()
);

#include "createPhi.H"

Info << "Creating turbulence model.\n" << endl;
autoPtr<compressible::turbulenceModel> turbulence
(
    compressible::turbulenceModel::New
    (
        rho,
        U,
        phi,
        thermo
    )
);
```

Define $\rho$, $p$, $\mathbf{U}$, $\phi$ and compressible turbulence model.

```cpp
Info<< "Reading populationBalanceProperties\n" << endl;

IOdictionary populationBalanceProperties
(
    IOobject
    (
        "populationBalanceProperties",
        runTime.constant(),
        mesh,
        IOobject::MUST_READ_IF_MODIFIED,
        IOobject::NO_WRITE
    )
);

autoPtr<populationBalanceModel> populationBalance
(
    populationBalanceModel::New
    (
        "populationBalance",
        populationBalanceProperties,
        phi
    )
);
```

Read population balance properties and define population balance model. 

## readControls.H

```cpp
if (runTime.controlDict().lookupOrDefault("suppressSolverInfo", false))
{
    lduMatrix::debug = 0;
}
```

Read `suppressSolverInfo` in `controlDict`.

## output.H

```cpp
runTime.write();

volScalarField moment0
(
    mesh.lookupObject<volScalarField>("moment.0.populationBalance")
);

Info<< "moment.0 = " << moment0[0] << endl;
```

Output `moment.0`.

## pbeFoam.C

```cpp
int main(int argc, char *argv[])
{
    argList::noParallel();

    #define CREATE_MESH createSingleCellMesh.H
    #define NO_CONTROL
    #include "postProcess.H"

    #include "setRootCase.H"
    #include "createTime.H"
    #include "createSingleCellMesh.H"
    #include "createFields.H"

    turbulence->validate();

    // * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * //

    Info<< "\nStarting time loop\n" << endl;

    while (runTime.run())
    {
        #include "readControls.H"

        runTime++;
        Info<< "Time = " << runTime.timeName() << nl << endl;

        turbulence->validate();
        populationBalance->solve();

        #include "output.H"

        Info<< "ExecutionTime = " << runTime.elapsedCpuTime() << " s"
            << "  ClockTime = " << runTime.elapsedClockTime() << " s"
            << nl << endl;
    }

    Info << "Number of steps = " << runTime.timeIndex() << endl;
    Info << "End" << nl << endl;

    return 0;
}
```

Solve population balance equation in a single cell.