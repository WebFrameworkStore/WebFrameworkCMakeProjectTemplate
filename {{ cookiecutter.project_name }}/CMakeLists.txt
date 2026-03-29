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

{% if "with_python_executors" in cookiecutter.features %}
	set(ARTIFACT_NAME ${ARTIFACT_NAME}WITH_PYTHON_EXECUTORS.ON-)
{% else %}
	set(ARTIFACT_NAME ${ARTIFACT_NAME}WITH_PYTHON_EXECUTORS.OFF-)
{% endif %}

{% if "with_python_api" in cookiecutter.features %}
	set(ARTIFACT_NAME ${ARTIFACT_NAME}BUILD_PYTHON_API.ON-)
{% else %}
	set(ARTIFACT_NAME ${ARTIFACT_NAME}BUILD_PYTHON_API.OFF-)
{% endif %}

{% if "python3.14" in cookiecutter.features %}
	set(ARTIFACT_NAME ${ARTIFACT_NAME}3.14-)
{% elif "python3.13" in cookiecutter.features %}
	set(ARTIFACT_NAME ${ARTIFACT_NAME}3.13-)
{% elif "python3.12" in cookiecutter.features %}
	set(ARTIFACT_NAME ${ARTIFACT_NAME}3.12-)
{% else %}
	set(ARTIFACT_NAME ${ARTIFACT_NAME}-)
{% endif %}

{% if "with_dotnet_executors" in cookiecutter.features %}
	set(ARTIFACT_NAME ${ARTIFACT_NAME}WITH_DOTNET_EXECUTORS.ON-)
{% else %}
	set(ARTIFACT_NAME ${ARTIFACT_NAME}WITH_DOTNET_EXECUTORS.OFF-)
{% endif %}

{% if "with_csharp_api" in cookiecutter.features %}
	set(ARTIFACT_NAME ${ARTIFACT_NAME}BUILD_CSHARP_API.ON-)
{% else %}
	set(ARTIFACT_NAME ${ARTIFACT_NAME}BUILD_CSHARP_API.OFF-)
{% endif %}

{% if "with_cc_api" in cookiecutter.features %}
	set(ARTIFACT_NAME ${ARTIFACT_NAME}BUILD_CC_API.ON)
{% else %}
	set(ARTIFACT_NAME ${ARTIFACT_NAME}BUILD_CC_API.OFF)
{% endif %}

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
