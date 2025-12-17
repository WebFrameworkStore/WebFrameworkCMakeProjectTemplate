cmake_minimum_required(VERSION 3.27.0)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

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
		GIT_REPOSITORY https://github.com/LazyPanda07/WebFramework.git
		GIT_TAG v{{ cookiecutter.web_framework_version }}
	)

	FetchContent_MakeAvailable(WebFramework)
endif()

target_link_libraries(
	${PROJECT_NAME} PRIVATE
	WebFramework::CXX_API
)
