if (NOT DEFINED FLATBUFFERS_PATH)
  message(FATAL_ERROR "Please set FLATBUFFERS_PATH")
endif()
message(STATUS "FLATBUFFERS_PATH: ${FLATBUFFERS_PATH}")
function(compile_fbs)
  foreach(fbs_file IN LISTS ARGN)
    get_filename_component(name ${fbs_file} NAME_WE)
    set(target_header ${name}_generated.h)
		set(fbs_header ${name}_fbs.h)
    message(STATUS "To compile fbs files ${fbs_file} for ${target_header}")
    add_custom_command(OUTPUT ${target_header}
      DEPENDS ${fbs_file}
      COMMAND ${FLATBUFFERS_PATH}/bin/flatc -o .  --gen-mutable --gen-object-api  --cpp ${fbs_file}
    )
		set(targets ${targets} ${target_header})
		add_custom_command(OUTPUT ${fbs_header}
      DEPENDS ${fbs_file}
      COMMAND ${CMAKE_CURRENT_SOURCE_DIR}/gen_header.sh ${fbs_file} ${name}
    )
    set(targets ${targets} ${fbs_header})
  endforeach()
  add_custom_target(Builder ALL DEPENDS ${targets})
endfunction()

compile_fbs(
  ${CMAKE_CURRENT_SOURCE_DIR}/bmodel.fbs
)
