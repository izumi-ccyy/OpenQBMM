# eigenSolver

1. [eigenSolver](#eigensolver)
   1. [Theory](#theory)
   2. [eigenSolver.H](#eigensolverh)
   3. [eigenSolver.C](#eigensolverc)
      1. [Constructors](#constructors)

## Theory

## eigenSolver.H

```cpp
private:

    // Private data

        //- Real part of eigenvalues
        scalarDiagonalMatrix eigenvaluesRe_;

        //- Imaginary part of eigenvalues
        scalarDiagonalMatrix eigenvaluesIm_;

        //- Eigenvectors matrix
        scalarSquareMatrix eigenvectors_;

        //- Matrix with the same size of original matrix
        scalarSquareMatrix H_;

        //- Number of rows and columns in original matrix
        label n_;
```

Declare private data.

```cpp
    // Private member functions

        //- Checks matrix for symmetry
        bool isSymmetric(const scalarSquareMatrix& A);

        //- Householder transform of a symmetric matrix to tri-diagonal form
        void tridiagonaliseSymmMatrix();

        //- Symmetric tri-diagonal QL algorithm
        void symmTridiagonalQL();

        //- Reduce non-symmetric matrix to Hessemberg form
        void Hessenberg();

        //- Reduce matrix from Hessenberg to real Schur form
        void realSchur();
```

Declare private member functions.

```cpp
    //- Return real part of the eigenvalues
        const scalarDiagonalMatrix& eigenvaluesRe() const
        {
            return eigenvaluesRe_;
        }

        const scalarDiagonalMatrix& eigenvaluesIm() const
        {
            return eigenvaluesIm_;
        }

        //- Return eigenvectors
        const scalarSquareMatrix& eigenvectors() const
        {
            return eigenvectors_;
        }
```

Declare public member functions.

## eigenSolver.C

### Constructors

```cpp
Foam::eigenSolver::eigenSolver
(
    const scalarSquareMatrix& A
)
:
    eigenvaluesRe_(A.n()),
    eigenvaluesIm_(A.n()),
    eigenvectors_(A.n(), A.n()),
    H_(),
    n_(A.n())
{
    if (isSymmetric(A))
    {
        eigenvectors_ = A;
        tridiagonaliseSymmMatrix();
        symmTridiagonalQL();
    }
    else
    {
        H_ = A;
        Hessenberg();
        realSchur();
    }
}
```

```cpp
bool Foam::eigenSolver::isSymmetric(const scalarSquareMatrix& A)
{
    bool symm = true;

    for (label j = 0; (j < n_) && symm; j++)
    {
        for (label i = 0; (i < n_) && symm; i++)
        {
            symm = (A[i][j] == A[j][i]);
        }
    }

    return symm;
}
```

Determine whether A is symmetric.