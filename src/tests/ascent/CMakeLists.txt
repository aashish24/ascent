###############################################################################
# Copyright (c) 2015-2019, Lawrence Livermore National Security, LLC.
#
# Produced at the Lawrence Livermore National Laboratory
#
# LLNL-CODE-716457
#
# All rights reserved.
#
# This file is part of Ascent.
#
# For details, see: http://ascent.readthedocs.io/.
#
# Please also read ascent/LICENSE
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions are met:
#
# * Redistributions of source code must retain the above copyright notice,
#   this list of conditions and the disclaimer below.
#
# * Redistributions in binary form must reproduce the above copyright notice,
#   this list of conditions and the disclaimer (as noted below) in the
#   documentation and/or other materials provided with the distribution.
#
# * Neither the name of the LLNS/LLNL nor the names of its contributors may
#   be used to endorse or promote products derived from this software without
#   specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
# AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
# IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
# ARE DISCLAIMED. IN NO EVENT SHALL LAWRENCE LIVERMORE NATIONAL SECURITY,
# LLC, THE U.S. DEPARTMENT OF ENERGY OR CONTRIBUTORS BE LIABLE FOR ANY
# DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
# DAMAGES  (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS
# OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
# HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT,
# STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING
# IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
# POSSIBILITY OF SUCH DAMAGE.
#
###############################################################################


################################
# Unit Tests
################################

################################
# Core Ascent Unit Tests
################################
set(BASIC_TESTS t_ascent_smoke
                t_ascent_runtime_options
                t_ascent_utils
                t_ascent_empty_runtime
                t_ascent_error_handling
                t_ascent_cinema_a
                t_ascent_render_2d
                t_ascent_render_3d
                t_ascent_web_flow
                t_ascent_web_main
                t_ascent_clip
                t_ascent_clip_with_field
                t_ascent_contour
                t_ascent_iso_volume
                t_ascent_pipelines_to_pipelines
                t_ascent_threshold
                t_ascent_slice
                t_ascent_vector_magnitude
                t_ascent_flow_runtime
                t_ascent_rover
                t_ascent_lagrangian
                t_ascent_log
                t_ascent_hola)

set(MPI_TESTS  t_ascent_mpi_smoke
               t_ascent_mpi_empty_runtime
               t_ascent_mpi_render_2d
               t_ascent_mpi_render_3d
               t_ascent_mpi_slice)

# t_ascent_hola_mpi uses 8 mpi tasks, so its added manually


# include the "ascent" pipeline
if(VTKM_FOUND)
   list(APPEND BASIC_TESTS t_ascent_ascent_runtime
                           t_ascent_vtkh_data_adapter)
   list(APPEND MPI_TESTS   t_ascent_mpi_ascent_runtime
                           t_ascent_mpi_relay_extract)
endif()

# adios tests
if(ADIOS_FOUND)
   list(APPEND MPI_TESTS t_ascent_mpi_adios_extract)
endif()

if(PYTHON_FOUND AND ENABLE_PYTHON)
    list(APPEND BASIC_TESTS t_ascent_python_extract)
    list(APPEND MPI_TESTS t_ascent_mpi_python_extract)
endif()


################################
# Add main tests
################################
message(STATUS "Adding ascent lib unit tests")
foreach(TEST ${BASIC_TESTS})
    add_cpp_test(TEST ${TEST} DEPENDS_ON ascent)
endforeach()

################################
# Add optional tests
################################

if(MPI_FOUND AND ENABLE_MPI)
    message(STATUS "MPI enabled: Adding related unit tests")
    foreach(TEST ${MPI_TESTS})
        # this uses 2 procs
        add_cpp_mpi_test(TEST ${TEST} NUM_MPI_TASKS 2 DEPENDS_ON ascent_mpi)
    endforeach()

    # add the hola mpi test which uses 8 ranks
    add_cpp_mpi_test(TEST t_ascent_hola_mpi NUM_MPI_TASKS 8 DEPENDS_ON ascent_mpi)

else()
    message(STATUS "MPI disabled: Skipping related tests")
endif()

if(PYTHON_FOUND AND ENABLE_PYTHON)
    add_subdirectory("python")
else()
    message(STATUS "Python disabled: Skipping ascent python module tests")
endif()


if(FORTRAN_FOUND AND ENABLE_FORTRAN)
     add_subdirectory("fortran")
else()
     message(STATUS "Fortran disabled: Skipping ascent fortran interface tests")
endif()


