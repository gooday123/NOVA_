project (NOVA_Request)

# 소스파일 자동 추가
file(GLOB_RECURSE REQUEST_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp")

# ✅ 라이브러리 생성
add_library(NOVA_Request STATIC ${REQUEST_SOURCES})

# 헤더파일 자동 추가
file(GLOB_RECURSE REQUEST_HEADERS 
    "${CMAKE_CURRENT_SOURCE_DIR}/include/*.hpp" 
    "${CMAKE_CURRENT_SOURCE_DIR}/include/*.tpp"
)

# 🔹 중복 제거 및 디렉터리 경로만 추출
set(REQUEST_INCLUDE_DIRS "")
foreach(file ${REQUEST_HEADERS})
    get_filename_component(dir ${file} DIRECTORY)
    list(APPEND REQUEST_INCLUDE_DIRS ${dir})
endforeach()
list(REMOVE_DUPLICATES REQUEST_INCLUDE_DIRS)  # 중복 제거

# ✅ REQUEST_INCLUDE_DIRS 내용 출력 (디버깅용)
#[[message(STATUS "REQUEST_INCLUDE_DIRS:")
foreach(dir ${REQUEST_INCLUDE_DIRS})
    message(STATUS " - ${dir}")
endforeach()]]

# ✅ 헤더 경로 포함: CMake에서 target_include_directories()는 특정 디렉터리를 추가하면 해당 디렉터리 내부의 모든 하위 폴더를 자동으로 검색불가. 그래서 헤더파일 재귀 추가문을 위에서 사용함
# PUBLIC: NOVA_Request 라이브러리를 사용하는 프로젝트에서 NOVA_Request의 헤더파일을 사용할 수 있도록 설정
target_include_directories(NOVA_Request PUBLIC 
    ${REQUEST_INCLUDE_DIRS}
    ${CMAKE_SOURCE_DIR}/third_party/jwt-cpp-0.7.0/include
    ${CMAKE_SOURCE_DIR}/third_party/fmtlog-2.3.0
)

# vcpkg 라이브러리(매니페스트 or 별도 설치) 찾기
# LOG
# JSON
find_package(glaze CONFIG REQUIRED)
# HTTP
find_package(Boost REQUIRED COMPONENTS asio beast)
#SSL
find_package(OpenSSL REQUIRED)

# 라이브러리 링크 (🔥 반드시 NOVA_Request이 정의된 이후에 배치)
target_link_libraries(NOVA_Request PUBLIC
    glaze::glaze
    Boost::asio
    Boost::beast
    OpenSSL::SSL
    OpenSSL::Crypto
    fmtlog-static
)   


