#!python
# -*-python-*-

import os
import sys

# Get the mode flag from command line
# Default is the release
mode = ARGUMENTS.get('mode','debug')

allowed_modes = {'debug':'dbg', 'release':'rel'}
if not (mode in allowed_modes.keys()):
    print "Error: Expected %s, found %s: " % (allowed_modes.keys(), mode)
    Exit(1)

print "Will build for %s mode..." % mode


use_plat = ARGUMENTS.get('use_plat',0)
use_test = ARGUMENTS.get('use_test',None)

bld_root  = 'sbuild'
platform  = sys.platform
if use_plat:
    print "Using platform '%s' in build output..." % platform
    bld_plat  = '%s/%s' % (bld_root, platform)
else:
    bld_plat = bld_root
    
bld_dir   = '%s/%s' % (bld_plat, allowed_modes[mode])

dump = ARGUMENTS.get('dump',0)



env = Environment(
    MSVC_VERSION='11.0', # VMSVC 2012 choose any version you prefer
    TARGET_ARCH='x86'   # x86 -> 32bit or x86_64 => 64bit
)

env.Decider('timestamp-newer')
env.SetOption('implicit_cache', 1)


env.AppendUnique(
    PRJ_BLD_ROOT = Dir(bld_root),
    PRJ_BLD      = Dir(bld_dir),
    PRJ_BLD_BIN  = Dir('%s/bin' % bld_dir),
    PRJ_BLD_LIB  = Dir('%s/lib' % bld_dir),
    PRJ_EXES     = {},
    PRJ_LIBS     = {},
    PRJ_OBJS     = {}
)

env.AppendUnique(CPPPATH = ['#/googletest','#/googletest/include','#/googlemock','#/googlemock/include'])


if platform == 'win32':
    if 'cl' in env['CC']:
        env.AppendUnique(CPPDEFINES = ['_VARIADIC_MAX=10'])
        env.AppendUnique(CCFLAGS = ['-EHsc' ])
        env.AppendUnique(LINKFLAGS = ['-subsystem:console'])
        if mode == 'debug':
            env.MergeFlags('-MTd -W1 -D_DEBUG -RTCs -Zi')
        else:
            env.MergeFlags('-MT -O1 -DNDEBUG')
    else:
        print "Unrecognized compiler: %s" % env['CC']
        Exit(1)
elif 'linux' in platform:
    # Replace LINKCOM to position LINKFLAGS at the very end of link command line
    env.Replace(LINKCOM='$LINK -o $TARGET $__RPATH $SOURCES $_LIBDIRFLAGS $_LIBFLAGS $LINKFLAGS')
    #env.AppendUnique(LINKFLAGS = ['-lm' ])
    env.AppendUnique(CCFLAGS = ['-fPIC','-DPIC'])
    if mode == 'debug':
        env.MergeFlags('-g')
    else:
        env.MergeFlags('-O2 -w')
else:
    print "Unrecognized platform: %s" % platform
    Exit(1)


src_dirs = [
    'googletest/src', 'googletest/test',
    'googlemock/src', 'googlemock/test'
]

for sd in src_dirs:
    env.SConscript(
        '%s/SConscript' % sd,
        variant_dir='%s/%s' % (bld_dir,sd),
        duplicate=0,
        exports='env'
    )

for lib in env['PRJ_LIBS'].values():
    env.Install("$PRJ_BLD_LIB", lib)

for exe in env['PRJ_EXES'].values():
    env.Install("$PRJ_BLD_BIN", exe)


Help("""
Type: 'scons' to build all libraries and tests.
      'scons mode=<mode>' where mode can be debug or release.
      'scons dump=1' to dump construction environment values.
      'scons use_test=1' to run all unit tests (after building everything).

""")

if dump:
    print env.Dump()

