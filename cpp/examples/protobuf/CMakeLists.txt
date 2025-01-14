INCLUDE(FindProtobuf)
FIND_PACKAGE(Protobuf 3 REQUIRED)
INCLUDE_DIRECTORIES(${PROTOBUF_INCLUDE_DIR} ${Protobuf_INCLUDE_DIRS})

FILE(GLOB all_protos "proto/foxglove/*.proto")

set(proto_sources FILES
    autogenerated/foxglove/PointCloud.pb.h
    autogenerated/foxglove/PointCloud.pb.cc
    autogenerated/foxglove/Vector3.pb.h
    autogenerated/foxglove/Vector3.pb.cc
    autogenerated/foxglove/Pose.pb.h
    autogenerated/foxglove/Pose.pb.cc
    autogenerated/foxglove/Quaternion.pb.h
    autogenerated/foxglove/Quaternion.pb.cc
    autogenerated/foxglove/PackedElementField.pb.h
    autogenerated/foxglove/PackedElementField.pb.cc
)

add_custom_command(
    OUTPUT ${proto_sources}
    COMMAND ${CMAKE_COMMAND} -E make_directory autogenerated
    COMMAND ${Protobuf_PROTOC_EXECUTABLE} --proto_path=${CMAKE_CURRENT_SOURCE_DIR}/proto --cpp_out=autogenerated ${all_protos}
)

add_library(pointcloud_proto_lib ${proto_sources})
target_link_libraries(pointcloud_proto_lib INTERFACE ${Protobuf_LIBRARIES})
target_include_directories(pointcloud_proto_lib PUBLIC ${CMAKE_CURRENT_BINARY_DIR}/autogenerated)
# Unfortunately the protobuf library headers include some unused parameters, which have to be
# downgraded back to warnings for all targets that depends on them.
target_compile_options(pointcloud_proto_lib PUBLIC -Wno-error=unused-parameter)

add_executable(example_protobuf_writer writer.cpp)
target_link_libraries(example_protobuf_writer ${CONAN_LIBS} pointcloud_proto_lib)

add_executable(example_protobuf_dynamic_reader dynamic_reader.cpp)
target_link_libraries(example_protobuf_dynamic_reader ${CONAN_LIBS} ${Protobuf_LIBRARIES})
target_compile_options(example_protobuf_dynamic_reader PUBLIC -Wno-error=unused-parameter)

add_executable(example_protobuf_static_reader static_reader.cpp)
target_link_libraries(example_protobuf_static_reader ${CONAN_LIBS} pointcloud_proto_lib)
target_compile_options(example_protobuf_static_reader PUBLIC -Wno-error=unused-parameter)
