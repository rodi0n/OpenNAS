# $FreeBSD: head/java/openjdk8/Makefile 392604 2015-07-20 21:16:09Z jkim $

PORTNAME=	openjdk8-jre

COMMENT=	Java Runtime Environment ${JDK_MAJOR_VERSION}

BUILD_JRE=      yes
MASTERDIR=      /usr/ports/java/openjdk8

STAGEDIR=	${NAS4FREE_ROOTFS}

do-install:
	$(MAKE) -C  ${WRKSRC} install DESTDIR=${NAS4FREE_ROOTFS}

.include "${MASTERDIR}/Makefile"