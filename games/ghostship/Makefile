COMMENT =	game PC Port of Super Mario 64

V =		1.0.2pl20260305
H =		c51008985b51514e82878d973707742764abf853
DISTNAME =	Ghostship-${H}
PKGNAME =	ghostship-${V}
DIST_TUPLE =	github HarbourMasters Ghostship ${H} .

# install location
INSTALL_PREFIX=	share/ghostship

# make versions
GAMECTRLDB_ID=	ff46b15524f72e75d4b258343e77d0db6c5d351d
LIBULTRASHIP_ID=0b76d895fbf49a990300d53d29c3c9efce06082c
TORCH_ID=	97bddc6fc6114904f37937da693b55a76df0910e
# libultraship
IMGUI_ID=	v1.91.9b-docking
PRISM_ID=	bbcbc7e3f890a5806b579361e7aa0336acd547e7
STB_ID=		0bc88af4de5fb022db643c2d8e549a0927749354
THREADPOOL_ID=	v4.1.0
# Torch
LIBGFXD_ID=	96fd3b849f38b3a7c7b7f3ff03c5921d328e6cdf
SPDLOG_ID=	79524ddd08a4ec981b7fea76afd08ee05f83755d
TINYXML2_ID=	10.0.0
YAMLCPP_ID=	2f86d13775d119edbb69af52e5f566fd65c6953b

# Ghostship depends:
# SDL_GameControllerDB, mappings to be used with SDL, Zlib licence
# libultraship, reimplementations of libultra (n64 sdk) functions, MIT
# Torch, generic asset processor for games, MIT
DIST_TUPLE+=	github mdqinc SDL_GameControllerDB ${GAMECTRLDB_ID} \
		dist/SDL_GameControllerDB
DIST_TUPLE+=	github Kenix3 libultraship ${LIBULTRASHIP_ID} libultraship
DIST_TUPLE+=	github HarbourMasters Torch ${TORCH_ID} Torch

# libultraship depends:
# imgui, Bloat-free Graphical User interface for C++, MIT
# prism, preprocessor that uses Prism pseudo language, MIT
# stb, single-file public domain libraries(check stb) for C/C++, MIT
# ThreadPool, thread pool library, MIT
DIST_TUPLE+=	github ocornut imgui ${IMGUI_ID} dist/imgui
DIST_TUPLE+=	github KiritoDv prism-processor ${PRISM_ID} dist/prism
DIST_TUPLE+=	github nothings stb ${STB_ID} dist/stb
DIST_TUPLE+=	github bshoshany thread-pool ${THREADPOOL_ID} dist/ThreadPool

# Torch extra depends as standalone binary:
# libgfxd, display list decompiler library, MIT
# spdlog, fast C++ logging library, MIT
# XXX standalone build included to generate .otr ?
DIST_TUPLE+=	github glankk libgfxd ${LIBGFXD_ID} dist/libgfxd
DIST_TUPLE+=	github gabime spdlog ${SPDLOG_ID} dist/spdlog

# Torch common depends:
# yaml-cpp, YAML parser and emitter in C++, MIT
# tinyxml2, C++ XML parser easy to integrate, Zlib licence
DIST_TUPLE+=	github jbeder yaml-cpp ${YAMLCPP_ID} dist/yaml-cpp
DIST_TUPLE+=	github leethomason tinyxml2 ${TINYXML2_ID} dist/tinyxml2

CATEGORIES =	games emulators

MAINTAINER =	xxx

# MIT
PERMIT_PACKAGE =	Yes

WANTLIB += ${COMPILER_LIBCXX} GL SDL2 c fmt m spdlog
WANTLIB += tinyxml2 zip

# C++20
COMPILER =		base-clang ports-gcc

MODULES =		devel/cmake

BUILD_DEPENDS =		devel/boost \
			textproc/nlohmann-json
RUN_DEPENDS =		devel/desktop-file-utils
LIB_DEPENDS =		archivers/libzip \
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
# libultraship
CONFIGURE_ARGS+=\
	-DFETCHCONTENT_SOURCE_DIR_IMGUI=${WRKSRC}/dist/imgui \
	-DFETCHCONTENT_SOURCE_DIR_PRISM=${WRKSRC}/dist/prism \
	-DFETCHCONTENT_SOURCE_DIR_THREADPOOL=${WRKSRC}/dist/ThreadPool
# Torch
CONFIGURE_ARGS+=\
	-DFETCHCONTENT_SOURCE_DIR_LIBGFXD=${WRKSRC}/dist/libgfxd \
	-DFETCHCONTENT_SOURCE_DIR_SPDLOG=${WRKSRC}/dist/spdlog \
	-DFETCHCONTENT_SOURCE_DIR_YAML-CPP=${WRKSRC}/dist/yaml-cpp \
	-DFETCHCONTENT_SOURCE_DIR_TINYXML2=${WRKSRC}/dist/tinyxml2

NO_TEST =		Yes

# XXX debug library lookups
# CONFIGURE_ARGS+=	-DCMAKE_FIND_DEBUG_MODE=TRUE
# XXX game crash with MALLOC_OPTIONS G or U, debug stuff
DEBUG_PACKAGES =	${BUILD_PACKAGES}
CFLAGS =		-O0 -pipe -g
CXXFLAGS =		-O0 -pipe -g
CONFIGURE_ARGS+=	-DCMAKE_BUILD_TYPE=Debug

_PATCHES=${WRKSRC}/libultraship/cmake/dependencies/patches
pre-patch:
# because we force SOURCE_DIR, patches don't get applied
	# apply libultraship patches to imgui
	@cd ${WRKSRC}/dist/imgui && \
		patch < ${_PATCHES}/imgui-fixes-and-config.patch

pre-configure:
	# setup libultraship file(DOWNLOAD ...) depends in ${WRKBUILD}/_deps
	# -> nothings/stb
	@mkdir -p ${WRKBUILD}/_deps/stb
	@cp ${WRKSRC}/dist/stb/stb_image.h ${WRKBUILD}/_deps/stb/

post-build:
	# Generate ghostship.o2r
	@${MODCMAKE_BUILD_TARGET} -t GeneratePortO2R

do-install:
	# game
	${INSTALL_PROGRAM} ${WRKBUILD}/Ghostship ${PREFIX}/bin/ghostship
	${INSTALL_DATA_DIR} ${PREFIX}/${INSTALL_PREFIX}
	${INSTALL_DATA} ${WRKBUILD}/config.yml ${PREFIX}/${INSTALL_PREFIX}
	${INSTALL_DATA} ${WRKBUILD}/ghostship.o2r ${PREFIX}/${INSTALL_PREFIX}
	${INSTALL_DATA} \
		${WRKSRC}/dist/SDL_GameControllerDB/gamecontrollerdb.txt \
		${PREFIX}/${INSTALL_PREFIX}
	cp -r ${WRKBUILD}/assets ${PREFIX}/${INSTALL_PREFIX}
	# bundle torch to allow manual ROM conversion
	${INSTALL_PROGRAM} \
		${WRKBUILD}/TorchExternal/src/TorchExternal-build/torch \
		${PREFIX}/${INSTALL_PREFIX}/torch
	# menu
	${INSTALL_DATA_DIR} ${PREFIX}/share/applications
	${INSTALL_DATA} ${FILESDIR}/ghostship.desktop \
		${PREFIX}/share/applications/ghostship.desktop
	${INSTALL_DATA_DIR} ${PREFIX}/share/pixmaps
	${INSTALL_DATA} ${WRKSRC}/logo.png \
		${PREFIX}/share/pixmaps/ghostship.png

### targets for port maintainer(s)

_TMP=		/tmp/ghostship
_GHOSTSHIP_API=	https://api.github.com/repos/HarbourMasters/Ghostship/contents/
_GAMECTRLDB_API=https://api.github.com/repos/mdqinc/SDL_GameControllerDB/commits
_TORCH_RAW=	https://raw.githubusercontent.com/HarbourMasters/Torch/
_ULTRASHIP_RAW=	https://raw.githubusercontent.com/Kenix3/libultraship/

versions:
	# make versions
	@mkdir -p ${_TMP} ;						\
									\
	ftp -VMo ${_TMP}/gamectrldb ${_GAMECTRLDB_API} ;		\
	printf "GAMECTRLDB_ID=\t%s\n" $$(cat ${_TMP}/gamectrldb |	\
		grep -m1 "sha" | awk -F'"' '{print $$4}' ) ;		\
									\
	ftp -VMo ${_TMP}/libultraship					\
	    ${_GHOSTSHIP_API}/libultraship?ref=${H} ;			\
	_ultraship_id=$$(cat ${_TMP}/libultraship | tr "," "\n" |	\
		grep "html_url" | awk -F'["/]' '{print $$10}' ) ;	\
	printf "LIBULTRASHIP_ID=%s\n" $$_ultraship_id ;			\
									\
	ftp -VMo ${_TMP}/torch ${_GHOSTSHIP_API}/Torch?ref=${H} ;	\
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
