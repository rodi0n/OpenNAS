# $FreeBSD: net-mgmt/ocsinventory-agent/Makefile 327757 2013-09-20 22:24:43Z bapt $

PORTNAME=	Ocsinventory
PORTVERSION=	2.1rc1
PORTEPOCH=	1
CATEGORIES=	net-mgmt
MASTER_SITES=	http://launchpad.net/ocsinventory-unix-agent/stable-2.1/${PORTVERSION}/+download/
PKGNAMESUFFIX=	-Unix-Agent
DISTNAME=	${PORTNAME}${PKGNAMESUFFIX}-${PORTVERSION}

MAINTAINER=	araujo@FreeBSD.org
COMMENT=	Keep track of the computers configuration and software

LICENSE=	GPLv2

BUILD_DEPENDS=	p5-libwww>=0:${PORTSDIR}/www/p5-libwww \
		p5-XML-Simple>=0:${PORTSDIR}/textproc/p5-XML-Simple \
		p5-Net-IP>=0:${PORTSDIR}/net-mgmt/p5-Net-IP \
		p5-Proc-Daemon>=0:${PORTSDIR}/devel/p5-Proc-Daemon \
		p5-Proc-PID-File>=0:${PORTSDIR}/devel/p5-Proc-PID-File \
		p5-Net-SNMP>=0:${PORTSDIR}/net-mgmt/p5-Net-SNMP \
		p5-Net-Netmask>=0:${PORTSDIR}/net-mgmt/p5-Net-Netmask \
		p5-Net-Ping>=0:${PORTSDIR}/net/p5-Net-Ping
RUN_DEPENDS+=			${BUILD_DEPENDS}

USES=		perl5
FETCH_ARGS=
USE_PERL5=	configure
WRKSRC=		${WRKDIR}/${PORTNAME}${PKGNAMESUFFIX}-${PORTVERSION}
MAN1=		ocsinventory-agent.1
MAN3=		Ocsinventory::Agent::XML::Inventory.3 \
			Ocsinventory::Agent::Common.3
SUB_FILES=	pkg-message
SUB_LIST=	SITE_PERL=${SITE_PERL}

NO_STAGE=	yes
post-install:
	@${CAT} ${PKGMESSAGE}

do-install:
	# Perl
	${INSTALL_PROGRAM} /usr/bin/perl ${NAS4FREE_ROOTFS}/usr/bin
	${MKDIR} -p ${NAS4FREE_ROOTFS}/usr/local/lib/perl5/${PERL_VER}
	${MKDIR} -p ${NAS4FREE_ROOTFS}/usr/local/lib/perl5/site_perl/${PERL_VER}
	${CP} -R /usr/local/lib/perl5/${PERL_VER}/* ${NAS4FREE_ROOTFS}/usr/local/lib/perl5/${PERL_VER}/
	${CP} -R /usr/local/lib/perl5/site_perl/${PERL_VER}/* ${NAS4FREE_ROOTFS}/usr/local/lib/perl5/site_perl/${PERL_VER}/

	#Ocs inventory
	${MKDIR} -p ${NAS4FREE_ROOTFS}/usr/local/lib/perl5/site_perl/${PERL_VER}/Ocsinventory/
	${CP} -R ${WRKSRC}/lib/Ocsinventory/* ${NAS4FREE_ROOTFS}/usr/local/lib/perl5/site_perl/${PERL_VER}/Ocsinventory/
	${INSTALL_SCRIPT} ${WRKSRC}/ocsinventory-agent ${NAS4FREE_ROOTFS}/usr/local/bin
	@${MKDIR} -p ${NAS4FREE_ROOTFS}/usr/local/etc/ocsinventory/

	# Manpages
	for I in ${MAN1}; \
		do (rm -f ${WRKSRC}/blib/man1/$$I.gz; gzip -c ${WRKSRC}/blib/man1/$$I >${WRKSRC}/blib/man1/$$I.gz; \
			$(INSTALL_DATA) ${WRKSRC}/blib/man1/$$I.gz ${NAS4FREE_ROOTFS}/usr/share/man/man1/$$I.gz; \
			rm -f ${WRKSRC}/blib/man1/$$I.gz); \
	done
	for I in ${MAN3}; \
		do (rm -f ${WRKSRC}/blib/man3/$$I.gz; gzip -c ${WRKSRC}/blib/man3/$$I >${WRKSRC}/blib/man3/$$I.gz; \
			$(INSTALL_DATA) ${WRKSRC}/blib/man3/$$I.gz ${NAS4FREE_ROOTFS}/usr/share/man/man1/$$I.gz; \
			rm -f ${WRKSRC}/blib/man3/$$I.gz); \
	done
	
	# Install service script
	@${INSTALL_SCRIPT} -v ${FILESDIR}/ocsinventory_agent.in ${NAS4FREE_ROOTFS}/etc/rc.d/ocsinventory_agent
.include <bsd.port.mk>