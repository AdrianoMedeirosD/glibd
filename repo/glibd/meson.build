project('GlibD', 'd',
    license: 'cIO-3.0',
    version: '2.2.0'
)

project_soversion = 0

pkg_conf = import('pkgconfig')

source_root = meson.current_source_dir()
build_root = meson.current_build_dir()
gen_dir = 'generated'

#
# Chamadas.
#
glib_dep = dependency('glib-2.0')
gmodule_dep = dependency('gmodule-2.0')
gobject_dep = dependency('gobject-2.0')
gio_dep = dependency('gio-2.0')
#
# As páginas Glib gir fazem parte do plug de
# introspecção gobject.
#
introspection_dep = dependency('gobject-introspection-1.0')

#
# Crie interfaces a partir do GIR.
#
gir_to_d_prog = find_program('girtod')
gir_wrap_dir = join_paths(source_root, 'src')
gir_d_intf_dir = join_paths(build_root, gen_dir)

message('Generating D interfaces from GIR...')
girtod_gen = run_command(gir_to_d_prog,
    '-i', gir_wrap_dir,
    '-o', gir_d_intf_dir,
    '--print-files',
    'relative,' + source_root,
    check: true)
if girtod_gen.returncode() != 0
    error('Unable to build D intefaces from GIR:\n' + girtod_gen.stderr())
endif

gir_bind_dir = include_directories(gen_dir)
#
# Fontes de interface Enlist D GIR.
#
gir_binding_sources = girtod_gen.stdout().strip().split('\n')

glibd = library('glibd-2.0',
    [gir_binding_sources],
    include_directories: [gir_bind_dir],
    dependencies: [glib_dep, gmodule_dep, gobject_dep, gio_dep],
    install: true,
    soversion: project_soversion,
    version: meson.project_version())

install_subdir(join_paths(build_root, gen_dir, 'glib'), install_dir: 'include/d/glibd-2/')
install_subdir(join_paths(build_root, gen_dir, 'gio'), install_dir: 'include/d/glibd-2/')
install_subdir(join_paths(build_root, gen_dir, 'gobject'), install_dir: 'include/d/glibd-2/')
install_subdir(join_paths(build_root, gen_dir, 'gtkd'), install_dir: 'include/d/glibd-2/')

pkg_conf.generate(glibd,
    name: 'glibd-2.0',
    subdirs: 'd/glibd-2',
    version: meson.project_version(),
    requires: [glib_dep, gmodule_dep, gio_dep, gobject_dep],
    description: 'D bindings for the GLib C Utility Library.')

#
# Para uso por outros que incorporam isso como subprojeto.
#
glibd_dep = declare_dependency(
    link_with: [glibd],
    include_directories: [gir_bind_dir]
)

subdir('tests')
