# -*- python -*- 

import os
import sys
import platform

# Home made tests
sys.path.append('SconsTests')
import wxconfig 
import utils

# Some features needs at least scons 0.98
EnsureSConsVersion(1, 2)

# TODO: how do we use it in help?
name="Dolphin"
version="SVN"
description="A wii/gamecube emulator"
license="GPL v2"

warnings = [
    'all',
    'write-strings',
    'shadow',
    'pointer-arith',
    'packed',
    'no-conversion',
#	'no-unused-result',  (need a newer gcc for this?)
    ]
compileFlags = [
    '-fno-exceptions',
    '-fno-strict-aliasing',
    '-msse2',
#    '-fomit-frame-pointer',
#    '-flto',
    ]

cppDefines = [
    ( '_FILE_OFFSET_BITS', 64),
    '_LARGEFILE_SOURCE',
    'GCC_HASCLASSVISIBILITY',
    ]

basedir = os.getcwd()+ '/'

include_paths = [
    basedir + 'Source/Core/Common/Src',
    basedir + 'Source/Core/DiscIO/Src',
    basedir + 'Source/PluginSpecs',
    basedir + 'Source/Core/Core/Src',
    basedir + 'Source/Core/DebuggerWX/Src',
    basedir + 'Externals/Bochs_disasm',
    basedir + 'Externals/Lua',
    basedir + 'Externals/WiiUseSrc/Src',
    basedir + 'Source/Core/VideoCommon/Src',
    basedir + 'Source/Core/InputCommon/Src',
    basedir + 'Source/Plugins/InputPluginCommon/Src',
    basedir + 'Source/Core/AudioCommon/Src',
    basedir + 'Source/Core/DebuggerUICommon/Src',
    basedir + 'Source/Core/DSPCore/Src',
    ]

dirs = [
    'Externals/Bochs_disasm',
    'Externals/Lua',
    'Externals/WiiUseSrc/Src', 
    'Source/Core/Common/Src',
    'Source/Core/Core/Src',
    'Source/Core/DiscIO/Src',
    'Source/Core/VideoCommon/Src',
    'Source/Core/InputCommon/Src',
    'Source/Core/AudioCommon/Src',
    'Source/Core/DebuggerUICommon/Src',
    'Source/Core/DSPCore/Src',
    'Source/DSPTool/Src',
    'Source/Plugins/Plugin_VideoOGL/Src',
    'Source/Plugins/Plugin_VideoSoftware/Src',
    'Source/Plugins/Plugin_DSP_HLE/Src',
    'Source/Plugins/Plugin_DSP_LLE/Src',
    'Source/Plugins/Plugin_GCPad/Src',
    'Source/Plugins/Plugin_GCPadNew/Src',
    'Source/Plugins/Plugin_Wiimote/Src',
    'Source/Core/DolphinWX/Src',
    'Source/Core/DebuggerWX/Src',
    'Source/UnitTests/',
    'Source/Plugins/InputPluginCommon/Src/',
    'Source/Plugins/Plugin_WiimoteNew/Src/',
    ]

builders = {}
if sys.platform == 'darwin':
    from plistlib import writePlist
    def createPlist(target, source, env):
        properties = {}
        for srcNode in source:
            properties.update(srcNode.value)
            for dstNode in target:
                writePlist(properties, str(dstNode))
    builders['Plist'] = Builder(action = createPlist)

# handle command line options
vars = Variables('args.cache')

vars.AddVariables(
    BoolVariable('verbose', 'Set for compilation line', False),
    BoolVariable('bundle', 'Set to create bundle', False),
    BoolVariable('lint', 'Set for lint build (extra warnings)', False),
    BoolVariable('nowx', 'Set For Building with no WX libs (WIP)', False),
    BoolVariable('openal', 'Build with OpenAL', False),
    BoolVariable('noao', 'Build without AO', False),
    BoolVariable('wxgl', 'Set For Building with WX GL libs (WIP)', False),
    BoolVariable('opencl', 'Build with OpenCL', False),
    BoolVariable('nojit', 'Remove entire jit cores', False),
    BoolVariable('shared_soil', 'Use system shared libSOIL', False),
    BoolVariable('shared_lzo', 'Use system shared liblzo2', False),
    BoolVariable('shared_sfml', 'Use system shared libsfml-network', False),
    PathVariable('userdir', 'Set the name of the user data directory in home', '.dolphin-emu', PathVariable.PathAccept),
    EnumVariable('install', 'Choose a local or global installation', 'local',
                 allowed_values = ('local', 'global'),
                 ignorecase = 2
                 ),
    PathVariable('prefix', 'Installation prefix (only used for a global build)', '/usr', PathVariable.PathAccept),
    PathVariable('destdir', 'Temporary install location (for package building)', None, PathVariable.PathAccept),
    EnumVariable('flavor', 'Choose a build flavor', 'release',
                 allowed_values = ('release', 'devel', 'debug', 'fastlog', 'prof'),
                 ignorecase = 2
                 ),
    EnumVariable('osx', 'Choose a backend (WIP)', '32cocoa',
                 allowed_values = ('32x11', '32cocoa', '64cocoa', 'universal'),
                 ignorecase = 2
                 ),
    PathVariable('wxconfig', 'Path to the wxconfig', None),
    EnumVariable('pgo', 'Profile-Guided Optimization (generate or use)', 'none',
                allowed_values = ('none', 'generate', 'use'),
                ignorecase = 2
                ),
    ('CC', 'The c compiler', 'gcc'),
    ('CXX', 'The c++ compiler', 'g++'),
    )

if sys.platform == 'win32':
    env = Environment(
        CPPPATH = include_paths,
        RPATH = [],
        LIBS = [],
        LIBPATH = [],
        tools = [ 'mingw' ],
        variables = vars,
        ENV = os.environ,
        BUILDERS = builders,
        DESCRIPTION = description,
        SUMMARY = description,
        LICENSE = license,
        NAME = name,
        VERSION = version,
        )
else:
    env = Environment(
        CPPPATH = include_paths,
        RPATH = [],
        LIBS = [],
        LIBPATH = [],
        variables = vars,
        ENV = {
            'PATH' : os.environ['PATH'],
            'HOME' : os.environ['HOME']
        },
        BUILDERS = builders,
        DESCRIPTION = description,
        SUMMARY = description,
        LICENSE = license,
        NAME = name,
        VERSION = version,
        )

# save the given command line options
vars.Save('args.cache', env)

# verbose compile
if not env['verbose']:
    env['CCCOMSTR'] = "Compiling $TARGET"
    env['CXXCOMSTR'] = "Compiling $TARGET"
    env['ARCOMSTR'] = "Archiving $TARGET"
    env['LINKCOMSTR'] = "Linking $TARGET"
    env['ASCOMSTR'] = "Assembling $TARGET"
    env['ASPPCOMSTR'] = "Assembling $TARGET"
    env['SHCCCOMSTR'] = "Compiling shared $TARGET"
    env['SHCXXCOMSTR'] = "Compiling shared $TARGET"
    env['SHLINKCOMSTR'] = "Linking shared $TARGET"
    env['RANLIBCOMSTR'] = "Indexing $TARGET"

# build flavor
flavour = env['flavor']
if (flavour == 'debug'):
    compileFlags.append('-ggdb')
    cppDefines.append('_DEBUG') #enables LOGGING
    # FIXME: this disable wx debugging how do we make it work?
    cppDefines.append('NDEBUG') 
elif (flavour == 'devel'):
    compileFlags.append('-ggdb')
elif (flavour == 'fastlog'):
    compileFlags.append('-O3')
    cppDefines.append('DEBUGFAST')
elif (flavour == 'prof'):
    compileFlags.append('-O3')
    compileFlags.append('-ggdb')
elif (flavour == 'release'):
    compileFlags.append('-O3')
    compileFlags.append('-fomit-frame-pointer');
# more warnings
if env['lint']:
    warnings.append('error')
    warnings.append('unreachable-code')
    warnings.append('float-equal')

# add the warnings to the compile flags
compileFlags += [ ('-W' + warning) for warning in warnings ]

env['CCFLAGS'] = compileFlags
if sys.platform == 'win32':
    env['CXXFLAGS'] = compileFlags
else:
    env['CXXFLAGS'] = compileFlags + [ '-fvisibility-inlines-hidden' ]
env['CPPDEFINES'] = cppDefines

# pgo - Profile Guided Optimization
if env['pgo']=='generate':
    compileFlags.append('-fprofile-generate')
    env['LINKFLAGS']='-fprofile-generate'
if env['pgo']=='use':
    compileFlags.append('-fprofile-use')
    env['LINKFLAGS']='-fprofile-use'


# Configuration tests section
tests = {'CheckWXConfig' : wxconfig.CheckWXConfig,
         'CheckPKGConfig' : utils.CheckPKGConfig,
         'CheckPKG' : utils.CheckPKG,
         'CheckSDL' : utils.CheckSDL,
         'CheckFink' : utils.CheckFink,
         'CheckMacports' : utils.CheckMacports,
         'CheckPortaudio' : utils.CheckPortaudio,
         }

#object files
env['build_dir'] = os.path.join(basedir, 'Build', platform.system() + '-' + platform.machine() + '-' + env['flavor'] + os.sep)


VariantDir(env['build_dir'], '.', duplicate=0)

# Where do we run from
env['base_dir'] = os.getcwd()+ '/'

# install paths
extra=''
if flavour == 'debug':
    extra = '-debug'
elif flavour == 'prof':
    extra = '-prof'

# Set up the install locations
if (env['install'] == 'global'):
    env['prefix'] = os.path.join(env['prefix'] + os.sep)
    env['binary_dir'] = env['prefix'] + 'bin/'
    env['libs_dir'] = env['prefix'] + 'lib/'
    env['plugin_dir'] = env['prefix'] + 'lib/dolphin-emu/' 
    env['data_dir'] = env['prefix'] + "share/dolphin-emu/"
else:
    env['prefix'] = os.path.join(env['base_dir'] + 'Binary', platform.system() + '-' + platform.machine() + extra + os.sep)
    env['binary_dir'] = env['prefix']
    if sys.platform == 'darwin':
        env['plugin_dir'] = env['prefix'] + 'Dolphin.app/Contents/PlugIns/'
        env['data_dir'] = env['prefix'] + 'Dolphin.app/Contents/'
        env['libs_dir'] = env['prefix'] + 'Libs/'
    else:
        env['plugin_dir'] = env['prefix'] + 'plugins/' 
        env['data_dir'] = env['prefix']
        env['libs_dir'] = env['prefix'] + 'lib/'

env['RPATH'].append(env['libs_dir'])

# static libs goes here
env['local_libs'] =  env['build_dir'] + os.sep + 'libs' + os.sep

env['LIBPATH'].append(env['local_libs']) 
env['LIBPATH'].append(env['libs_dir']) 

conf = env.Configure(custom_tests = tests, 
                     config_h="Source/Core/Common/Src/Config.h")

if not conf.CheckPKGConfig('0.15.0'):
    print "Can't find pkg-config, some tests will fail"

# find ports/fink for library and include path
if sys.platform == 'darwin':
    #ports usually has newer versions
    conf.CheckMacports()
    conf.CheckFink()

env['HAVE_SDL'] = conf.CheckSDL('1.0.0')

# Bluetooth for wii support
env['HAVE_BLUEZ'] = conf.CheckPKG('bluez')

# needed for sound
env['HAVE_AO'] = 0
if not env['noao']:
    env['HAVE_AO'] = conf.CheckPKG('ao')

env['HAVE_OPENAL'] = 0
if env['openal']:
    env['HAVE_OPENAL'] = conf.CheckPKG('openal')

env['HAVE_ALSA'] = conf.CheckPKG('alsa')

env['HAVE_PULSEAUDIO'] = 0
#env['HAVE_PULSEAUDIO'] = conf.CheckPKG('libpulse')

# OpenCL
env['HAVE_OPENCL'] = 0
if env['opencl']:   
    env['HAVE_OPENCL'] = conf.CheckPKG('OpenCL')

env['HAVE_PORTAUDIO'] =  0
if sys.platform != 'darwin':
# needed for mic
    env['HAVE_PORTAUDIO'] =  conf.CheckPortaudio(1890)

# SOIL
env['SHARED_SOIL'] = 0;
if env['shared_soil']:
    env['SHARED_SOIL'] = conf.CheckPKG('SOIL')
    if not env['SHARED_SOIL']:
        print "shared SOIL library not detected"
        print "falling back to the static library"
if not env['SHARED_SOIL']:
    env['CPPPATH'] += [ basedir + 'Externals/SOIL' ]
    dirs += ['Externals/SOIL']

# LZO
env['SHARED_LZO'] = 0;
if env['shared_lzo']:
    env['SHARED_LZO'] = conf.CheckPKG('lzo2')
    if not env['SHARED_LZO']:
        print "shared LZO library not detected"
        print "falling back to the static library"
if not env['SHARED_LZO']:
    env['CPPPATH'] += [ basedir + 'Externals/LZO' ]
    dirs += ['Externals/LZO']

# SFML
env['SHARED_SFML'] = 0;
if env['shared_sfml']:
    # TODO:  Check the version of sfml.  It should be at least version 1.5
    env['SHARED_SFML'] = conf.CheckPKG('sfml-network') and conf.CheckCXXHeader("SFML/Network/Ftp.hpp")
    if not env['SHARED_SFML']:
        print "shared sfml-network library not detected"
        print "falling back to the static library"
if not env['SHARED_SFML']:
    env['CPPPATH'] += [ basedir + 'Externals/SFML/include' ]
    dirs += ['Externals/SFML/src']

#osx 64 specifics
if sys.platform == 'darwin':
    if env['osx'] == '64cocoa':
        compileFlags += ['-arch' , 'x86_64', '-m64' ]
        env['LINKFLAGS'] += ['-arch' , 'x86_64', '-m64' ]
        conf.Define('MAP_32BIT', 0)
    if env['osx'] == '32cocoa':
        compileFlags += ['-arch' , 'i386', '-m32' ]
        env['LINKFLAGS'] += ['-arch' , 'i386', '-m32' ]
    if env['osx'] == 'universal':
        compileFlags += ['-arch i386', '-arch x86_64']
        env['LINKFLAGS'] += ['-arch i386', '-arch x86_64'] 
    if not env['osx'] == '32x11':
        env['HAVE_X11'] = 0
        env['HAVE_COCOA'] = 1

else:
    env['HAVE_X11'] = conf.CheckPKG('x11')
    env['HAVE_COCOA'] = 0
   
# handling wx flags CCFLAGS should be created before
wxmods = ['aui', 'adv', 'core', 'base']

env['USE_WX'] = 0
if env['wxgl']:
    wxmods.append('gl')
    env['USE_WX'] = 1

if sys.platform == 'win32':
    env['HAVE_WX'] = 1
    env['USE_WX'] = 1

# Gui less build
if env['nowx']:
    env['HAVE_WX'] = 0;
else:
    env['HAVE_WX'] = conf.CheckWXConfig('2.8', wxmods, 0) 

# check for libgtk2.0
env['HAVE_GTK2'] = 0
if sys.platform == 'linux2':
    env['HAVE_GTK2'] = env['HAVE_WX'] and conf.CheckPKG('gtk+-2.0')
    if env['HAVE_WX'] and not env['HAVE_GTK2']:
        print "gtk+-2.0 developement headers not detected"
        print "gtk+-2.0 is required to build the WX GUI"
        Exit(1)

env['NOJIT'] = 0
if env['nojit']:
	env['NOJIT'] = 1

conf.Define('NOJIT', env['NOJIT'])

# Creating config.h defines
conf.Define('HAVE_SDL', env['HAVE_SDL'])
conf.Define('HAVE_BLUEZ', env['HAVE_BLUEZ'])
conf.Define('HAVE_AO', env['HAVE_AO'])
conf.Define('HAVE_OPENCL', env['HAVE_OPENCL'])
conf.Define('HAVE_OPENAL', env['HAVE_OPENAL'])
conf.Define('HAVE_ALSA', env['HAVE_ALSA'])
conf.Define('HAVE_WX', env['HAVE_WX'])
conf.Define('USE_WX', env['USE_WX'])
conf.Define('HAVE_X11', env['HAVE_X11'])
conf.Define('HAVE_COCOA', env['HAVE_COCOA'])
conf.Define('HAVE_PORTAUDIO', env['HAVE_PORTAUDIO'])
conf.Define('SHARED_SOIL', env['SHARED_SOIL'])
conf.Define('SHARED_LZO', env['SHARED_LZO'])
conf.Define('SHARED_SFML', env['SHARED_SFML'])
conf.Define('USER_DIR', "\"" + env['userdir'] + "\"")
if (env['install'] == 'global'):
    conf.Define('DATA_DIR', "\"" + env['data_dir'] + "\"")
    conf.Define('LIBS_DIR', "\"" + env['libs_dir'] + "\"")

# lua
env['LUA_USE_MACOSX'] = 0
env['LUA_USE_LINUX'] = 0
env['LUA_USE_POSIX'] = 0
if sys.platform == 'darwin':
	env['LUA_USE_MACOSX'] = 1
elif sys.platform == 'linux2':
	env['LUA_USE_LINUX'] = 1

conf.Define('LUA_USE_MACOSX', env['LUA_USE_MACOSX'])
conf.Define('LUA_USE_LINUX', env['LUA_USE_LINUX'])

# profile
env['USE_OPROFILE'] = 0
if (flavour == 'prof'): 
    proflibs = [ '/usr/lib/oprofile', '/usr/local/lib/oprofile' ]
    env['LIBPATH'].append(proflibs)
    env['RPATH'].append(proflibs)
    if conf.CheckPKG('opagent'):
        env['USE_OPROFILE'] = 1
    else:
        print "Can't build prof without oprofile, disabling"

conf.Define('USE_OPROFILE', env['USE_OPROFILE'])
# After all configuration tests are done
conf.Finish()

#wx windows flags
if env['HAVE_WX']:
    wxconfig.ParseWXConfig(env)
    #this smells like a hack, but i dont know any other way to fix this
    #right now. ParseWXConfig calls wx-config --libs, which returns
    #"-arch i386" on my box (and probably also tmator's).
    #SCons.ParseConfig makes this a tuple, which is
    # 1) a problem for utils.filterWarnings
    # 2) a duplicate (and conflicting) set of arch specifiers
    #this mainly affects MacOSX, since darwin builds explicitly get
    #those set around line 280.
    if sys.platform == 'darwin':
	env['CCFLAGS'] = [ 
	    f 
	    for f in filter(lambda x:isinstance(x, basestring), env['CCFLAGS'])
	    ]
else:
    print "WX not found or disabled, not building GUI"

# add methods from utils to env
env.AddMethod(utils.filterWarnings)

rev = utils.GenerateRevFile(env['flavor'], 
                            "Source/Core/Common/Src/svnrev_template.h",
                            "Source/Core/Common/Src/svnrev.h")
# print a nice progress indication when not compiling
Progress(['-\r', '\\\r', '|\r', '/\r'], interval = 5)

# Setup destdir for package building
# Warning:  The program will not run from this location.  It is assumed the
# package will later install it to the prefix as it was defined before this.
if env.has_key('destdir'):
    env['prefix'] = env['destdir'] + env['prefix']
    env['plugin_dir'] = env['destdir'] + env['plugin_dir']
    env['binary_dir'] = env['destdir'] + env['binary_dir']
    env['libs_dir'] = env['destdir'] + env['libs_dir']
    env['data_dir'] = env['destdir'] + env['data_dir']

# die on unknown variables
unknown = vars.UnknownVariables()
if unknown:
    print "Unknown variables:", unknown.keys()
    Exit(1)

# generate help
Help(vars.GenerateHelpText(env))

Export('env')

for subdir in dirs:
    SConscript(
        subdir + os.sep + 'SConscript',
        variant_dir = env[ 'build_dir' ] + subdir + os.sep,
        duplicate=0
        )

# Data install
if sys.platform == 'darwin':
    env.Install(env['data_dir'], 'Data/Sys')
    env.Install(env['data_dir'], 'Data/User')
    env.Install(env['binary_dir'] + 'Dolphin.app/Contents/Resources/', 
                'Source/Core/DolphinWX/resources/Dolphin.icns')
else:
    env.InstallAs(env['data_dir'] + 'sys', 'Data/Sys')
    env.InstallAs(env['data_dir'] + 'user', 'Data/User')

env.Alias('install', env['prefix'])

if env['bundle']:
    # Make tar ball (TODO put inside normal dir)
    tar_env = env.Clone()
    tarball = tar_env.Tar('dolphin-'+rev +'.tar.bz2', env['prefix'])
    tar_env.Append(TARFLAGS='-j', 
                   TARCOMSTR="Creating release tarball")


#TODO clean all bundles
#env.Clean(all, 'dolphin-*'+ '.tar.bz2')


