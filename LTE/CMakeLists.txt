project (NOVA_LTE)

# 파일 자동 추가: 절대 ${CMAKE_SOURCE_DIR}를 사용하면 안됨. 이는 최상위 CMakeLists.txt의 경로를 가리키기 때문이다.
file(GLOB_RECURSE LTE_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp")
add_executable(NOVA_LTE ${LTE_SOURCES})


# ✅ 헤더 경로 포함: CMake에서 target_include_directories()는 특정 디렉터리를 추가하면 해당 디렉터리 내부의 모든 하위 폴더를 검색하지는 못하기에 일일이 지정해줘야함
target_include_directories(NOVA_LTE PUBLIC 
    ${CMAKE_CURRENT_SOURCE_DIR}/include
    ${CMAKE_SOURCE_DIR}/third_party/jwt-cpp-0.7.0/include
    ${CMAKE_SOURCE_DIR}/third_party/websocketpp-0.8.2
    ${CMAKE_SOURCE_DIR}/third_party/fmtlog-2.3.0
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/include
)

# vcpkg 라이브러리(매니페스트 or 별도 설치) 찾기
find_package(GTest CONFIG REQUIRED)
find_package(Qt5 COMPONENTS Core REQUIRED)
# LOG
find_package(Boost REQUIRED COMPONENTS log)
# YAML
find_package(yaml-cpp CONFIG REQUIRED)
# JSON
find_package(glaze CONFIG REQUIRED)
find_package(simdjson CONFIG REQUIRED)
find_package(yyjson CONFIG REQUIRED)
find_package(daw-json-link CONFIG REQUIRED)
find_package(Boost REQUIRED COMPONENTS json)
find_package(rapidjson CONFIG REQUIRED)
find_package(nlohmann_json CONFIG REQUIRED)
# HTTP & Websocket
find_package(Boost REQUIRED COMPONENTS asio beast)
find_package(CURL REQUIRED)
find_package(httplib CONFIG REQUIRED)   # GPT가 cpp-httplib로 잘못알려줌
find_package(Drogon CONFIG REQUIRED)
find_package(cpr REQUIRED)
# SSL
find_package(OpenSSL REQUIRED)

# 라이브러리 링크 (🔥 반드시 NOVA_LTE이 정의된 이후에 배치)
target_link_libraries(NOVA_LTE PRIVATE
    yaml-cpp::yaml-cpp
    GTest::gtest
    GTest::gtest_main
    Qt5::Core

    Boost::log
    fmtlog-static

    yaml-cpp::yaml-cpp

    glaze::glaze
    simdjson::simdjson
    yyjson::yyjson
    daw::daw-json-link
    Boost::json
    rapidjson
    nlohmann_json::nlohmann_json

    Boost::asio
    Boost::beast
    CURL::libcurl
    httplib::httplib
    Drogon::Drogon
    cpr::cpr
    OpenSSL::SSL
    OpenSSL::Crypto
)   