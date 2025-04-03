project (NOVA_ATE)

# 파일 자동 추가
file(GLOB_RECURSE ATE_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp")
add_executable(NOVA_ATE ${ATE_SOURCES})

# ✅ 헤더 경로 포함: CMake에서 target_include_directories()는 특정 디렉터리를 추가하면 해당 디렉터리 내부의 모든 하위 폴더를 검색할 수 있도록 설정함
target_include_directories(NOVA_ATE PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)

# vcpkg 라이브러리(매니페스트 or 별도 설치) 찾기
# LOG
find_package(spdlog CONFIG REQUIRED)
# JSON
find_package(glaze CONFIG REQUIRED)
# HTTP
find_package(Boost REQUIRED COMPONENTS asio beast)

# 라이브러리 링크 (🔥 반드시 CMakeProject1이 정의된 이후에 배치)
target_link_libraries(NOVA_ATE PRIVATE
    spdlog::spdlog
    glaze::glaze
    Boost::asio
    Boost::beast
)   
