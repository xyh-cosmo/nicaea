cmake_minimum_required(VERSION 3.2)

# Adding customized cmake module
list(APPEND CMAKE_MODULE_PATH  "${CMAKE_SOURCE_DIR}/cmake/Modules/")

project(nicaea)

		#
		# Loading dependencies
		#
		find_package(FFTW REQUIRED)
		find_package(GSL 1.8 REQUIRED)

    include_directories(${GSL_INCLUDE_DIRS} ${FFTW_INCLUDES})
    link_directories(${GSL_LIBRARY_DIRS} ${FFTW_LIBRARY_DIRS})

		#
		# Compilation flags
		#
		set(CMAKE_C_FLAGS "-Wuninitialized -O3 -fPIC -std=gnu9x")

		#
		# Building nicaea library
		#
		#
		include_directories(Cosmo/include Coyote/include halomodel/include tools/include)
		FILE(GLOB src_cosmo  "${PROJECT_SOURCE_DIR}/Cosmo/src/*.c")
		FILE(GLOB src_halo   "${PROJECT_SOURCE_DIR}/halomodel/src/*.c")
		FILE(GLOB src_coyote "${PROJECT_SOURCE_DIR}/Coyote/src/*.c")
		FILE(GLOB src_tools  "${PROJECT_SOURCE_DIR}/tools/src/*.c")
		add_library(nicaea STATIC ${src_cosmo} ${src_halo} ${src_coyote} ${src_tools})
		target_link_libraries(nicaea ${GSL_LIBRARIES} ${FFTW_LIBRARIES} m)

		#
		# Compiling executables
		#
		#
		add_executable(lensingdemo Demo/lensingdemo.c)
		target_link_libraries(lensingdemo nicaea)

		add_executable(cmb_bao_demo Demo/cmb_bao_demo.c)
		target_link_libraries(cmb_bao_demo nicaea)

		add_executable(cosebi_demo Demo/cosebi_demo.c)
		target_link_libraries(cosebi_demo nicaea)

		add_executable(decomp_eb_demo Demo/decomp_eb_demo.c)
		target_link_libraries(decomp_eb_demo nicaea)

		add_executable(halomodeldemo Demo/halomodeldemo.c)
		target_link_libraries(halomodeldemo nicaea)

		add_executable(sn1ademo Demo/sn1ademo.c)
		target_link_libraries(sn1ademo nicaea)

		add_executable(third_order_demo Demo/third_order_demo.c)
		target_link_libraries(third_order_demo nicaea)


		#
		# Install (by default in the project directory)
		#
		set(CMAKE_INSTALL_PREFIX ${PROJECT_SOURCE_DIR})

		# Install nicaea library
		INSTALL(TARGETS nicaea DESTINATION lib)

		# Install nicaea headers
		FILE(GLOB inc "${PROJECT_SOURCE_DIR}/tools/include/*.h" "${PROJECT_SOURCE_DIR}/Cosmo/include/*.h" "${PROJECT_SOURCE_DIR}/halomodel/include/*.h" "${PROJECT_SOURCE_DIR}/Coyote/include/*.h")
		INSTALL(FILES ${inc} DESTINATION include/nicaea)

		# install nicaea executables
		INSTALL(TARGETS lensingdemo cmb_bao_demo decomp_eb_demo halomodeldemo sn1ademo cosebi_demo third_order_demo DESTINATION bin)

		#
		# Tests
		#
		enable_testing()
		message("-- Creating test module lensingdemo")
		add_test(NAME lensingdemo COMMAND ${CMAKE_INSTALL_PREFIX}/bin/lensingdemo WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/par_files)
		add_test(NAME cmb_bao_demo COMMAND ${CMAKE_INSTALL_PREFIX}/bin/cmb_bao_demo WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/par_files)
