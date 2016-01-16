#!python
# -*-python-*-
#
# SCons build script for googletest and googlemock
# Author: Semih Cemiloglu <semih.cemiloglu@gmail.com>
# Initial: 2016-01-16
# This file and all associated files have the same copyright terms
# as the googletest project.
#
import sys
import SConsLib


# Construct variables object by merging command-line settings and
# configuration file.
vars = SConsLib.constructVariables('SConsCfg.py')

# Instantiate Scons environment
env = Environment(
    variables = vars,
    MSVC_VERSION='11.0', # MSVC 2012 choose any version you prefer
    TARGET_ARCH='x86'   # x86 -> 32bit or x86_64 => 64bit
)

# Check for unrecognized variables and warn
SConsLib.checkUnknownVariables(vars)

# Setup Scons environment to be used during build
SConsLib.setupEnvironment(env)

# Further refinements to the environment
Help(vars.GenerateHelpText(env))
env.Decider('timestamp-newer')
env.SetOption('implicit_cache', 1)


# Set include directories
env.AppendUnique(CPPPATH = [
    '#/googletest','#/googletest/include',
    '#/googlemock','#/googlemock/include'
])


# Extra flags for compiler specific to this project
if sys.platform == 'win32':
    if 'cl' in env['CC']:
        env.AppendUnique(CPPDEFINES = ['_VARIADIC_MAX=10'])
        env.AppendUnique(CCFLAGS = ['-EHsc' ])
        env.AppendUnique(LINKFLAGS = ['-subsystem:console'])


# Source directories that we expect to find SConscript files:
src_dirs = [
    'googletest/src', 'googletest/test',
    'googlemock/src', 'googlemock/test'
]

# Read and process SConscript files
SConsLib.readSConscriptFiles(env, src_dirs)

# Use progress indicator to get feedback from SCons
SConsLib.useProgress()


 
