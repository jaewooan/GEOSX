message( "Entering /src/coreComponents/linearAlgebra/CMakeLists.txt")

#
# Specify all headers
#
set( linearAlgebra_headers
     common.hpp
     interfaces/BlasLapackFunctions.h
     interfaces/BlasLapackLA.hpp
     interfaces/InterfaceTypes.hpp
     interfaces/LinearOperator.hpp
     interfaces/MatrixBase.hpp
     interfaces/VectorBase.hpp
     solvers/BiCGSTABsolver.hpp
     solvers/BlockPreconditioner.hpp
     solvers/CGsolver.hpp
     solvers/GMRESsolver.hpp
     solvers/KrylovSolver.hpp
     solvers/KrylovUtils.hpp
     solvers/PreconditionerBase.hpp
     solvers/PreconditionerIdentity.hpp
     solvers/SeparateComponentPreconditioner.hpp
     utilities/BlockOperatorView.hpp
     utilities/BlockOperatorWrapper.hpp
     utilities/BlockOperator.hpp
     utilities/BlockVectorView.hpp
     utilities/BlockVectorWrapper.hpp
     utilities/BlockVector.hpp
     utilities/LAIHelperFunctions.hpp
     utilities/LinearSolverParameters.hpp
     utilities/LinearSolverResult.hpp
     DofManager.hpp
     DofManagerHelpers.hpp )

#
# Specify all sources
#
set( linearAlgebra_sources
     interfaces/BlasLapackLA.cpp
     solvers/BiCGSTABsolver.cpp
     solvers/BlockPreconditioner.cpp
     solvers/CGsolver.cpp
     solvers/GMRESsolver.cpp
     solvers/KrylovSolver.cpp
     solvers/SeparateComponentPreconditioner.cpp
     utilities/LAIHelperFunctions.cpp
     DofManager.cpp )

if( BUILD_OBJ_LIBS )
  set( dependencyList common blas lapack raja managers)
else()
  set( dependencyList common blas lapack raja )
endif()


if( ENABLE_MPI )
    list( APPEND dependencyList mpi )
endif()

if( ENABLE_TRILINOS )

    list( APPEND linearAlgebra_headers
          interfaces/trilinos/EpetraMatrix.hpp
          interfaces/trilinos/EpetraVector.hpp
          interfaces/trilinos/EpetraUtils.hpp
          interfaces/trilinos/TrilinosPreconditioner.hpp
          interfaces/trilinos/TrilinosSolver.hpp
          interfaces/trilinos/TrilinosInterface.hpp )

    list( APPEND linearAlgebra_sources
          interfaces/trilinos/EpetraMatrix.cpp
          interfaces/trilinos/EpetraVector.cpp
          interfaces/trilinos/TrilinosPreconditioner.cpp
          interfaces/trilinos/TrilinosSolver.cpp
          interfaces/trilinos/TrilinosInterface.cpp )

    list( APPEND dependencyList trilinos )

endif()

if( ENABLE_HYPRE )

    list( APPEND linearAlgebra_headers
          interfaces/hypre/HypreMGRStrategies.hpp
          interfaces/hypre/HypreMatrix.hpp
          interfaces/hypre/HypreVector.hpp
          interfaces/hypre/HyprePreconditioner.hpp
          interfaces/hypre/HypreSolver.hpp
          interfaces/hypre/HypreInterface.hpp
          interfaces/hypre/HypreUtils.hpp
          interfaces/hypre/HypreSuperlu.hpp )

    list( APPEND linearAlgebra_sources
          interfaces/hypre/HypreMatrix.cpp
          interfaces/hypre/HypreVector.cpp
          interfaces/hypre/HyprePreconditioner.cpp
          interfaces/hypre/HypreSolver.cpp
          interfaces/hypre/HypreInterface.cpp
          interfaces/hypre/HypreSuperlu.cpp )

    list( APPEND dependencyList hypre )

    if( ENABLE_SUITESPARSE )

        list( APPEND linearAlgebra_headers
              interfaces/hypre/HypreSuiteSparse.hpp )

        list( APPEND linearAlgebra_sources
              interfaces/hypre/HypreSuiteSparse.cpp )

        list( APPEND dependencyList suitesparse )

    endif()

endif()

if( ENABLE_PETSC )

    list( APPEND linearAlgebra_headers
          interfaces/petsc/PetscVector.hpp
          interfaces/petsc/PetscMatrix.hpp
          interfaces/petsc/PetscPreconditioner.hpp
          interfaces/petsc/PetscSolver.hpp
          interfaces/petsc/PetscInterface.hpp
          interfaces/petsc/PetscUtils.hpp
          interfaces/petsc/PetscSuperlu.hpp )

    list( APPEND linearAlgebra_sources
          interfaces/petsc/PetscVector.cpp
          interfaces/petsc/PetscMatrix.cpp
          interfaces/petsc/PetscPreconditioner.cpp
          interfaces/petsc/PetscSolver.cpp
          interfaces/petsc/PetscInterface.cpp
          interfaces/petsc/PetscSuperlu.cpp )

    list( APPEND dependencyList petsc )

    if( ENABLE_SUITESPARSE )

        list( APPEND linearAlgebra_headers
              interfaces/petsc/PetscSuiteSparse.hpp )

        list( APPEND linearAlgebra_sources
              interfaces/petsc/PetscSuiteSparse.cpp )

        list( APPEND dependencyList suitesparse )

    endif()

endif()

if( ENABLE_OPENMP )
    set( dependencyList ${dependencyList} openmp )
endif()

if ( ENABLE_CUDA )
    set( dependencyList ${dependencyList} cuda )
endif()

blt_add_library( NAME                  linearAlgebra
                 SOURCES               ${linearAlgebra_sources}
                 HEADERS               ${linearAlgebra_headers}
                 DEPENDS_ON            ${dependencyList}
               )

target_include_directories( linearAlgebra PUBLIC ${CMAKE_CURRENT_LIST_DIR} )

geosx_add_code_checks( PREFIX linearAlgebra )

add_subdirectory( unitTests )

message( "Leaving /src/coreComponents/linearAlgebra/CMakeLists.txt")
