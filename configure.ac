#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.
# AC_PREREQ(2.61)
#
# Change the version number below after doing a public release.
# The version in git should reflect the *next* version planned.
# Version must be MAJOR.MINOR.PATCH otherwise things will break.
#
AC_INIT([jzmq],[2.1.0],[zeromq-dev@lists.zeromq.org])

AC_CONFIG_AUX_DIR(config)
AC_CONFIG_MACRO_DIR(config)
AM_CONFIG_HEADER(src/config.hpp)
AM_INIT_AUTOMAKE(tar-ustar)

#
# Libtool -version-info (ABI version)
#
# Currently 0.0.0 ("unstable"). Don't change this unless you 
# know exactly what you're doing and have read and understand
# http://www.gnu.org/software/libtool/manual/html_node/Updating-version-info.html
#
# libjzmq -version-info
JLTVER="0:0:0"
AC_SUBST(JLTVER)
SPEC_VERSION="0.0.0"
AC_SUBST(SPEC_VERSION)

# Checks for programs.
AC_LIBTOOL_DLOPEN
AC_PROG_LIBTOOL
# AC_PROG_SED
AC_PROG_AWK
AM_PROG_CC_C_O
AC_PROG_CXX
AC_LANG(C++)

# Set default CPPFLAGS for reentrant code
CPPFLAGS="-D_REENTRANT -D_THREAD_SAFE $CPPFLAGS"

# Check for zeromq library
zeromq_prefix=detect
AC_ARG_WITH([zeromq], 
    [AS_HELP_STRING([--with-zeromq=PREFIX], 
        [build with ZeroMQ library installed in PREFIX [default=autodetect]])],
    [case "x$withval" in
        xno)
            AC_MSG_ERROR([jzmq requires the ZeroMQ library])
            ;;
        xyes|x)
            ;;
        *)
            CPPFLAGS="$CPPFLAGS -I${withval}/include"
            LDFLAGS="$LDFLAGS -L${withval}/lib"
            zeromq_prefix=${withval}
            ;;
    esac ]
)
AC_ARG_VAR([JAVA_TARGET_VERSION], [Compile byte codes for a specific JVM version.])
AC_ARG_VAR([JAVAC_EXTRA_FLAGS], [Any other flags worth passing directly to the compiler.])

if test "x$zeromq_prefix" = "xdetect"; then
    PKG_CHECK_MODULES(
        [ZeroMQ], [libzmq], [zeromq_prefix=pkgconfig], [zeromq_prefix=])
        if test "x$zeromq_prefix" = "xpkgconfig"; then
            CPPFLAGS="$CPPFLAGS ${ZeroMQ_CFLAGS}"
            LDFLAGS="$LDFLAGS ${ZeroMQ_LIBS}"
        fi
fi
AC_CHECK_HEADER([zmq.h], [], 
    [AC_MSG_ERROR([cannot find zmq.h])])
AC_CHECK_LIB([zmq], [zmq_init], [],
    [AC_MSG_ERROR([cannot link with -lzmq])])

# Check for JDK
AX_CHECK_JAVA_HOME

AC_PATH_PROG(JAVAC, javac, [no], [$PATH:$JAVA_HOME/bin])
if test "x$JAVAC" = "xno"; then
    AC_MSG_ERROR([cannot find javac.]);
fi
AC_PATH_PROG(JAVAH, javah, [no], [$PATH:$JAVA_HOME/bin])
if test "x$JAVAH" = "xno"; then
    AC_MSG_ERROR([cannot find javah.]);
fi
AC_PATH_PROG(JAR, jar, [no], [$PATH:$JAVA_HOME/bin])
if test "x$JAR" = "xno"; then
    AC_MSG_ERROR([cannot find jar.]);
fi
AC_PATH_PROG(JAVA, java, [no], [$PATH:$JAVA_HOME/bin])
if test "x$JAVA" = "xno"; then
    AC_MSG_ERROR([cannot find java.]);
fi
if test "x$JAVA_TARGET_VERSION" != "x"; then
    JAVAC_EXTRA_FLAGS="-source $JAVA_TARGET_VERSION -target $JAVA_TARGET_VERSION $JAVAC_EXTRA_FLAGS"
    AC_MSG_NOTICE([Setting JAVAC_EXTRA_FLAGS to $JAVAC_EXTRA_FLAGS])
    AC_SUBST(JAVAC_EXTRA_FLAGS)
else
    AC_MSG_NOTICE([Going with default bytecode version.])
fi

# Check for JNI headers
AX_JNI_INCLUDE_DIR

for JNI_INCLUDE_DIR in $JNI_INCLUDE_DIRS
do
        CPPFLAGS="$CPPFLAGS -I$JNI_INCLUDE_DIR"
done

# Need host-specific path for jni_md.h on some systems, this is braindead
AC_CANONICAL_HOST
case "${host_os}" in
    *solaris*)
        CPPFLAGS="$CPPFLAGS -I${JAVA_HOME}/include/solaris"
    ;;
    *openbsd*)
        CPPFLAGS="$CPPFLAGS -I${JAVA_HOME}/include/openbsd"
    ;;
    *linux*)
        CPPFLAGS="$CPPFLAGS -I${JAVA_HOME}/include/linux"
    ;;
    *darwin*)
        # Darwin does not need an extra -I path
    ;;
    *)
        AC_MSG_ERROR([don't know how to find jni_md.h on this platform])
    ;;
esac

case "${host_os}" in
    *darwin*) AC_SUBST(LIB_EXTENSION,[dylib])
    ;;
    *mingw*) AC_SUBST(LIB_EXTENSION,[dll])
    ;;
    *cygwin*) AC_SUBST(LIB_EXTENSION,[dll])
    ;;
    *) AC_SUBST(LIB_EXTENSION,[so])
    ;;
esac

JAVAROOT=.
AC_SUBST(JAVAROOT)


BUILD_VERSION=`git rev-parse HEAD`
BUILD_TIME=`date "+%Y-%m-%d[%H:%M:%S]%Z"`
AC_SUBST(BUILD_VERSION)
AC_SUBST(BUILD_TIME)

AC_OUTPUT(Makefile src/Makefile perf/Makefile)

#  On Linux patch libtool to delete hardcoded paths (rpath).
case "${host_os}" in
    *linux*)
        sed < libtool > libtool-2 \
        's/^hardcode_libdir_flag_spec.*$'/'hardcode_libdir_flag_spec=" "/'
        mv libtool-2 libtool
        chmod 755 libtool
        ;;
esac
