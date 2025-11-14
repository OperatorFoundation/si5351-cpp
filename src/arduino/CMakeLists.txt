cmake_minimum_required(VERSION 3.31)
project(si5351-arduino)

include(${CMAKE_SOURCE_DIR}/lib/cmake-helpers/arduino-sdk.cmake)

set(CMAKE_CXX_STANDARD 17)
set(SI5351_LIB_DIR ${CMAKE_SOURCE_DIR}/src/cpp)
set(SI5351_LIB_SOURCE_DIR ${SI5351_LIB_DIR}/src)
set(SI5351_ARDUINO_DIR ${CMAKE_SOURCE_DIR}/src/arduino)
set(SI5351_ARDUINO_SOURCE_DIR ${SI5351_ARDUINO_DIR}/src)
set(SI5351_ARDUINO_EXAMPLES_DIR ${SI5351_ARDUINO_DIR}/examples)
set(SI5351_ARDUINO_EXAMPLE_DIR ${SI5351_ARDUINO_EXAMPLES_DIR}/si5351_example)

# Define your sketch file
set(SKETCH_FILE ${SI5351_ARDUINO_EXAMPLE_DIR}/si5351_example.ino)

set(BOARD "rp2040:rp2040:adafruit_feather_rp2350_hstx")
set(PORT "/dev/cu.usbmodem1101")

set(OUTPUT_DIR ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/arduino/rp2350/si5351-i2c)
file(GLOB_RECURSE SI5351_FILES ${SI5351_LIB_SOURCE_DIR}/*.cpp ${SI5351_ARDUINO_SOURCE_DIR}/*.cpp ${SI5351_ARDUINO_SOURCE_DIR}/*.ino)

add_executable(si5351-arduino ${SI5351_FILES})
add_dependencies(si5351-arduino build_si5351_arduino_example)
target_link_libraries(si5351-arduino PRIVATE si5351-cpp)
target_link_arduino_sdk(si5351-arduino rp2040)

add_custom_target(build_si5351_arduino_example ALL
        COMMAND arduino-cli compile
            --fqbn ${BOARD}
            --output-dir ${OUTPUT_DIR}
            --build-property "compiler.cpp.extra_flags=-std=c++17 -D_BSD_SOURCE -D_DEFAULT_SOURCE"
            --library ${SI5351_LIB_DIR}
            --library ${SI5351_ARDUINO_DIR}
            --library ${ONDA}
            --library ${ONDA_ARDUINO}
  ${SKETCH_FILE}
        WORKING_DIRECTORY ${SI5351_ARDUINO_SOURCE_DIR}
        COMMENT "Building si5351-arduino with arduino-cli"
)

add_custom_target(upload_si5351_arduino_example ALL
        COMMAND arduino-cli upload
        -p ${PORT}
        --fqbn ${BOARD}
        ${SI5351_ARDUINO_PROTOCOL_SERVER_DIR}
        COMMENT "Uploading si5351-arduino-protocol-server with arduino-cli"
)
add_dependencies(upload_si5351_arduino_example build_si5351_arduino_example)