# Generate documentation in HTML and PDF format using Sphinx.

set(GENERATE_DOC TRUE)

# We use the Sphinx documentation generator to render HTML and manual
# pages from the user and reference documentation in ReST format.
find_package(Sphinx 1.7.5 QUIET)
if(NOT SPHINX_FOUND)
  message(WARNING "Unable to find Sphinx documentation generator")
  set(GENERATE_DOC FALSE)
endif(NOT SPHINX_FOUND)

if(GENERATE_DOC)
  # documentation tools
  set(SOURCE_BUILD_DIR "${CMAKE_CURRENT_SOURCE_DIR}")
  # configured documentation tools and intermediate build results
  set(BINARY_BUILD_DIR "${CMAKE_CURRENT_BINARY_DIR}")
  # static ReST documentation sources
  set(SOURCES_DIR "${CMAKE_CURRENT_BINARY_DIR}/_sources")
  set(PDF_SOURCES_DIR "${CMAKE_CURRENT_BINARY_DIR}/_pdfsources")

  # generated ReST documentation sources
  set(REF_SOURCES_DIR "${SOURCES_DIR}/reference")
  # master document with modules index
  set(REF_MASTER_DOC "modules")

  # substitute variables in configuration and scripts
  foreach(file
      conf.py
      sources.cmake
  )
    configure_file(
      "${SOURCE_BUILD_DIR}/${file}.in"
      "${BINARY_BUILD_DIR}/${file}"
      @ONLY
    )
  endforeach(file)

  set(CLEAN_FILES
    "${BINARY_BUILD_DIR}/html"
  )

  add_custom_target(ALL
    DEPENDS "${REF_SOURCES_DIR}/${REF_MASTER_DOC}.rst"
  )

  # Sphinx requires all sources in the same directory tree. As we wish
  # to include generated reference documention from the build tree, we
  # copy static ReST documents to the build tree before calling Sphinx.
  add_custom_target(doc_sources ALL
    "${CMAKE_COMMAND}" -P "${BINARY_BUILD_DIR}/sources.cmake"
  )
  list(APPEND CLEAN_FILES
    "${SOURCES_DIR}"
    "${PDF_SOURCES_DIR}"
  )

  # note the trailing slash to exclude directory name
  #install(DIRECTORY "${SOURCES_DIR}/"
  #    DESTINATION "${CMAKE_INSTALL_DOCDIR}/html"
  #)

  # Sphinx cache with pickled ReST documents
  set(SPHINX_CACHE_DIR "${CMAKE_CURRENT_BINARY_DIR}/_doctrees")
  # HTML output directory
  set(SPHINX_HTML_DIR "${CMAKE_CURRENT_BINARY_DIR}/html")

  # This target builds HTML documentation using Sphinx.
    add_custom_target(doc_html ALL
    ${SPHINX_EXECUTABLE}
      -b html
      -c "${BINARY_BUILD_DIR}"
      -d "${SPHINX_CACHE_DIR}"
      "${SOURCES_DIR}"
      "${SPHINX_HTML_DIR}"
    COMMENT "Building HTML documentation with Sphinx"
  )
  list(APPEND CLEAN_FILES
    "${SPHINX_CACHE_DIR}"
    "${SPHINX_HTML_DIR}"
  )
  add_dependencies(doc_html
    doc_sources
  )
  install(DIRECTORY "${SPHINX_HTML_DIR}"
    DESTINATION "${CMAKE_INSTALL_DOCDIR}/html"
    COMPONENT libs
  )

  # This target builds PDF documentation using Sphinx and LaTeX.
  #if(PDFLATEX_COMPILER)
    # PDF output directory
    set(SPHINX_PDF_DIR "${CMAKE_CURRENT_BINARY_DIR}/pdf")

    add_custom_target(doc_pdf ALL
      ${SPHINX_EXECUTABLE}
	-Q -b latex
	-c "${BINARY_BUILD_DIR}"
	-d "${SPHINX_CACHE_DIR}"
    "${PDF_SOURCES_DIR}"
	"${SPHINX_PDF_DIR}"
      COMMENT "Building PDF documentation with Sphinx"
    )
    add_custom_command(TARGET doc_pdf POST_BUILD
      COMMAND ${CMAKE_MAKE_PROGRAM} LATEXOPTS=-interaction=batchmode
      WORKING_DIRECTORY "${SPHINX_PDF_DIR}"
    )
    list(APPEND CLEAN_FILES
      "${SPHINX_PDF_DIR}"
    )
    add_dependencies(doc_pdf
      doc_sources
    )
    # serialize Sphinx targets to avoid cache conflicts in parallel builds
    #    add_dependencies(doc_pdf
    #  doc_html
    #)
    install(FILES "${SPHINX_PDF_DIR}/libmcsapi_usage.pdf"
      DESTINATION "${CMAKE_INSTALL_DOCDIR}"
      COMPONENT libs
    )
#endif(PDFLATEX_COMPILER)

  # Add output directories to clean target.
  set_directory_properties(PROPERTIES
    ADDITIONAL_MAKE_CLEAN_FILES "${CLEAN_FILES}"
  )

else(GENERATE_DOC)
  message(WARNING "Missing python-sphinx documentation tools, docs will not be built")
endif(GENERATE_DOC)
