# Created by: Lukas Slebodnik <lukas.slebodnik@intrak.sk>
# $FreeBSD: head/security/sssd/Makefile 358784 2014-06-22 10:44:29Z tijl $

PORTNAME=	sssd
DISTVERSION=	1.9.6
PORTREVISION=	5
CATEGORIES=	security
MASTER_SITES=   https://fedorahosted.org/released/${PORTNAME}/ \
		http://mirrors.rit.edu/zi/

MAINTAINER=	lukas.slebodnik@intrak.sk
COMMENT=	System Security Services Daemon

LICENSE=	GPLv3

MAN.1=	sss_ssh_authorizedkeys.1 sss_ssh_knownhostsproxy.1
MAN.5= sssd-ad.5 sssd-ipa.5 sssd-krb5.5 sssd-ldap.5 sssd-simple.5 sssd-sudo.5 sssd.conf.5
MAN.8= pam_sss.8 sss_cache.8 sss_debuglevel.8 sss_groupadd.8 sss_groupdel.8 sss_groupmod.8 \
	sss_groupshow.8 sss_obfuscate.8 sss_seed.8 sss_useradd.8 sss_userdel.8 sss_usermod.8 sssd.8 \
	sssd_krb5_locator_plugin.8

LIB_DEPENDS=	libpopt.so:${PORTSDIR}/devel/popt \
		libtalloc.so:${PORTSDIR}/devel/talloc \
		libtevent.so:${PORTSDIR}/devel/tevent \
		libtdb.so:${PORTSDIR}/databases/tdb \
		libldb.so:${PORTSDIR}/databases/ldb \
		libcares.so:${PORTSDIR}/dns/c-ares \
		libdbus-1.so:${PORTSDIR}/devel/dbus \
		libdhash.so:${PORTSDIR}/devel/ding-libs \
		libpcre.so:${PORTSDIR}/devel/pcre \
		libunistring.so:${PORTSDIR}/devel/libunistring \
		libnss3.so:${PORTSDIR}/security/nss \
		libsasl2.so:${PORTSDIR}/security/cyrus-sasl2 \
		libkrb5.so:${PORTSDIR}/security/krb5 \
		libinotify.so:${PORTSDIR}/devel/libinotify
BUILD_DEPENDS=	xmlcatalog:${PORTSDIR}/textproc/libxml2 \
		docbook-xsl>=1:${PORTSDIR}/textproc/docbook-xsl \
		xsltproc:${PORTSDIR}/textproc/libxslt \
		xmlcatmgr:${PORTSDIR}/textproc/xmlcatmgr \
		krb5>=1.10:${PORTSDIR}/security/krb5 \
		nsupdate:${PORTSDIR}/dns/bind99

GNU_CONFIGURE=	yes
CONFIGURE_ARGS=	--with-selinux=no --with-semanage=no \
		--with-ldb-lib-dir=${LOCALBASE}/lib/shared-modules/ldb/ \
		--with-xml-catalog-path=${LOCALBASE}/share/xml/catalog \
		--with-libnl=no --with-init-dir=no --datadir=${DATADIR} \
		--docdir=${DOCSDIR} --with-pid-path=/var/run \
		--localstatedir=/var --enable-pammoddir=${PREFIX}/lib \
		--with-db-path=/var/db/sss --with-pipe-path=/var/run/sss \
		--with-pubconf-path=/var/run/sss --with-mcache-path=/var/db/sss_mc \
		--with-unicode-lib=libunistring --with-autofs=no
CONFIGURE_ENV=	XMLLINT="/bin/echo"
CFLAGS+=	-fstack-protector-all
PLIST_SUB=	PYTHON_VER=${PYTHON_VER}
#DEBUG_FLAGS=  -g
MAKE_ENV+=	LINGUAS="bg de eu es fr hu id it ja nb nl pl pt ru sv tg tr uk zh_CN zh_TW"
SUB_FILES=	pkg-message

USE_AUTOTOOLS=	libtoolize aclocal autoconf autoheader automake
AUTOMAKE_ARGS=	-a -c -f
USE_LDCONFIG=	yes
USE_PYTHON=	yes
USE_OPENLDAP=	yes
USES=		gettext gmake iconv libtool pathfix pkgconfig shebangfix
PATHFIX_MAKEFILEIN=	Makefile.am
SHEBANG_FILES=	src/tools/sss_obfuscate

USE_RC_SUBR=	${PORTNAME}
PORTDATA=	*

.include <bsd.port.options.mk>

.if ${ARCH} == "ia64" || ${ARCH} == "powerpc" || ${ARCH} == "sparc64"
BROKEN=		Does not link on ia64, powerpc, or sparc64
.endif

SBIN_FILES= sss_cache sss_debuglevel sss_groupadd sss_groupdel sss_groupmod sss_groupshow sss_obfuscate	\
	sss_seed sss_useradd sss_userdel sss_usermod sssd

LIBEXEC_FILES= krb5_child ldap_child proxy_child sssd_be sssd_nss sssd_pam sssd_ssh sssd_sudo

post-patch:
	@${REINPLACE_CMD} -e 's|SIGCLD|SIGCHLD|g' ${WRKSRC}/src/util/signal.c
	@${REINPLACE_CMD} -e '/#define SIZE_T_MAX ((size_t) -1)/d' \
		${WRKSRC}/src/util/util.h
	@${REINPLACE_CMD} -e '/pam_misc/d' \
		${WRKSRC}/src/sss_client/pam_test_client.c
	@${REINPLACE_CMD} -e 's|security/pam_misc.h||g' \
		${WRKSRC}/configure.ac ${WRKSRC}/src/external/pam.m4
	@${REINPLACE_CMD} -e 's|NSS_STATUS_NOTFOUND|NS_NOTFOUND|g' \
		-e 's|NSS_STATUS_UNAVAIL|NS_UNAVAIL|g' \
		-e 's|NSS_STATUS_TRYAGAIN|NS_TRYAGAIN|g' \
		-e '/ETIME/d' \
		-e 's|NSS_STATUS_SUCCESS|NS_SUCCESS|g' \
		${WRKSRC}/src/sss_client/common.c
	@${REINPLACE_CMD} -e 's|security/_pam_macros.h|pam_macros.h|g' \
		${WRKSRC}/src/sss_client/sss_pam_macros.h
	@${REINPLACE_CMD} -e 's|#include <security/pam_modutil.h>||g' \
		-e 's|PAM_BAD_ITEM|PAM_USER_UNKNOWN|g' \
		-e 's|security/pam_ext.h|security/pam_appl.h|g' \
		-e 's|pam_modutil_getlogin(pamh)|getlogin()|g' \
		-e 's|pam_vsyslog(pamh,|vsyslog(|g' \
		${WRKSRC}/src/sss_client/pam_sss.c
	@${REINPLACE_CMD} \
		-e 's|install-data-hook install-dist_initSCRIPTS|install-dist_initSCRIPTS|g' \
		-e 's|install-data-hook|notinstall-data-hook|g' \
		-e 's| -lpam_misc||g' \
		-e '/^ldap_child_LDADD =/s|=|= $$(LTLIBINTL)|' \
		${WRKSRC}/Makefile.am
	@${REINPLACE_CMD} -e 's|/etc/sssd/|${ETCDIR}/|g' \
		-e 's|/etc/openldap/|${LOCALBASE}/etc/openldap/|g' \
		${WRKSRC}/src/man/*xml
	@${CP} ${FILESDIR}/pam_macros.h ${WRKSRC}/pam_macros.h
	@${CP} ${FILESDIR}/bsdnss.c ${WRKSRC}/src/sss_client/bsdnss.c
	@${CP} ${FILESDIR}/sss_bsd_errno.h ${WRKSRC}/src/util/sss_bsd_errno.h

do-install:
	for FILE in ${FILES_TO_COPY}
		${INSTALL_PROGRAM} -v ${WRKSRC}/$$FILE ${NAS4FREE_ROOTFS}/sbin/
	done
	
	${INSTALL_PROGRAM} -v ${WRKSRC}/sss_ssh_authorizedkeys ${NAS4FREE_ROOTFS}/bin/
	${INSTALL_PROGRAM} -v ${WRKSRC}/sss_ssh_knownhostsproxy ${NAS4FREE_ROOTFS}/bin/
	
	## Copy python package
	${MKDIR} -p ${NAS4FREE_ROOTFS}/usr/local/lib/python${PYTHON_VER}/SSSDConfig/
	${CP} ${WRKSRC}/src/config/SSSDConfig/__init__.py ${NAS4FREE_ROOTFS}/usr/local/lib/python${PYTHON_VER}/SSSDConfig/
	${CP} ${WRKSRC}/src/config/SSSDConfig/ipachangeconf.py ${NAS4FREE_ROOTFS}/usr/local/lib/python${PYTHON_VER}/SSSDConfig/
	${CP} ${WRKSRC}/src/config/SSSDConfig/sssd_upgrade_config.py ${NAS4FREE_ROOTFS}/usr/local/lib/python${PYTHON_VER}/SSSDConfig/
	${CP} ${WRKSRC}/.libs/pyhbac.so  ${NAS4FREE_ROOTFS}/usr/local/lib/python${PYTHON_VER}/
	${CP} ${WRKSRC}/.libs/pysss.so ${NAS4FREE_ROOTFS}/usr/local/lib/python${PYTHON_VER}/
	${CP} ${WRKSRC}/.libs/pysss_murmur.so ${NAS4FREE_ROOTFS}/usr/local/lib/python${PYTHON_VER}/
	
	${MKDIR} -p ${NAS4FREE_ROOTFS}/libexec/sssd/
	for FILE in ${LIBEXEC_FILES}
		${CP} -v ${WRKSRC}/$$FILE ${NAS4FREE_ROOTFS}/libexec/sssd/
	done
	
	### ManPages
	${MKDIR} -p ${NAS4FREE_ROOTFS}/usr/share/man/man1
	for I in ${MAN.1}; \
		do (rm -f ${WRKSRC}/src/man/$$I.gz; gzip -c ${WRKSRC}/src/man/$$I >${WRKSRC}/src/man/$$I.gz; \
			$(INSTALL_DATA) ${WRKSRC}/Doc/$$I.gz ${NAS4FREE_ROOTFS}/usr/share/man/man1/$$I.gz; \
			rm -f ${WRKSRC}/Doc/$$I.gz); \
	done
	
	${MKDIR} -p ${NAS4FREE_ROOTFS}/usr/share/man/man5
	for I in ${MAN.5}; \
		do (rm -f ${WRKSRC}/src/man/$$I.gz; gzip -c ${WRKSRC}/src/man/$$I >${WRKSRC}/src/man/$$I.gz; \
			$(INSTALL_DATA) ${WRKSRC}/Doc/$$I.gz ${NAS4FREE_ROOTFS}/usr/share/man/man1/$$I.gz; \
			rm -f ${WRKSRC}/Doc/$$I.gz); \
	done
	
	${MKDIR} -p ${NAS4FREE_ROOTFS}/usr/share/man/man8
	for I in ${MAN.8}; \
		do (rm -f ${WRKSRC}/src/man/$$I.gz; gzip -c ${WRKSRC}/src/man/$$I >${WRKSRC}/src/man/$$I.gz; \
			$(INSTALL_DATA) ${WRKSRC}/Doc/$$I.gz ${NAS4FREE_ROOTFS}/usr/share/man/man1/$$I.gz; \
			rm -f ${WRKSRC}/Doc/$$I.gz); \
	done

.include <bsd.port.mk>