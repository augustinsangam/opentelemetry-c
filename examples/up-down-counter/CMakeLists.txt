project(up-down-counter-example C)
set(CMAKE_C_STANDARD 11)
add_executable(up-down-counter-example)
target_sources(up-down-counter-example PRIVATE main.c)
target_link_libraries(up-down-counter-example PRIVATE opentelemetry-c)
