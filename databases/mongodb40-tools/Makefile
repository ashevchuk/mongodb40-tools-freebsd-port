# $FreeBSD$

PORTNAME=		mongodb40-tools
DISTVERSIONPREFIX=	r
DISTVERSION=		4.0.8
CATEGORIES=		databases

MAINTAINER=		dev.ashevchuk@gmail.com
COMMENT=		Tools for MongoDB

LICENSE=		APACHE20

ONLY_FOR_ARCHS=		amd64 i386
ONLY_FOR_ARCHS_REASON= "not yet ported to anything other than i386 and amd64"

BUILD_DEPENDS=		${LOCALBASE}/go${GO_LANG_VER_PATH_STR}/bin/go:lang/go${GO_LANG_VER_PATH_STR}

USES=			compiler:c++14-lang localbase

GO_LANG_VERSION=        1.4
GO_LANG_VER_PATH_STR=   ${GO_LANG_VERSION:S/.//}

GO_LANG_PATH_DIR= 	.gopath

CONFLICTS_INSTALL=	mongodb3[46] mongodb3[46]-tools

USE_LDCONFIG=		yes

CC=			clang

USE_GITHUB=		yes
GH_ACCOUNT=		mongodb
GH_PROJECT=		mongo-tools
GH_TAG=			5db0b4a18cc1366ebd368eed8e7c4d426d851f13

OPTIONS_SUB=		yes
NO_OPTIONS_SORT=	yes

OPTIONS_DEFINE=		DOCS
OPTIONS_DEFAULT=	SSL SASL MONGOFILES MONGOEXPORT MONGOIMPORT MONGORESTORE MONGODUMP

OPTIONS_MULTI=		TOOLS SECURITY
OPTIONS_MULTI_TOOLS=	BSONDUMP MONGOSTAT MONGOFILES MONGOEXPORT MONGOIMPORT MONGORESTORE MONGODUMP MONGOTOP MONGOREPLAY
OPTIONS_MULTI_SECURITY=	SSL SASL

BSONDUMP_DESC=		BSON files into human-readable formats
MONGOSTAT_DESC=		Status of a running mongod or mongos instance
MONGOFILES_DESC=	Interface to GridFS in a MongoDB instance
MONGOEXPORT_DESC=	JSON or CSV export of MongoDB instance data
MONGOIMPORT_DESC=	Importing JSON, CSV, or TSV into a MongoDB instance
MONGORESTORE_DESC=	BSON data to a MongoDB instance
MONGODUMP_DESC=		BSON data from the contents of a MongoDB instance
MONGOTOP_DESC=		Track the amount of data I/O time
MONGOREPLAY_DESC=	Traffic capture and replay tool

BSONDUMP_VARS=		tool_build+=bsondump
MONGOSTAT_VARS=		tool_build+=mongostat
MONGOFILES_VARS=	tool_build+=mongofiles
MONGOEXPORT_VARS=	tool_build+=mongoexport
MONGOIMPORT_VARS=	tool_build+=mongoimport
MONGORESTORE_VARS=	tool_build+=mongorestore
MONGODUMP_VARS=		tool_build+=mongodump
MONGOTOP_VARS=		tool_build+=mongotop
MONGOREPLAY_VARS=	tool_build+=mongoreplay

SSL_USE=		my_tags=ssl
SSL_USES=		ssl

SASL_USE=		my_tags=sasl
SASL_LIB_DEPENDS=	libsasl2.so:security/cyrus-sasl2

.include <bsd.port.options.mk>

post-patch:
	@${ECHO_MSG} "Preparing hierarchy"
	${MKDIR} ${WRKSRC}/${GO_LANG_PATH_DIR} && \
	${LN} -sf ${WRKSRC}/vendor ${WRKSRC}/${GO_LANG_PATH_DIR}/src && \
	${MKDIR} ${WRKSRC}/${GO_LANG_PATH_DIR}/src/github.com/${GH_ACCOUNT} && \
	${LN} -sf ${WRKSRC} ${WRKSRC}/${GO_LANG_PATH_DIR}/src/github.com/${GH_ACCOUNT}/${GH_PROJECT}

do-build:
.for tool in ${TOOL_BUILD}
	@${ECHO_MSG} "Building ${tool}"
	${SETENV} ${MAKE_ENV} \
	GOPATH="${WRKSRC}/${GO_LANG_PATH_DIR}:${WRKSRC}/vendor" \
	CGO_CPPFLAGS="-isystem ${LOCALBASE}/include" \
	CGO_LDFLAGS="-L${LOCALBASE}/lib" \
	${LOCALBASE}/go${GO_LANG_VER_PATH_STR}/bin/go build \
	-o ${WRKSRC}/bin/${tool} \
	-tags "${USE_MY_TAGS}" \
	${WRKSRC}/${tool}/main/${tool}.go
.endfor

do-install:
.for tool in ${TOOL_BUILD}
	@${ECHO_MSG} "Installing ${tool}"
	${INSTALL_PROGRAM} ${WRKSRC}/bin/${tool} ${STAGEDIR}${PREFIX}/bin/
.endfor

	${MKDIR} ${STAGEDIR}${DOCSDIR}
.for doc in README.md CONTRIBUTING.md LICENSE.md THIRD-PARTY-NOTICES
	@${ECHO_MSG} "Installing ${doc}"
	${INSTALL_MAN} ${WRKSRC}/${doc} ${STAGEDIR}${DOCSDIR}
.endfor

.include <bsd.port.mk>
