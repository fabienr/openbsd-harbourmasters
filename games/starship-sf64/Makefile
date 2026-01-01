COMMENT =	game PC Port of Star Fox 64

V =		2.0.0pl20251225
ID =		e8246fa8e98b9fe00e33a16249aed403d4f81c1a
DISTNAME =	Starship-${ID}
PKGNAME =	starship-sf64-${V}
DIST_TUPLE =	github HarbourMasters Starship ${ID} .

# install location
INSTALL_PREFIX=	share/starship-sf64

# make versions
DR_LIBS_ID=	da35f9d6c7374a95353fd1df1d394d44ab66cf01
GAMECTRLDB_ID=	16ac3e553e23068e26819971f2cc6cd088a7f2f6
LIBULTRASHIP_ID=09dfab5fb2a9a047a6e268dc9db2daad9b2ce5f0
SSE2NEON_ID=	ade5552a32852422e4f34f0beaa51790ef9f4171
TORCH_ID=	cd92cc0f161c5e79e36f5dda0d0029edd3fc8d50
# libultraship
IMGUI_ID=	v1.91.9b-docking
PRISM_ID=	bbcbc7e3f890a5806b579361e7aa0336acd547e7
STB_ID=		0bc88af4de5fb022db643c2d8e549a0927749354
THREADPOOL_ID=	v4.1.0
# Torch
LIBGFXD_ID=	96fd3b849f38b3a7c7b7f3ff03c5921d328e6cdf
SPDLOG_ID=	7e635fca68d014934b4af8a1cf874f63989352b7
TINYXML2_ID=	10.0.0
YAMLCPP_ID=	2f86d13775d119edbb69af52e5f566fd65c6953b

# Starship depends:
# dr_libs, audio decoding libraries for C/C++, each in a single source file, MIT
# SDL_GameControllerDB, mappings to be used with SDL, Zlib licence
# libultraship, reimplementations of libultra (n64 sdk) functions, MIT
# sse2neon, translator from Intel SSE intrinsics to Arm/Aarch64 NEON, MIT
# Torch, generic asset processor for games, MIT
DIST_TUPLE+=	github mackron dr_libs ${DR_LIBS_ID} dist/dr_libs
DIST_TUPLE+=	github mdqinc SDL_GameControllerDB ${GAMECTRLDB_ID} \
		dist/SDL_GameControllerDB
DIST_TUPLE+=	github Kenix3 libultraship ${LIBULTRASHIP_ID} libultraship
DIST_TUPLE+=	github DLTcollab sse2neon ${SSE2NEON_ID} dist/sse2neon
DIST_TUPLE+=	github HarbourMasters Torch ${TORCH_ID} tools/Torch

# libultraship depends:
# imgui, Bloat-free Graphical User interface for C++, MIT
# prism, preprocessor that uses Prism pseudo language, MIT
# stb, single-file public domain libraries(check stb) for C/C++, MIT
# ThreadPool, thread pool library, MIT
DIST_TUPLE+=	github ocornut imgui ${IMGUI_ID} dist/imgui
DIST_TUPLE+=	github KiritoDv prism-processor ${PRISM_ID} dist/prism
DIST_TUPLE+=	github nothings stb ${STB_ID} dist/stb
DIST_TUPLE+=	github bshoshany thread-pool ${THREADPOOL_ID} dist/ThreadPool

# tools/Torch extra depends as standalone binary:
# libgfxd, display list decompiler library, MIT
# spdlog, fast C++ logging library, MIT
DIST_TUPLE+=	github glankk libgfxd ${LIBGFXD_ID} dist/libgfxd
DIST_TUPLE+=	github gabime spdlog ${SPDLOG_ID} dist/spdlog

# tools/Torch common depends:
# yaml-cpp, YAML parser and emitter in C++, MIT
# tinyxml2, C++ XML parser easy to integrate, Zlib licence
DIST_TUPLE+=	github jbeder yaml-cpp ${YAMLCPP_ID} dist/yaml-cpp
DIST_TUPLE+=	github leethomason tinyxml2 ${TINYXML2_ID} dist/tinyxml2

CATEGORIES =	games emulators

MAINTAINER =	xxx

# CC0 1.0 Universal
PERMIT_PACKAGE =	Yes

WANTLIB += ${COMPILER_LIBCXX} GL SDL2 bz2 c fmt m ogg
WANTLIB += spdlog tinyxml2 vorbis vorbisenc vorbisfile
WANTLIB += z zip

# C++20
COMPILER =		base-clang ports-gcc

MODULES =		devel/cmake

BUILD_DEPENDS =		devel/boost \
			textproc/nlohmann-json
RUN_DEPENDS =		devel/desktop-file-utils
LIB_DEPENDS =		audio/libogg \
			audio/libvorbis \
			archivers/libzip \
			devel/sdl2 \
			devel/spdlog \
			textproc/tinyxml2

# upstream default
CONFIGURE_ARGS+=	-DCMAKE_BUILD_TYPE=Release
# system install
CONFIGURE_ARGS+=	-DNON_PORTABLE=ON \
			-DCMAKE_INSTALL_PREFIX=${LOCALBASE}/${INSTALL_PREFIX}
# OpenBSD specific:
# CMAKE_CXX_COMPILER_CLANG_SCAN_DEPS-NOTFOUND, clang-scan-deps is not in base
# port set CMAKE_CXX_STANDARD 20 but not for modules themselves
# disable cmake scan for modules to build on base-clang
CONFIGURE_ARGS+=	-DCMAKE_CXX_SCAN_FOR_MODULES=OFF

# use pre-fetch depends
CONFIGURE_ARGS+= -DFETCHCONTENT_SOURCE_DIR_DR_LIBS=${WRKSRC}/dist/dr_libs
# libultraship
CONFIGURE_ARGS+= -DFETCHCONTENT_SOURCE_DIR_IMGUI=${WRKSRC}/dist/imgui
CONFIGURE_ARGS+= -DFETCHCONTENT_SOURCE_DIR_PRISM=${WRKSRC}/dist/prism
CONFIGURE_ARGS+= -DFETCHCONTENT_SOURCE_DIR_THREADPOOL=${WRKSRC}/dist/ThreadPool
# Torch
CONFIGURE_ARGS+= -DFETCHCONTENT_SOURCE_DIR_LIBGFXD=${WRKSRC}/dist/libgfxd
CONFIGURE_ARGS+= -DFETCHCONTENT_SOURCE_DIR_SPDLOG=${WRKSRC}/dist/spdlog
CONFIGURE_ARGS+= -DFETCHCONTENT_SOURCE_DIR_YAML-CPP=${WRKSRC}/dist/yaml-cpp
CONFIGURE_ARGS+= -DFETCHCONTENT_SOURCE_DIR_TINYXML2=${WRKSRC}/dist/tinyxml2

# XXX debug library lookups
# CONFIGURE_ARGS+=	-DCMAKE_FIND_DEBUG_MODE=TRUE
# XXX game crash with MALLOC_OPTIONS G or U, debug stuff
# CFLAGS =		-O0 -pipe -g
# CXXFLAGS =		-O0 -pipe -g
# CONFIGURE_ARGS+=	-DCMAKE_BUILD_TYPE=Debug
# DEBUG_PACKAGES =	${BUILD_PACKAGES}

NO_TEST =		Yes

_PATCHES=${WRKSRC}/libultraship/cmake/dependencies/patches
pre-patch:
# because we force SOURCE_DIR, patches don't get applied
	# apply libultraship patches to imgui
	@cd ${WRKSRC}/dist/imgui && \
		patch < ${_PATCHES}/imgui-fixes-and-config.patch

pre-configure:
	# setup Starship file(DOWNLOAD ...) depends in ${WRKBUILD}/_deps
	# -> DLTcollab/sse2neon
	@mkdir -p ${WRKBUILD}/_deps/sse2neon
	@cp ${WRKSRC}/dist/sse2neon/sse2neon.h ${WRKBUILD}/_deps/sse2neon/
	# setup libultraship file(DOWNLOAD ...) depends in ${WRKBUILD}/_deps
	# -> nothings/stb
	@mkdir -p ${WRKBUILD}/_deps/stb
	@cp ${WRKSRC}/dist/stb/stb_image.h ${WRKBUILD}/_deps/stb/

post-build:
	# Generate starship.o2r
	@${MODCMAKE_BUILD_TARGET} -t GeneratePortO2R

do-install:
	# game
	${INSTALL_PROGRAM} ${WRKBUILD}/Starship ${PREFIX}/bin/StarshipSF64
	${INSTALL_DATA_DIR} ${PREFIX}/${INSTALL_PREFIX}
	${INSTALL_DATA} ${WRKBUILD}/config.yml ${PREFIX}/${INSTALL_PREFIX}
	${INSTALL_DATA} ${WRKBUILD}/starship.o2r ${PREFIX}/${INSTALL_PREFIX}
	${INSTALL_DATA} \
		${WRKSRC}/dist/SDL_GameControllerDB/gamecontrollerdb.txt \
		${PREFIX}/${INSTALL_PREFIX}
	cp -r ${WRKBUILD}/assets ${PREFIX}/${INSTALL_PREFIX}
	# bundle torch
	${INSTALL_PROGRAM} \
		${WRKBUILD}/TorchExternal/src/TorchExternal-build/torch \
		${PREFIX}/${INSTALL_PREFIX}/torch
	# menu
	${INSTALL_DATA_DIR} ${PREFIX}/share/applications
	${INSTALL_DATA} ${FILESDIR}/starship-sf64.desktop \
		${PREFIX}/share/applications/starship-sf64.desktop
	${INSTALL_DATA_DIR} ${PREFIX}/share/pixmaps
	${INSTALL_DATA} ${WRKSRC}/logo.png \
		${PREFIX}/share/pixmaps/starship-sf64.png

### targets for port maintainer(s)

_TMP=		/tmp/starship-sf64
_STARSHIP_API=	https://api.github.com/repos/HarbourMasters/Starship/contents/
_SSE2NEON_API=	https://api.github.com/repos/DLTcollab/sse2neon/commits
_GAMECTRLDB_API=https://api.github.com/repos/mdqinc/SDL_GameControllerDB/commits
_HM_RAW=	https://raw.githubusercontent.com/HarbourMasters
_STARSHIP_RAW=	${_HM_RAW}/Starship/
_TORCH_RAW=	${_HM_RAW}/Torch/
_ULTRASHIP_RAW=	https://raw.githubusercontent.com/Kenix3/libultraship/

versions:
	# make versions
	@mkdir -p ${_TMP} ;						\
									\
	ftp -VMo ${_TMP}/CMakeLists.starship				\
	    ${_STARSHIP_RAW}/${ID}/CMakeLists.txt ;			\
	printf "DR_LIBS_ID=\t%s\n" $$(cat ${_TMP}/CMakeLists.starship |	\
		grep -A1 "mackron/dr_libs" |				\
		awk '/GIT_TAG/{print $$2}' ) ;				\
									\
	ftp -VMo ${_TMP}/gamectrldb ${_GAMECTRLDB_API} ;		\
	printf "GAMECTRLDB_ID=\t%s\n" $$(cat ${_TMP}/gamectrldb |	\
		grep -m1 "sha" | awk -F'"' '{print $$4}' ) ;		\
									\
	ftp -VMo ${_TMP}/libultraship					\
	    ${_STARSHIP_API}/libultraship?ref=${ID} ;			\
	_ultraship_id=$$(cat ${_TMP}/libultraship | tr "," "\n" |	\
		grep "html_url" | awk -F'["/]' '{print $$10}' ) ;	\
	printf "LIBULTRASHIP_ID=%s\n" $$_ultraship_id ;			\
									\
	ftp -VMo ${_TMP}/sse2neon ${_SSE2NEON_API} ;			\
	printf "SSE2NEON_ID=\t%s\n" $$(cat ${_TMP}/sse2neon |		\
		grep -m1 "sha" | awk -F'"' '{print $$4}' ) ;		\
									\
	ftp -VMo ${_TMP}/torch ${_STARSHIP_API}/tools/Torch?ref=${ID} ;	\
	_torch_id=$$(cat ${_TMP}/torch | tr "," "\n" | 			\
		grep "html_url" | awk -F'["/]' '{print $$10}' ) ;	\
	printf "TORCH_ID=\t%s\n" $$_torch_id ;				\
									\
	echo "# libultraship" ;						\
	_ULTRASHIP_RAW="${_ULTRASHIP_RAW}/$${_ultraship_id}" ;		\
	ftp -VMo ${_TMP}/common.cmake					\
	    $${_ULTRASHIP_RAW}/cmake/dependencies/common.cmake ;	\
	printf "IMGUI_ID=\t%s\n" $$(cat ${_TMP}/common.cmake |		\
		grep -A1 "ocornut/imgui" |				\
		awk '/GIT_TAG/{print $$2}' ) ;				\
	printf "PRISM_ID=\t%s\n" $$(cat ${_TMP}/common.cmake |		\
		grep -A1 "KiritoDv/prism-processor" |			\
		awk '/GIT_TAG/{print $$2}' ) ;				\
	printf "STB_ID=\t\t%s\n" $$(cat ${_TMP}/common.cmake |		\
		grep "nothings/stb" | awk -F'["/]' '{print $$8}' ) ;	\
	printf "THREADPOOL_ID=\t%s\n" $$(cat ${_TMP}/common.cmake |	\
		grep -A1 "bshoshany/thread-pool" |			\
		awk '/GIT_TAG/{print $$2}' ) ;				\
									\
	echo "# Torch" ;						\
	_TORCH_RAW="${_TORCH_RAW}/$${_torch_id}/CMakeLists.txt" ;	\
	ftp -VMo ${_TMP}/CMakeLists.torch $${_TORCH_RAW} ;		\
	printf "LIBGFXD_ID=\t%s\n" $$(cat ${_TMP}/CMakeLists.torch |	\
		grep -A1 "glankk/libgfxd" |				\
		awk '/GIT_TAG/{print $$2}' ) ;				\
	printf "SPDLOG_ID=\t%s\n" $$(cat ${_TMP}/CMakeLists.torch |	\
		grep -m1 -A1 "gabime/spdlog" |				\
		awk '/GIT_TAG/{print $$2}' ) ;				\
	printf "TINYXML2_ID=\t%s\n" $$(cat ${_TMP}/CMakeLists.torch |	\
		grep -A1 "leethomason/tinyxml2" |			\
		awk '/GIT_TAG/{print $$2}' ) ;				\
	printf "YAMLCPP_ID=\t%s\n" $$(cat ${_TMP}/CMakeLists.torch |	\
		grep -A1 "jbeder/yaml-cpp" |				\
		awk '/GIT_TAG/{print $$2}' ) ;

.include <bsd.port.mk>
