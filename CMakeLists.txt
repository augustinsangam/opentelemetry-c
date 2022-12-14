cmake_minimum_required(VERSION 3.1)

project(opentelemetry-c)

find_package(LTTngUST REQUIRED)
find_package(nlohmann_json CONFIG REQUIRED)
find_package(Protobuf CONFIG REQUIRED)
find_package(opentelemetry-cpp CONFIG REQUIRED)

# target = opentelemetry-c
add_library(opentelemetry-c STATIC)
target_include_directories(opentelemetry-c PRIVATE ${OPENTELEMETRY_CPP_INCLUDE_DIRS})
target_link_libraries(opentelemetry-c PRIVATE
	${OPENTELEMETRY_CPP_LIBRARIES}
    lttng-ust
    dl)

target_include_directories(opentelemetry-c PUBLIC include)
target_sources(opentelemetry-c PRIVATE
    src/opentelemetry_c.cpp
    src/utils/map.cpp
    src/utils/socket_carrier.cpp
)

option(LTTNG_EXPORTER_ENABLED "Whether to use LTTng exporter or not. If set to off, OTLP GRPC default exporter will be used" ON)
if (LTTNG_EXPORTER_ENABLED)
    target_compile_definitions(opentelemetry-c PRIVATE LTTNG_EXPORTER_ENABLED)
    target_sources(opentelemetry-c PRIVATE
        src/utils/lttng_metrics_exporter.cpp
        src/utils/lttng_opentelemetry_exporter_tracepoints.cpp
        src/utils/lttng_spans_exporter.cpp
    )
endif()

# Include examples
option(BUILD_EXAMPLES "Whether to build examples or not" ON)
if (BUILD_EXAMPLES)
	add_subdirectory(examples)
endif()
