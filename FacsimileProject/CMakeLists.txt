cmake_minimum_required(VERSION 3.9)

project(ProjectName LANGUAGES CXX C VERSION 1.0.0)

# Set C++ Standard
################################################################################
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# IMPOSE WARNINGS ON DEBUG
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -Wall -Wextra -pedantic-errors")

# IMPOSE CXX FLAGS FOR WINDOWS
if (WIN32)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wa,-mbig-obj")
endif (WIN32)

# Add dependencies
################################################################################
set(CMAKE_PREFIX_PATH ${CMAKE_PREFIX_PATH};${CMAKE_CURRENT_SOURCE_DIR}/../externals/Main_Install/eigen3)
set(CMAKE_PREFIX_PATH ${CMAKE_PREFIX_PATH};${CMAKE_CURRENT_SOURCE_DIR}/../externals/Main_Install/googletest)

## Eigen3
find_package(Eigen3 CONFIG REQUIRED)
list(APPEND ProjectName_LINKED_LIBRARIES PUBLIC Eigen3::Eigen)

## Threads
find_package(Threads REQUIRED)
list(APPEND ProjectName_LINKED_LIBRARIES PRIVATE Threads::Threads)

## GTest
find_package(GTest REQUIRED)

list(APPEND ProjectName_LINKED_LIBRARIES PRIVATE GTest::gtest)
list(APPEND ProjectName_LINKED_LIBRARIES PRIVATE GTest::gtest_main)
list(APPEND ProjectName_LINKED_LIBRARIES PRIVATE GTest::gmock)
list(APPEND ProjectName_LINKED_LIBRARIES PRIVATE GTest::gmock_main)

# Copy file
###############################################################################

# Insert Sources
################################################################################
add_subdirectory(src)
list(APPEND ${CMAKE_NAME_PROJECT}_sources ${src_sources})
list(APPEND ${CMAKE_NAME_PROJECT}_headers ${src_headers})
list(APPEND ${CMAKE_NAME_PROJECT}_includes ${src_includes})
list(APPEND ${CMAKE_NAME_PROJECT}_LINKED_LIBRARIES ${src_includes})

add_subdirectory(src_test)
list(APPEND ${CMAKE_NAME_PROJECT}_TEST_sources ${src_test_sources})
list(APPEND ${CMAKE_NAME_PROJECT}_TEST_headers ${src_test_headers})
list(APPEND ${CMAKE_NAME_PROJECT}_TEST_includes ${src_test_includes})
list(APPEND ${CMAKE_NAME_PROJECT}_TEST_LINKED_LIBRARIES ${src_test_includes})

# Create executable
################################################################################
add_executable(${CMAKE_PROJECT_NAME} main.cpp ${${CMAKE_NAME_PROJECT}_sources} ${${CMAKE_NAME_PROJECT}_headers}) # ""forza ad essere una stringa unica, per il main non serve fare sta cosa perchè sta nella cartella dove c'è il cmakelist
target_link_libraries(${CMAKE_PROJECT_NAME} PUBLIC ${${CMAKE_NAME_PROJECT}_LINKED_LIBRARIES})
target_include_directories(${CMAKE_PROJECT_NAME} PUBLIC ${${CMAKE_NAME_PROJECT}_includes})
target_compile_options(${PROJECT_NAME} PUBLIC -fPIC)

#per aggiungere un altro eseguibile
add_executable(${CMAKE_PROJECT_NAME}_TEST main_test.cpp ${${CMAKE_NAME_PROJECT}_TEST_sources} ${${CMAKE_NAME_PROJECT}_TEST_headers}) # ""forza ad essere una stringa unica, per il main non serve fare sta cosa perchè sta nella cartella dove c'è il cmakelist
target_link_libraries(${CMAKE_PROJECT_NAME}_TEST PUBLIC ${${CMAKE_NAME_PROJECT}_LINKED_LIBRARIES} ${${CMAKE_NAME_PROJECT}_TEST_LINKED_LIBRARIES})
target_include_directories(${CMAKE_PROJECT_NAME}_TEST PUBLIC ${${CMAKE_NAME_PROJECT}_includes} ${${CMAKE_NAME_PROJECT}_TEST_includes})
target_compile_options(${PROJECT_NAME}_TEST PUBLIC -fPIC)
