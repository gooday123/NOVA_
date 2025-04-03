project (NOVA_BTE)

# Testing Environment Project 들은 라이브러리를 생성하지 않는다. 이들 내부에 각각의 실행파일이 존재하기에 
# 라이브러리를 생성할 경우 최상위 프로젝트에서 문제가 발생할 수 있다. (여러 main 함수가 존재하는 경우)
# 따라서, Testing Environment Project 사용시에는 실행파일 내용을 최상위 프로젝트에서 직접 실행하는 방식으로 진행한다. 
# 즉, 실행파일과 라이브러리는 다른 개념이다

#위 방식으로 할 거면 아래의 코드는 필요없다. 허나 아래의 코드를 살리고 최상위 cmakelist에서 add_subdirectory(ATE)를 추가하고 나머지 
# 하위 프로젝트를 제가하는 방식으로 하면 된다.
# 파일 자동 추가
file(GLOB_RECURSE BTE_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp")
add_executable(NOVA_BTE ${BTE_SOURCES})

# ✅ 헤더 경로 포함: CMake에서 target_include_directories()는 특정 디렉터리를 추가하면 해당 디렉터리 내부의 모든 하위 폴더를 검색할 수 있도록 설정함
target_include_directories(NOVA_BTE PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)

# vcpkg 라이브러리(매니페스트 or 별도 설치) 찾기
# LOG
find_package(spdlog CONFIG REQUIRED)
# JSON
find_package(glaze CONFIG REQUIRED)
# HTTP
find_package(Boost REQUIRED COMPONENTS asio beast)

# 라이브러리 링크 (🔥 반드시 NOVA_BTE이 정의된 이후에 배치)
target_link_libraries(NOVA_BTE PRIVATE
    spdlog::spdlog
    glaze::glaze
    Boost::asio
    Boost::beast
)   
