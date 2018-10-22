html_theme = 'https://github.com/hotdoc/hotdoc_lumen_theme/releases/download/0.6/hotdoc_lumen_theme-0.6.tar.xz?sha256=0e2f175f4cf8c00ed7ac5014e30c806a294b0d3818565eb74e1424a948e8a452'
build_hotdoc = false

hotdoc_extension_dir = []
required_hotdoc_extensions = []
if gst_dep.type_name() == 'internal'
    gst_proj = subproject('gstreamer')
    plugins_cache_generator = gst_proj.get_variable('plugins_cache_generator')
    if gst_proj.get_variable('build_hotdoc')
        hotdoc_extension_dir = [gst_proj.get_variable('hotdoc_extension_dir')]
    else
        required_hotdoc_extensions += ['gst-extension']
    endif
else
    required_hotdoc_extensions += ['gst-extension']
    plugins_cache_generator = find_program(join_paths(gst_dep.get_pkg_variable('libexecdir'), 'gstreamer-' + api_version, 'gst-plugins-doc-cache-generator'))
endif

plugins_cache = join_paths(meson.current_source_dir(), 'gst_plugins_cache.json')
if plugins_cache_generator.found()
    plugins_doc_dep = custom_target('libav-plugins-doc-cache',
        command: [plugins_cache_generator, plugins_cache, '@INPUT@'],
        input: plugins,
        output: 'gst_plugins_cache.json',
    )
else
    warning('GStreamer plugin inspector for documentation not found, can\'t update the cache')
endif


build_hotdoc = true
hotdoc = import('hotdoc')
docconf = configuration_data()
docconf.set('GST_API_VERSION', api_version)

hotdoc = import('hotdoc')
if required_hotdoc_extensions != [] and not hotdoc.has_extensions(required_hotdoc_extensions)
    if get_option('doc').enabled()
        error('Documentation enabled but gi-extension missing')
    endif

    message('@0@ extensions not found, not building documentation'.format(required_hotdoc_extensions))
    subdir_done()
endif

libs_doc = []
plugins_doc = [hotdoc.generate_doc('libav',
    project_version: api_version,
    sitemap: 'sitemap.txt',
    index: 'index.md',
    gst_index: 'index.md',
    gst_smart_index: true,
    gst_c_sources: ['../ext/*/*.[ch]',],
    gst_cache_file: plugins_cache,
    gst_plugin_name: 'libav',
    dependencies: [gst_dep, gstlibav_plugin],
    extra_extensions_paths: hotdoc_extension_dir,
    disable_incremental_build: true,
    html_theme: html_theme,
)]
