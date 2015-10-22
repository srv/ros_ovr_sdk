cmake_minimum_required(VERSION 2.8.3)
project(oculus_sdk)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED)
	
set(OVR_DIR ovr_sdk_linux_0.5.0.1)

# Sets the SDK libraries / include files / binaries to build into the devel folder in the subdirectory sdk
set(OVR_OUT_DIR ${CATKIN_DEVEL_PREFIX}/${PROJECT_NAME})

# Make must be executed first, when the dependency is located.
execute_process(
	COMMAND make PREFIX=${OVR_OUT_DIR} release
	COMMAND make PREFIX=${OVR_OUT_DIR} install
	WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/sdk/${OVR_DIR}
	)

SET(OVR_SRC_DIR ${PROJECT_SOURCE_DIR}/sdk/${OVR_DIR}/LibOVR/Src)
SET(OVR_INC_DIR ${PROJECT_SOURCE_DIR}/sdk/${OVR_DIR}/LibOVR/Include)
SET(OVRKERNEL_DIR ${PROJECT_SOURCE_DIR}/sdk/${OVR_DIR}/LibOVRKernel/Src)

SET(OVRKERNEL_HEADERS 
    ${OVRKERNEL_DIR}/GL
    ${OVRKERNEL_DIR}/Kernel
    ${OVRKERNEL_DIR}/Tracing
    ${OVRKERNEL_DIR}/Util)

FILE(COPY ${OVRKERNEL_HEADERS}
    DESTINATION ${OVR_OUT_DIR}/include/${PROJECT_NAME}
    PATTERN .svn EXCLUDE)

FILE(GLOB OVR_HEADERS_SRC
    RELATIVE "${OVR_SRC_DIR}"
    "${OVR_SRC_DIR}/*")

foreach(templateFile ${OVR_HEADERS_SRC})
    set(srcTemplatePath ${OVR_SRC_DIR}/${templateFile})
    FILE(COPY ${srcTemplatePath}
         DESTINATION ${OVR_OUT_DIR}/include/${PROJECT_NAME})
endforeach(templateFile)

FILE(GLOB OVR_HEADERS_INC
    RELATIVE "${OVR_INC_DIR}"
    "${OVR_INC_DIR}/*")

foreach(templateFile ${OVR_HEADERS_INC})
    set(srcTemplatePath ${OVR_INC_DIR}/${templateFile})
    FILE(COPY ${srcTemplatePath}
         DESTINATION ${OVR_OUT_DIR}/include/${PROJECT_NAME})
endforeach(templateFile)

catkin_package(
    INCLUDE_DIRS ${OVR_OUT_DIR}/include ${OVR_OUT_DIR}/include/${PROJECT_NAME}
	LIBRARIES ${OVR_OUT_DIR}/lib/libOVRRT64_0.so.5.0.1 
			  ${OVR_OUT_DIR}/lib/libOVRRT64_0.so.5
			  ${OVR_OUT_DIR}/lib/libOVR.a
)
