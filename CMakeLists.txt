cmake_minimum_required(VERSION 3.5)
project(mockito_vendor)

find_package(ament_cmake_export_jars REQUIRED)

include(UseJava)

# Find jars for Mockito and it's dependencies
set(jars_to_find
  "mockito-core"
  "byte-buddy"
  "byte-buddy-agent"
  "objenesis"
)

# Download 2.23.0 (version provided by Ubuntu Focal)
set(mockito-core_version "2.23.0")
set(mockito-core_sha256 "637991bfc37fdd2a7adfe610f2ee5290acf9b15a7e4347bc3c96c61ed9dfe043")
set(mockito-core_url "https://search.maven.org/remotecontent?filepath=org/mockito/mockito-core/${mockito-core_version}/mockito-core-${mockito-core_version}.jar")

# Dependencies of Mockito 2.23.0
set(byte-buddy_version "1.9.0")
set(byte-buddy_sha256 "695d00c430d7111196c5be8de2ad7d289abfba91636fa4003bd8950d501b8740")
set(byte-buddy_url "https://search.maven.org/remotecontent?filepath=net/bytebuddy/byte-buddy/${byte-buddy_version}/byte-buddy-${byte-buddy_version}.jar")

set(byte-buddy-agent_version "1.9.0")
set(byte-buddy-agent_sha256 "f1b5865730f0ae36539d91984d2dbf38ce7640c3db4c70b1a778395e579b3229")
set(byte-buddy-agent_url "https://search.maven.org/remotecontent?filepath=net/bytebuddy/byte-buddy-agent/${byte-buddy-agent_version}/byte-buddy-agent-${byte-buddy-agent_version}.jar")

set(objenesis_version "2.6")
set(objenesis_sha256 "5e168368fbc250af3c79aa5fef0c3467a2d64e5a7bd74005f25d8399aeb0708d")
set(objenesis_url "https://search.maven.org/remotecontent?filepath=org/objenesis/objenesis/${objenesis_version}/objenesis-${objenesis_version}.jar")

set(${PROJECT_NAME}_exported_jars "")
foreach(jar_name IN LISTS jars_to_find)
  message(STATUS "Finding JAR '${jar_name}'")
  find_jar(${jar_name}_path NAME "${jar_name}")
  if(NOT ${jar_name}_path)
    message(STATUS "JAR not found '${jar_name}', downloading from '${${jar_name}_url}")
    set(${jar_name}_download_path "${CMAKE_CURRENT_BINARY_DIR}/jars/${jar_name}-${${jar_name}_version}.jar")
    file(DOWNLOAD ${${jar_name}_url} ${${jar_name}_download_path} EXPECTED_HASH SHA256=${${jar_name}_sha256})
    install(FILES ${${jar_name}_download_path}
      DESTINATION "share/${PROJECT_NAME}/java"
    )
    set(${jar_name}_path "share/${PROJECT_NAME}/java/${jar_name}-${${jar_name}_version}.jar")
  endif()
  list(APPEND ${PROJECT_NAME}_exported_jars "${${jar_name}_path}")
endforeach()

ament_export_jars(${${PROJECT_NAME}_exported_jars})

ament_package()
