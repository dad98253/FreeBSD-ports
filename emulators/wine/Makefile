# Created by: Gerald Pfeifer <gerald@FreeBSD.org>
# $FreeBSD$

PORTNAME=	wine
DISTVERSION=	5.0
PORTREVISION=	1
PORTEPOCH=	1
CATEGORIES=	emulators
MASTER_SITES=	SF/${PORTNAME}/Source \
		http://mirrors.ibiblio.org/wine/source/5.0/

MAINTAINER?=	gerald@FreeBSD.org
COMMENT?=	Microsoft Windows compatibility environment

LICENSE=	LGPL21 LGPL3
LICENSE_COMB=	dual
LICENSE_FILE=	${WRKSRC}/LICENSE

ONLY_FOR_ARCHS=	amd64 i386

BUILD_DEPENDS=	${LOCALBASE}/bin/flex:textproc/flex
LIB_DEPENDS=	libFAudio.so:audio/faudio \
		libxml2.so:textproc/libxml2

USES=		bison desktop-file-utils gmake localbase pkgconfig shebangfix tar:xz

CONFLICTS_INSTALL=	wine-staging-[0-9]* wine-devel-[0-9]* i386-wine-[0-9]* \
			i386-wine-staging-[0-9]* i386-wine-devel-[0-9]*

GNU_CONFIGURE=	yes
CONFIGURE_ARGS+=--verbose \
		--disable-kerberos \
		--disable-tests \
		--without-alsa --without-capi --without-dbus \
		--without-gettext --without-gettextpo \
		--without-gphoto --without-gsm --without-gstreamer \
		--without-inotify \
		--without-krb5 \
		--without-opencl \
		--without-osmesa \
		--with-oss \
		--without-pulse \
		--without-sane \
		--without-sdl \
		--without-tiff \
		--without-udev \
		--without-unwind 
CONFIGURE_ENV=	CPPBIN="${CPP}" FLEX="${LOCALBASE}/bin/flex"
WINELIBDIR?=	${PREFIX}/lib
.if !defined(USE_LDCONFIG32)
USE_LDCONFIG=	${WINELIBDIR} ${WINELIBDIR}/wine
.endif
SHEBANG_FILES=	tools/make_requests tools/winemaker/winemaker \
		tools/winedump/function_grep.pl

SUB_FILES=	pkg-message

PORTDATA=	l_intl.nls wine.inf

OPTIONS_DEFINE=	CUPS DOCS DOSBOX GCC GNUTLS HAL LDAP LIBXSLT MPG123 OPENAL V4L VKD3D VULKAN WINEMAKER X11 GECKO MONO
OPTIONS_DEFAULT=GCC GNUTLS X11
OPTIONS_SUB=	yes

DOSBOX_DESC=	Use DOSBox to run MS-DOS programs
LIBXSLT_DESC=	Use libxslt (only used by msxml3.dll)
VKD3D_DESC=	Direct3D 12 support via Vulkan
VULKAN_DESC=	Vulkan API support
WINEMAKER_DESC=	Fully support winemaker (requires Perl)
GECKO_DESC=	Bundle Gecko MSI package for Wine
MONO_DESC=	Bundle Mono MSI package for Wine

PORTSCOUT=	limit:^5\.0

CUPS_CONFIGURE_WITH=	cups
CUPS_LIB_DEPENDS=	libcups.so:print/cups

DOSBOX_RUN_DEPENDS=	dosbox:emulators/dosbox

GCC_USE=		gcc=yes
GCC_USES_OFF=		compiler:c11

GECKO_RUN_DEPENDS=	${DATADIR}/gecko/wine-gecko-2.47.1-x86.msi:emulators/wine-gecko

GNUTLS_CONFIGURE_WITH=	gnutls
GNUTLS_LIB_DEPENDS=	libgnutls.so:security/gnutls

HAL_CONFIGURE_WITH=	hal
HAL_LIB_DEPENDS=	libhal.so:sysutils/hal

LDAP_CONFIGURE_WITH=	ldap
LDAP_USE=		OPENLDAP=yes

LIBXSLT_CONFIGURE_WITH=	xslt
LIBXSLT_LIB_DEPENDS=	libxslt.so:textproc/libxslt

MONO_RUN_DEPENDS=	${DATADIR}/mono/wine-mono-4.9.4.msi:emulators/wine-mono

MPG123_CONFIGURE_WITH=	mpg123
MPG123_LIB_DEPENDS=	libmpg123.so:audio/mpg123

OPENAL_CONFIGURE_WITH=	openal
OPENAL_USES=		openal

V4L_CONFIGURE_WITH=	v4l2
V4L_BUILD_DEPENDS=	${LOCALBASE}/include/linux/videodev2.h:multimedia/v4l_compat
V4L_LIB_DEPENDS=	libv4l2.so:multimedia/libv4l

VKD3D_CONFIGURE_WITH=	vkd3d
VKD3D_LIB_DEPENDS=	libvkd3d.so:graphics/vkd3d

VULKAN_CONFIGURE_WITH=	vulkan
VULKAN_LIB_DEPENDS=	libvulkan.so:graphics/vulkan-loader

WINEMAKER_RUN_DEPENDS=	p5-XML-LibXML>0:textproc/p5-XML-LibXML

X11_CONFIGURE_WITH=	x cms fontconfig freetype glu jpeg opengl png xinerama xinput2 xrandr xrender
X11_USES=		gl jpeg xorg
X11_USE=		GL=glu XORG=x11,xext,xcomposite,xcursor,xi,xinerama,xrandr,xrender
X11_LIB_DEPENDS=	libfontconfig.so:x11-fonts/fontconfig \
			libfreetype.so:print/freetype2 \
			liblcms2.so:graphics/lcms2 \
			libpng.so:graphics/png

# Include this only if it has not been already included by the
# i386-wine slave port.
.ifndef PKGNAMEPREFIX
.include <bsd.port.pre.mk>
.endif

.if ${ARCH} == amd64
# Wine is composed of three parts:
#  - wine (aka this port) is the 32-bit component and runs 32-bit programs (on FreeBSD/i386)
#  - wine64 (aka this part of this port) is the 64-bit component and runs 64-bit programs
#    (on FreeBSD/amd64)
#  - wow64 (a subset of emulators/i386-wine-devel) is a part of the 64-bit component that runs
#    32-bit programs (on FreeBSD/amd64)
# We also currently have:
#  - "wine32" (aka emulators/i386-wine-devel) is the 32-bit component and runs 32-bit programs
#    (on FreeBSD/amd64).  This will be superseded by wow64.

# --libdir is required since Wine defaults to "${PREFIX}/lib64" on amd64.
CONFIGURE_ARGS+=	--enable-win64 --libdir=${PREFIX}/lib
PLIST_SUB+=		WINE32="@comment " WINE64=""

# Wine assumes a WoW64 package is available, however WoW64 is not yet
# available on FreeBSD. Also fix the desktop file.
post-patch:
	${REINPLACE_CMD} 's|/wine|/wine64|g' ${WRKSRC}/tools/wineapploader.in
	${REINPLACE_CMD} '/Exec/s|wine|wine64|g' ${WRKSRC}/loader/wine.desktop

.else
PLIST_SUB+=		WINE32="" WINE64="@comment "
.endif

pre-build:
	cd ${WRKSRC} && ${MAKE_CMD} depend

post-install:
	@${MV} -f ${STAGEDIR}${WINELIBDIR}/libwine.so.1.0 \
	          ${STAGEDIR}${WINELIBDIR}/libwine.so.1
	@${LN} -sf libwine.so.1 ${STAGEDIR}/${WINELIBDIR}/libwine.so
	@${MKDIR} ${STAGEDIR}${DOCSDIR}
.for i in README ANNOUNCE AUTHORS
	${INSTALL_DATA} ${WRKSRC}/${i} ${STAGEDIR}${DOCSDIR}
.endfor
	${INSTALL_DATA} ${WRKSRC}/programs/winedbg/README ${STAGEDIR}${DOCSDIR}/README.winedbg
	${INSTALL_DATA} ${WRKSRC}/tools/winedump/README ${STAGEDIR}${DOCSDIR}/README.winedump

.ifndef PKGNAMEPREFIX
.include <bsd.port.post.mk>
.endif
