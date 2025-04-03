cmake_minimum_required(VERSION 3.29)

# 프로젝트 설정
project(NOVA LANGUAGES C CXX)

# C++23 사용
set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 빌드 아티팩트(실행 파일, 라이브러리 등) 위치 통합
set(BUILD_DIR ${CMAKE_SOURCE_DIR}/out/build)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${BUILD_DIR}/bin)  # 실행 파일 저장 위치
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${BUILD_DIR}/lib)  # 동적 라이브러리 저장 위치
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${BUILD_DIR}/lib)  # 정적 라이브러리 저장 위치
set(CMAKE_BINARY_DIR ${BUILD_DIR})                    # 전체 빌드 파일 저장 위치

# 🟢 최적화 컴파일러 플래그 추가 (MSVC / GCC / Clang 구분)
if (MSVC)
    # Visual Studio (MSVC) 최적화 옵션
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /O2 /GL /arch:AVX2 /fp:fast /Oi /Ot /Gy /Gw")
    set(CMAKE_EXE_LINKER_FLAGS_RELEASE "${CMAKE_EXE_LINKER_FLAGS_RELEASE} /LTCG")
elseif (CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    # GCC 최적화 옵션
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O3 -march=native -ffast-math -funroll-loops -flto")
elseif (CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    # Clang 최적화 옵션
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O3 -march=native -ffast-math -funroll-loops -flto")
endif()

# 런타임 라이브러리 설정 (CMake 3.15 이상에서만 적용 가능)
if (MSVC)
    if (CMAKE_BUILD_TYPE STREQUAL "Debug")
        set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreadedDebug") # /MTd
    else()
        set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded") # /MT
    endif()
endif()


##################### 아래 설정은 NOVA 환경 사용시 모두 주석처리  
if(0)


# 🔹 하위 디렉터리(src 폴더) 추가: (🔥 `add_executable()` 전에 배치해야 함)
#add_subdirectory(ATE)
#add_subdirectory(BTE)
add_subdirectory(LTE)



endif()
###################### 아래 설정은 TE 환경 사용시 모두 주석 처리
if(1)


add_subdirectory(third_party/fmtlog-2.3.0)


# 파일 자동 추가: GLOB_RECURSE는 재귀적으로 작동하여 모든 존재하는 하위 폴더 전부 탐색한다.
file(GLOB_RECURSE SOURCES "${CMAKE_SOURCE_DIR}/src/*.cpp")

# 🔥 실행 파일(.exe) 정의: 이름은 NOVA. Sources는 위에서 정의한 파일 목록. ATE, BTE, LTE를 사용할때는 꺼놔야함
add_executable(NOVA ${SOURCES})  # 🔥 타겟명이 NOVA

# 🔹 NOVA_Request 라이브러리 연결
target_link_libraries(NOVA PRIVATE 

    fmtlog-static

)

# ✅ 헤더 경로 포함: CMake에서 target_include_directories()는 특정 디렉터리를 추가하면 해당 디렉터리 내부의 모든 하위 폴더를 자동으로 검색불가. 그래서 헤더파일 재귀 추가문을 위에서 사용함
# 헤더 경로 포함:  PRIVATE: NOVA에서만 사용되는 헤더 경로. 참고로 NOVA 실행파일을 만드는 것이 아니면 (TE를 이용하는 경우면 헤더 경로 또한 주석처리 필요)
# PRIVATE으로 하면 하위 프로젝트도 개별적으로 설정해줘야함
target_include_directories(NOVA PRIVATE 
    ${CMAKE_SOURCE_DIR}/include
    ${CMAKE_SOURCE_DIR}/third_party/jwt-cpp-0.7.0/include
)


# configs 디렉토리를 런타임 작업 디렉토리에 복사
add_custom_command(TARGET NOVA POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_directory
    ${CMAKE_SOURCE_DIR}/users $<TARGET_FILE_DIR:NOVA>/users
)

endif()