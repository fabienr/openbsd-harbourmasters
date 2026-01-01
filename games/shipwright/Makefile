COMMENT =	game PC port of The Legend of Zelda: Ocarina of Time

V =		9.1.1
PKGNAME=	shipwright-${V}
DIST_TUPLE =	github HarbourMasters Shipwright ${V} .

# install location
INSTALL_PREFIX=	share/shipwright

CATEGORIES =	games emulators

HOMEPAGE =	https://www.shipofharkinian.com/

MAINTAINER =	xxx

# make versions
DR_LIBS_ID=	da35f9d6c7374a95353fd1df1d394d44ab66cf01
GAMECTRLDB_ID=	16ac3e553e23068e26819971f2cc6cd088a7f2f6
LIBULTRASHIP_ID=17a0b7939bd05f5e617cef89457ca43774fc9a9f
OTREXPORTER_ID=	32e088e28c8cdd055d4bb8f3f219d33ad37963f3
ZAPDTR_ID=	684f21a475dcfeee89938ae1f4afc42768a3e7ef
# libultraship
IMGUI_ID=	v1.91.9b-docking
LIBGFXD_ID=	008f73dca8ebc9151b205959b17773a19c5bd0da
PRISM_ID=	bbcbc7e3f890a5806b579361e7aa0336acd547e7
STB_ID=		0bc88af4de5fb022db643c2d8e549a0927749354
STORMLIB_ID=	v9.25
THREADPOOL_ID=	v4.1.0

# Shipwright depends:
# dr_libs, audio decoding libraries for C/C++, each in a single source file, MIT
# SDL_GameControllerDB, mappings to be used with SDL, Zlib licence
# libultraship, reimplementations of libultra (n64 sdk) functions, MIT
# OTRExporter, MIT
# ZAPDTR, Zelda Asset Processor for Decomp, MIT
DIST_TUPLE+=	github mackron dr_libs ${DR_LIBS_ID} dist/dr_libs
DIST_TUPLE+=	github mdqinc SDL_GameControllerDB ${GAMECTRLDB_ID} \
		dist/SDL_GameControllerDB
DIST_TUPLE+=	github Kenix3 libultraship ${LIBULTRASHIP_ID} libultraship
DIST_TUPLE+=	github harbourmasters OTRExporter ${OTREXPORTER_ID} OTRExporter
DIST_TUPLE+=	github harbourmasters ZAPDTR ${ZAPDTR_ID} ZAPDTR

# libultraship depends:
# imgui, Bloat-free Graphical User interface for C++, MIT
# libgfxd, display list decompiler library, MIT
# prism, preprocessor that uses Prism pseudo language, MIT
# stb, single-file public domain libraries(check stb) for C/C++, MIT
# StormLib, library that can work with Blizzard MPQ archives, MIT
# ThreadPool, thread pool library, MIT
DIST_TUPLE+=	github ocornut imgui ${IMGUI_ID} dist/imgui
DIST_TUPLE+=	github glankk libgfxd ${LIBGFXD_ID} dist/libgfxd
DIST_TUPLE+=	github KiritoDv prism-processor ${PRISM_ID} dist/prism
DIST_TUPLE+=	github nothings stb ${STB_ID} dist/stb
DIST_TUPLE+=	github ladislav-zezula StormLib ${STORMLIB_ID} dist/StormLib
DIST_TUPLE+=	github bshoshany thread-pool ${THREADPOOL_ID} dist/ThreadPool

PERMIT_PACKAGE =	licence to clarify

WANTLIB += ${COMPILER_LIBCXX} GL SDL2 SDL2_net bz2 c
WANTLIB += execinfo fmt m ogg opus opusfile png spdlog
WANTLIB += tinyxml2 vorbis vorbisenc vorbisfile z zip

FIX_CRLF_FILES=		ZAPDTR/ZAPD/CMakeLists.txt

# C++20
COMPILER =		base-clang ports-gcc

MODULES =		devel/cmake \
			lang/python

BUILD_DEPENDS =		devel/boost \
			textproc/nlohmann-json
RUN_DEPENDS =		devel/desktop-file-utils
LIB_DEPENDS =		audio/libogg \
			audio/libvorbis \
			audio/opusfile \
			audio/opus \
			archivers/libzip \
			devel/sdl2 \
			devel/sdl2-net \
			devel/spdlog \
			graphics/png \
			textproc/tinyxml2

# upstream default
CONFIGURE_ARGS+=	-DCMAKE_BUILD_TYPE=Release \
			-DBUILD_REMOTE_CONTROL=1 \
# system install
CONFIGURE_ARGS+=	-DNON_PORTABLE=ON \
			-DCMAKE_INSTALL_PREFIX=${LOCALBASE}/${INSTALL_PREFIX}
# OpenBSD specific:
# CMAKE_CXX_COMPILER_CLANG_SCAN_DEPS-NOTFOUND, clang-scan-deps is not in base
# port set CMAKE_CXX_STANDARD 20 but not for modules themselves
# disable cmake scan for modules to build on base-clang
CONFIGURE_ARGS+=	-DCMAKE_CXX_SCAN_FOR_MODULES=OFF

# use pre-fetch depends
CONFIGURE_ARGS+=\
	-DFETCHCONTENT_SOURCE_DIR_DR_LIBS=${WRKSRC}/dist/dr_libs
# libultraship
CONFIGURE_ARGS+=\
	-DFETCHCONTENT_SOURCE_DIR_IMGUI=${WRKSRC}/dist/imgui \
	-DFETCHCONTENT_SOURCE_DIR_LIBGFXD=${WRKSRC}/dist/libgfxd \
	-DFETCHCONTENT_SOURCE_DIR_PRISM=${WRKSRC}/dist/prism \
	-DFETCHCONTENT_SOURCE_DIR_STORMLIB=${WRKSRC}/dist/StormLib \
	-DFETCHCONTENT_SOURCE_DIR_THREADPOOL=${WRKSRC}/dist/ThreadPool

# XXX debug library lookups
# CONFIGURE_ARGS+=	-DCMAKE_FIND_DEBUG_MODE=TRUE
# XXX game crash with MALLOC_OPTIONS G or U, debug stuff
# DEBUG_PACKAGES =	${BUILD_PACKAGES}
# CFLAGS =		-O0 -pipe -g
# CXXFLAGS =		-O0 -pipe -g
# CONFIGURE_ARGS+=	-DCMAKE_BUILD_TYPE=Debug

NO_TEST =		Yes

_PATCHES=${WRKSRC}/libultraship/cmake/dependencies/patches
pre-patch:
# because we force SOURCE_DIR, patches don't get applied
	# apply libultraship patches to imgui & StormLib
	@cd ${WRKSRC}/dist/imgui && \
		patch < ${_PATCHES}/imgui-fixes-and-config.patch
	@cd ${WRKSRC}/dist/StormLib && \
		patch < ${_PATCHES}/stormlib-optimizations.patch

pre-configure:
	# setup libultraship file(DOWNLOAD ...) depends in ${WRKBUILD}/_deps
	# -> nothings/stb
	@mkdir -p ${WRKBUILD}/_deps/stb
	@cp ${WRKSRC}/dist/stb/stb_image.h ${WRKBUILD}/_deps/stb/

post-build:
	# Generate soh.o2r
	@${MODCMAKE_BUILD_TARGET} -t GenerateSohOtr

do-install:
	# game
	${INSTALL_PROGRAM} ${WRKBUILD}/soh/soh ${PREFIX}/bin/ShipwrightSOH
	${INSTALL_DATA_DIR} ${PREFIX}/${INSTALL_PREFIX}
	${INSTALL_DATA} ${WRKBUILD}/soh/soh.o2r ${PREFIX}/${INSTALL_PREFIX}
	${INSTALL_DATA} \
		${WRKSRC}/dist/SDL_GameControllerDB/gamecontrollerdb.txt \
		${PREFIX}/${INSTALL_PREFIX}
	cp -r ${WRKBUILD}/soh/assets ${PREFIX}/${INSTALL_PREFIX}
	# menu
	${INSTALL_DATA_DIR} ${PREFIX}/share/applications
	${INSTALL_DATA} ${FILESDIR}/shipwright.desktop \
		${PREFIX}/share/applications/shipwright.desktop
	${INSTALL_DATA_DIR} ${PREFIX}/share/pixmaps
	${INSTALL_DATA} ${WRKSRC}/soh/macosx/sohIcon.png \
		${PREFIX}/share/pixmaps/shipwright.png

### targets for port maintainer(s)

_TMP=		/tmp/shipwright
_SHIPWRIGHT_API=https://api.github.com/repos/HarbourMasters/Shipwright/contents/
_GAMECTRLDB_API=https://api.github.com/repos/mdqinc/SDL_GameControllerDB/commits
_SHIPWRIGHT_RAW=https://raw.githubusercontent.com/HarbourMasters/Shipwright/
_ULTRASHIP_RAW=	https://raw.githubusercontent.com/Kenix3/libultraship/

versions:
	# make versions
	@mkdir -p ${_TMP} ;						\
									\
	ftp -VMo ${_TMP}/CMakeLists.shipwright				\
	    ${_SHIPWRIGHT_RAW}/${V}/soh/CMakeLists.txt ;		\
	printf "DR_LIBS_ID=\t%s\n" $$(					\
		cat ${_TMP}/CMakeLists.shipwright |			\
		grep -A1 "mackron/dr_libs" |				\
		awk '/GIT_TAG/{print $$2}' ) ;				\
									\
	ftp -VMo ${_TMP}/gamectrldb ${_GAMECTRLDB_API} ;		\
	printf "GAMECTRLDB_ID=\t%s\n" $$(cat ${_TMP}/gamectrldb |	\
		grep -m1 "sha" | awk -F'"' '{print $$4}' ) ;		\
									\
	ftp -VMo ${_TMP}/libultraship					\
	    ${_SHIPWRIGHT_API}/libultraship?ref=${V} ;			\
	_ultraship_id=$$(cat ${_TMP}/libultraship | tr "," "\n" |	\
		grep "html_url" | awk -F'["/]' '{print $$10}' ) ;	\
	printf "LIBULTRASHIP_ID=%s\n" $$_ultraship_id ;			\
									\
	ftp -VMo ${_TMP}/otrexporter					\
		${_SHIPWRIGHT_API}/OTRExporter?ref=${V} ;		\
	printf "OTREXPORTER_ID=\t%s\n" $$(				\
		cat ${_TMP}/otrexporter | tr "," "\n" |			\
		grep "html_url" | awk -F'["/]' '{print $$10}' ) ;	\
									\
	ftp -VMo ${_TMP}/zapdtr						\
		${_SHIPWRIGHT_API}/ZAPDTR?ref=${V} ;			\
	printf "ZAPDTR_ID=\t%s\n" $$(					\
		cat ${_TMP}/zapdtr | tr "," "\n" | 			\
		grep "html_url" | awk -F'["/]' '{print $$10}' ) ;	\
									\
	echo "# libultraship" ;						\
	_ULTRASHIP_RAW="${_ULTRASHIP_RAW}/$${_ultraship_id}" ;		\
	ftp -VMo ${_TMP}/common.cmake					\
	    $${_ULTRASHIP_RAW}/cmake/dependencies/common.cmake ;	\
	printf "IMGUI_ID=\t%s\n" $$(cat ${_TMP}/common.cmake |		\
		grep -A1 "ocornut/imgui" |				\
		awk '/GIT_TAG/{print $$2}' ) ;				\
	printf "LIBGFXD_ID=\t%s\n" $$(cat ${_TMP}/common.cmake |	\
		grep -A1 "glankk/libgfxd" |				\
		awk '/GIT_TAG/{print $$2}' ) ;				\
	printf "PRISM_ID=\t%s\n" $$(cat ${_TMP}/common.cmake |		\
		grep -A1 "KiritoDv/prism-processor" |			\
		awk '/GIT_TAG/{print $$2}' ) ;				\
	printf "STB_ID=\t\t%s\n" $$(cat ${_TMP}/common.cmake |		\
		grep "nothings/stb" | awk -F'["/]' '{print $$8}' ) ;	\
	printf "STORMLIB_ID=\t%s\n" $$(cat ${_TMP}/common.cmake |	\
		grep -A1 "ladislav-zezula/StormLib" |			\
		awk '/GIT_TAG/{print $$2}' ) ;				\
	printf "THREADPOOL_ID=\t%s\n" $$(cat ${_TMP}/common.cmake |	\
		grep -A1 "bshoshany/thread-pool" |			\
		awk '/GIT_TAG/{print $$2}' ) ;

.include <bsd.port.mk>
