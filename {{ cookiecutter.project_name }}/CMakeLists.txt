cmake_minimum_required(VERSION 3.27.0)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(FETCHCONTENT_QUIET OFF CACHE BOOL "")

set(WEB_FRAMEWORK_VERSION {{ cookiecutter.web_framework_version }})
set(WEB_FRAMEWORK_URL https://github.com/LazyPanda07/WebFramework/releases/download/${WEB_FRAMEWORK_VERSION})

if (UNIX)
    set(WEB_FRAMEWORK_URL ${WEB_FRAMEWORK_URL}/linux.zip)
else()
    set(WEB_FRAMEWORK_URL ${WEB_FRAMEWORK_URL}/windows.zip)
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

	if (UNIX)
		set(WEB_FRAMEWORK_SDK ${webframework_SOURCE_DIR})
	else()
	    set(WEB_FRAMEWORK_SDK ${webframework_SOURCE_DIR}/Release_Windows)
	endif()
	
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
