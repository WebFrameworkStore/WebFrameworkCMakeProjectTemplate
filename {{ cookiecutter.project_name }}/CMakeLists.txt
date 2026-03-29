cmake_minimum_required(VERSION 3.27.0)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(FETCHCONTENT_QUIET OFF CACHE BOOL "")

set(WEB_FRAMEWORK_VERSION {{ cookiecutter.web_framework_version }})
set(WEB_FRAMEWORK_URL https://github.com/LazyPanda07/WebFramework/releases/download/${WEB_FRAMEWORK_VERSION})

if (UNIX)
	set(ARTIFACT_NAME Linux-)
else()
	set(ARTIFACT_NAME Windows-)
endif()

set(ARTIFACT_NAME ${ARTIFACT_NAME}WITH_PYTHON_EXECUTORS.{{ cookiecutter.with_python_executors }}-)
set(ARTIFACT_NAME ${ARTIFACT_NAME}BUILD_PYTHON_API.{{ cookiecutter.with_python_api }}-)
set(ARTIFACT_NAME ${ARTIFACT_NAME}{{ cookiecutter.python_version }}-)
set(ARTIFACT_NAME ${ARTIFACT_NAME}WITH_DOTNET_EXECUTORS.{{ cookiecutter.with_dotnet_executors }}-)
set(ARTIFACT_NAME ${ARTIFACT_NAME}BUILD_CSHARP_API.{{ cookiecutter.with_csharp_api }}-)
set(ARTIFACT_NAME ${ARTIFACT_NAME}BUILD_CC_API.{{ cookiecutter.with_cc_api }})

if (UNIX)
    set(WEB_FRAMEWORK_URL ${WEB_FRAMEWORK_URL}/${ARTIFACT_NAME}.zip)
else()
    set(WEB_FRAMEWORK_URL ${WEB_FRAMEWORK_URL}/${ARTIFACT_NAME}.zip)
endif()

project({{ cookiecutter.project_name }})

include(FetchContent)

add_library(
	${PROJECT_NAME} SHARED
	src/{{ cookiecutter.executor_name }}.cpp
)

target_include_directories(
	${PROJECT_NAME} PRIVATE
	include
)

if (NOT TARGET WebFramework)
	FetchContent_Declare(
		WebFramework
		URL ${WEB_FRAMEWORK_URL}
	)

	FetchContent_MakeAvailable(WebFramework)

	set(WEB_FRAMEWORK_SDK ${webframework_SOURCE_DIR})
	
	list(APPEND CMAKE_PREFIX_PATH ${WEB_FRAMEWORK_SDK})
endif()

find_package(WebFramework REQUIRED)

target_link_libraries(
	${PROJECT_NAME} PRIVATE
	WebFramework::CXX_API
)

install(TARGETS ${PROJECT_NAME} DESTINATION .)
install(DIRECTORY settings/ DESTINATION .)
install(FILES config.json DESTINATION .)
