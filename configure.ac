#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

AC_PREREQ([2.60])

AC_INIT(ftk, 1.0, xianjimli@hotmail.com)
AC_CONFIG_SRCDIR([src/ftk_canvas.h])
AC_CONFIG_HEADERS([config_ftk.h])
AM_INIT_AUTOMAKE(ftk, 1.0)
AC_CONFIG_MACRO_DIR([m4])

# Checks for programs.
AC_PROG_CC
AC_PROG_CXX
AC_PROG_LIBTOOL
AM_PROG_CC_C_O
AC_C_BIGENDIAN
PKG_PROG_PKG_CONFIG

# Checks for libraries.
# FIXME: Replace `main' with a function in `-ljpeg':
AC_CHECK_LIB([jpeg], [main])

dnl Test for libpng

PNG=no
PKG_CHECK_MODULES(PNG, libpng, PNG="yes", [PNG="no"
  AC_MSG_WARN([*** libpng not found.])])

if test "$PNG" = "yes"; then
	AC_SUBST(PNG_CFLAGS)
	AC_SUBST(PNG_LIBS)

	CFLAGS="$CFLAGS $PNG_CFLAGS"
	LDFLAGS="$LDFLAGS $PNG_LIBS"
fi

# Checks for header files.
AC_CHECK_HEADERS([fcntl.h stdlib.h string.h sys/ioctl.h sys/time.h unistd.h execinfo.h])

AC_ARG_WITH(canvas, [AC_HELP_STRING([--with-canvas=default|skia|cairo|directfb],
                    [use which canvas])])
CFLAGS="$CFLAGS -DHAVE_CONFIG_FTK_H"
case "$with_canvas" in
	skia*) 
		canvas="skia"
		SKIA_ROOT="/work/skia/"
		CANVAS_CFLAGS="-I$SKIA_ROOT/include/core -I$SKIA_ROOT/include/config -I$SKIA_ROOT/include/images -I$SKIA_ROOT/include"
		SKIA_LIBS_ROOT="$SKIA_ROOT/out/Debug/obj.target/gyp/"
		CANVAS_LDFLAGS="-lstdc++ -lm -lfreetype -lpthread $SKIA_LIBS_ROOT/libcore.a $SKIA_LIBS_ROOT/libopts.a $SKIA_LIBS_ROOT/libimages.a $SKIA_LIBS_ROOT/libzlib.a $SKIA_LIBS_ROOT/libgr.a $SKIA_LIBS_ROOT/libutils.a";;
	directfb*)
		canvas="directfb";;
	cairo*)
		canvas="cairo";;
	dummy*)
		canvas="dummy";;
	*)
		canvas="default";;
esac

AC_ARG_WITH(backend, [AC_HELP_STRING([--with-backend=linux-x11:WxH|linux-fb|linux-vlcd|linux-dfb|linux-st7781|linux-sigma|psp],[use which display/input backend])])

CFLAGS="$CFLAGS -DHAVE_CONFIG_FTK_H"
case "$with_backend" in
	linux-x11*) 
		backend="x11"
		CFLAGS="$CFLAGS -I/usr/X11R6/include -DUSE_LINUX_X11 -DDISPLAY_PARAM=\"\\\"$with_backend\\\"\"";
		LDFLAGS="$LDFLAGS -L/usr/X11R6/lib -lX11 -lXext";;
	linux-vlcd*)
		backend="fb"
		CFLAGS="$CFLAGS -DUSE_LINUX_FB -DUSE_LINUX_NATIVE -DPC_EMU";;
	linux-dfb*)
		backend="dfb"
		canvas="directfb"
		DFB_VERSION=1.2.0
		PKG_CHECK_MODULES(DIRECTFB, [directfb >= $DFB_VERSION])

		CFLAGS="$CFLAGS -DUSE_LINUX_DFB $DIRECTFB_CFLAGS";
		LDFLAGS="$LDFLAGS $DIRECTFB_LIBS";;
	linux-sigma*)
		backend="sigma"
		CFLAGS="$CFLAGS $SIGMA_CFLAGS";
		LDFLAGS="$LDFLAGS $SIGMA_OBJS $SIGMA_LIBS";;
	psp*)
		backend="psp"
		CFLAGS="$CFLAGS $PSP_CFLAGS";
		LDFLAGS="$LDFLAGS $PSP_OBJS $PSP_LIBS $PSP_LDFLAGS";;
	linux-st7781)
		backend="st7781"
		CFLAGS="$CFLAGS -DUSE_LINUX_ST7781 -DUSE_LINUX_NATIVE ";;
	*)
		backend="fb"
		CFLAGS="$CFLAGS -DUSE_LINUX_FB -DUSE_LINUX_NATIVE ";;
esac

AM_CONDITIONAL(FTK_BACKEND_X11, test "$backend" = "x11")
AM_CONDITIONAL(FTK_BACKEND_FB, test "$backend" = "fb")
AM_CONDITIONAL(FTK_BACKEND_ST7781, test "$backend" = "st7781")
AM_CONDITIONAL(FTK_BACKEND_DFB, test "$backend" = "dfb")
AM_CONDITIONAL(FTK_BACKEND_SIGMA, test "$backend" = "sigma")
AM_CONDITIONAL(FTK_BACKEND_PSP, test "$backend" = "psp")

AC_SUBST(CANVAS_LDFLAGS)
AC_SUBST(CANVAS_CFLAGS)

if test "$with_canvas" = "" 
then
	with_canvas="default"
fi
AM_CONDITIONAL(FTK_CANVAS_DUMMY, test "$canvas" = "dummy")
AM_CONDITIONAL(FTK_CANVAS_DEFAULT, test "$canvas" = "default")
AM_CONDITIONAL(FTK_CANVAS_SKIA, test "$canvas" = "skia")
AM_CONDITIONAL(FTK_CANVAS_DIRECTFB, test "$canvas" = "directfb")
AM_CONDITIONAL(FTK_CANVAS_CAIRO, test "$canvas" = "cairo")

AC_ARG_WITH(fontengine, [AC_HELP_STRING([--with-fontengine=default|freetype],
                    [use which font engine])])

case "$with_fontengine" in
	freetype) 
		dnl Test for freetype2
		FREETYPE=no
		PKG_CHECK_MODULES(FREETYPE, freetype2, FREETYPE="yes", [FREETYPE="no"
		  AC_MSG_WARN([*** freetype2 not found.])])

		if test "$FREETYPE" = "yes"; then
		  AC_SUBST(FREETYPE_CFLAGS)
		  AC_SUBST(FREETYPE_LIBS)
		  CFLAGS="$CFLAGS $FREETYPE_CFLAGS -DUSE_FREETYPE"
		  LDFLAGS="$LDFLAGS $FREETYPE_LIBS"
		fi		
		font_engine="freetype";;
	*)
		CFLAGS="$CFLAGS -DUSE_DEFAULT_FONT"
		font_engine="default";;
esac

AM_CONDITIONAL(FONT_ENGINE_FREETYPE, test "$font_engine" = "freetype")
AM_CONDITIONAL(FONT_ENGINE_DEFAULT,  test "$font_engine" = "default")

AC_ARG_WITH(inputmethod, [AC_HELP_STRING([--with-inputmethod=py_wb_hw|gpinyin],
                    [use which input method])])
case "$with_inputmethod" in
	py_wb_hw) 
		input_method="py_wb_hw";;
	*)
		CFLAGS="$CFLAGS  -DUSE_GPINYIN"
		input_method="gpinyin";;
esac

AM_CONDITIONAL(ENABLE_GPINYIN, test "$input_method" = "gpinyin")

AC_ARG_ENABLE(tslib,
  [  --enable-tslib            build with tslib support [[default=no]]],,
  enable_tslib=no)

if test "$enable_tslib" = "yes"; then
  dnl Check for tslib
  PKG_CHECK_MODULES(TSLIB, tslib, TSLIB="yes", [TSLIB="no"
    AC_MSG_WARN([*** tslib not found.])])

  if test "$TSLIB" = "yes"; then
      AC_SUBST(TSLIB_CFLAGS)
      AC_SUBST(TSLIB_LIBS)

      CFLAGS="$CFLAGS $TSLIB_CFLAGS -DUSE_TSLIB"
      LDFLAGS="$LDFLAGS $TSLIB_LIBS"
  fi
fi

AM_CONDITIONAL(ENABLE_TSLIB,  test "$enable_tslib" = "yes")

AC_ARG_ENABLE(linebreak,
  [  --enable-linebreak            build with linebreak support [[default=yes]]],,
  enable_linebreak=yes)

if test "$enable_linebreak" = "yes"; then
  CFLAGS="$CFLAGS -DUSE_LINEBREAK "
fi

AM_CONDITIONAL(ENABLE_LINEBREAK,  test "$enable_linebreak" = "yes")

AC_ARG_ENABLE(cairo,
  [  --enable-cairo            build with cairo support [[default=no]]],,
  enable_cairo=no)

if test "$enable_cairo" = "yes"; then
  dnl Check for cairo
  PKG_CHECK_MODULES(CAIRO, cairo, CAIRO="yes", [CAIRO="no"
    AC_MSG_WARN([*** cairo not found.])])

  if test "$CAIRO" = "yes"; then
    AC_SUBST(CAIRO_CFLAGS)
    AC_SUBST(CAIRO_LIBS)

    CFLAGS="$CFLAGS $CAIRO_CFLAGS -DUSE_CAIRO"
    LDFLAGS="$LDFLAGS $CAIRO_LIBS"

    dnl Test for pixman
    PIXMAN=no
    PKG_CHECK_MODULES(PIXMAN, pixman-1, PIXMAN="yes", [PIXMAN="no"
      AC_MSG_WARN([*** pixman not found.])])

    if test "$PIXMAN" = "yes"; then
	AC_SUBST(PIXMAN_CFLAGS)
	AC_SUBST(PIXMAN_LIBS)

	CFLAGS="$CFLAGS $PIXMAN_CFLAGS"
	LDFLAGS="$LDFLAGS $PIXMAN_LIBS"
    fi
  fi
fi

AM_CONDITIONAL(ENABLE_CAIRO,  test "$enable_cairo" = "yes")

AC_ARG_ENABLE(fribidi,
  [  --enable-fribidi            build with fribidi support [[default=no]]],,
  enable_fribidi=no)

if test "$enable_fribidi" = "yes"; then
  CFLAGS="$CFLAGS -DUSE_FRIBIDI `pkg-config --cflags fribidi`"
  LDFLAGS="$LDFLAGS `pkg-config --libs fribidi`"
fi

AM_CONDITIONAL(ENABLE_FRIBIDI,  test "$enable_fribidi" = "yes")

AC_ARG_ENABLE(opengles,
  [  --enable-opengles            build with opengles support [[default=no]]],,
  enable_opengles=no)

if test "$enable_opengles" = "yes"; then
  CFLAGS="$CFLAGS -DUSE_OPENGLES"
fi

AM_CONDITIONAL(ENABLE_OPENGLES,  test "$enable_opengles" = "yes")

AC_ARG_ENABLE(vnc,
  [  --enable-vnc            build with vnc support [[default=yes]]],,
  enable_vnc=yes)

if test "$enable_vnc" = "yes"; then
  CFLAGS="$CFLAGS -DUSE_VNC"
fi

AM_CONDITIONAL(ENABLE_VNC,  test "$enable_vnc" = "yes")

AC_ARG_ENABLE(handwrite,
  [  --enable-handwrite            build with handwrite support [[default=no]]],,
  enable_handwrite=no)

if test "$enable_handwrite" = "yes"; then
  CFLAGS="$CFLAGS -DUSE_HANDWRITE"
fi

AM_CONDITIONAL(ENABLE_HANDWRITE,  test "$enable_handwrite" = "yes")

AC_ARG_ENABLE(profile,
  [  --enable-profile build with performance profile support [[default=no]]],,
  enable_profile=no)

if test "$enable_profile" = "yes"; then
  CFLAGS="$CFLAGS -DFTK_PROFILE -DFTK_MEMORY_PROFILE"
fi

if test "$enable_shared" = "yes"; then
  CFLAGS="$CFLAGS -DFTK_PROFILE -DFTK_MEMORY_PROFILE"
fi

# Checks for typedefs, structures, and compiler characteristics.
AC_C_INLINE
AC_TYPE_SIZE_T

# check for gtk-doc
GTK_DOC_CHECK([1.10],[--flavour no-tmpl])

GETTEXT_PACKAGE=ftk
AC_SUBST(GETTEXT_PACKAGE)
AC_DEFINE_UNQUOTED(GETTEXT_PACKAGE,"$GETTEXT_PACKAGE", [Gettext package.])

dnl Add the languages which your application supports here.
ALL_LINGUAS="zh_CN"
AM_GLIB_GNU_GETTEXT

# Checks for library functions.
AC_CONFIG_FILES([ftk.pc
				Makefile
				ftk.cnf
                docs/Makefile
                docs/reference/Makefile
                po/Makefile.in
                theme/Makefile
                theme/default/Makefile
                data/Makefile
                testdata/Makefile
                src/Makefile
                src/im/Makefile
                src/im/default/Makefile
                src/im/gpinyin/Makefile
                src/demos/Makefile
                src/test/Makefile
                src/backend/Makefile
                src/backend/directfb/Makefile
                src/backend/native/Makefile
                src/backend/ucosii/Makefile
                src/backend/sigma/Makefile
                src/backend/iphone/Makefile
                src/backend/psp/Makefile
                src/backend/x11-emu/Makefile
                src/backend/win32/Makefile
                src/os/Makefile
                src/os/linux/Makefile
                src/os/iphone/Makefile
                src/os/windows/Makefile
                src/os/psp/Makefile
                src/os/ucos-ii/Makefile
                src/compiler/Makefile
                script_binding/Makefile
                script_binding/lua/Makefile
                script_binding/lua/lua/Makefile
                apps/Makefile
                apps/common/Makefile
                apps/launcher/Makefile
                apps/desktop/Makefile
                apps/desktop/icons/Makefile
                apps/desktop/xul/Makefile
                apps/desktop/libvncserver/Makefile
                apps/calculator/Makefile
                apps/calculator/icons/Makefile
                apps/calculator/data/Makefile
                apps/filemanager/Makefile
                apps/filemanager/icons/Makefile
                apps/filemanager/data/Makefile
                apps/shell/Makefile
                apps/shell/icons/Makefile
                apps/shell/data/Makefile
                apps/gallery/Makefile
                apps/gallery/icons/Makefile
                apps/gallery/lua/Makefile
                apps/gallery/data/Makefile
                tools/Makefile
                tools/cross-build/Makefile
                tools/fontextract/Makefile
                tools/imconvert/Makefile
                tools/luagen/Makefile
                tools/luagen/idl/Makefile
                tools/fbusgen/Makefile
                fbus/Makefile
                fbus/demo/Makefile
                fbus/demo/echo.service
                services/Makefile
                services/fconf/Makefile
                services/fconf/fconf.service
		opengles/ug/Makefile
		opengles/include/Makefile
		opengles/include/GLES/Makefile
		opengles/src/Makefile
		opengles/Makefile
		opengles/opengles.pc
		opengles/redbook/Makefile
                ])
AC_OUTPUT
echo ================================================
echo with_backend=$with_backend
echo with_canvas=$with_canvas
echo enable_cairo=$enable_cairo
echo enable_opengles=$enable_opengles
echo enable_vnc=$enable_vnc
echo enable_profile=$enable_profile
echo enable_handwrite=$handwrite
echo enable_linebreak=$enable_linebreak
echo ================================================
echo CANVAS_CFLAGS=$CANVAS_CFLAGS
echo CANVAS_LDFLAGS=$CANVAS_LDFLAGS
echo ================================================


