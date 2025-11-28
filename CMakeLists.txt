cmake_minimum_required(VERSION 3.31.5)
project(TestProject)

# Set the C++ standard
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Specify Assimp paths
set(ASSIMP_ROOT_DIR "${CMAKE_CURRENT_SOURCE_DIR}/libraries/assimp")
set(ASSIMP_INCLUDE_DIR "${ASSIMP_ROOT_DIR}/include")
set(ASSIMP_LIBRARY_DIR "${ASSIMP_ROOT_DIR}/lib")

# === GLAD ===
# Create a static library for GLAD.
# Adjust the path below if your glad source file is not at glad/src/glad.c.
add_library(glad STATIC
    libraries/glad/src/glad.c
)
# Specify the include directory for GLAD.
target_include_directories(glad PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/libraries/glad/include)

# === GLFW ===
# Add GLFW as a subdirectory if its CMakeLists.txt exists in the glfw-3.4 folder.
# This will build the GLFW library as part of your project.
add_subdirectory(libraries/glfw-3.4)

# Note: The GLFW target name depends on its CMake configuration.
# Often, it is set to "glfw" or "glfw3". Check the GLFW CMakeLists.txt if linking issues occur.

# === Main Executable ===
# Create an executable from your test.cpp file.
add_executable(test_app src/test.cpp src/stb_image.cpp)

set_target_properties(test_app PROPERTIES
    INSTALL_RPATH "/Users/huangkangyi/Desktop/OpenGL-Learn/build/"
)
# Add include directories for test_app (if needed, e.g., if you have custom header folders).
# In this case, GLAD's include directory is already provided to the glad library.
target_include_directories(test_app PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}/libraries
    ${CMAKE_CURRENT_SOURCE_DIR}/libraries/glad/include
    ${CMAKE_CURRENT_SOURCE_DIR}/libraries/glfw-3.4/include
	${CMAKE_CURRENT_SOURCE_DIR}/libraries/glm-1.0.0
    ${CMAKE_CURRENT_SOURCE_DIR}/libraries/imgui/{*.cpp,*.h}
    ${CMAKE_CURRENT_SOURCE_DIR}/libraries/imgui/backends/imgui_impl_opengl3.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/libraries/imgui/misc/cpp/imgui_stdlib.*
	# ${CMAKE_CURRENT_SOURCE_DIR}libraries/stb_image.h
    # ${CMAKE_CURRENT_SOURCE_DIR}libraries/glm-1.0.0
    ${ASSIMP_INCLUDE_DIR}
)

# Link the external libraries to your executable.
# Here we link the GLAD library we created and the GLFW library built in the subdirectory.
target_link_libraries(test_app PRIVATE 
    glad 
    glfw 
    "${ASSIMP_LIBRARY_DIR}/libassimp.dylib"  # For macOS
    # "${ASSIMP_LIBRARY_DIR}/libassimp.so"   # For Linux
    # "${ASSIMP_LIBRARY_DIR}/assimp.dll"     # For Windows
)

# Set RPATH for the executable to find the dynamic library
set_target_properties(test_app PROPERTIES
    BUILD_WITH_INSTALL_RPATH TRUE
    INSTALL_RPATH "${ASSIMP_LIBRARY_DIR}"
)
