#
# CDDL HEADER START
#
# The contents of this file are subject to the terms of the
# Common Development and Distribution License (the "License").
# You may not use this file except in compliance with the License.
#
# You can obtain a copy of the license at usr/src/OPENSOLARIS.LICENSE
# or http://www.opensolaris.org/os/licensing.
# See the License for the specific language governing permissions
# and limitations under the License.
#
# When distributing Covered Code, include this CDDL HEADER in each
# file and include the License file at usr/src/OPENSOLARIS.LICENSE.
# If applicable, add the following below this CDDL HEADER, with the
# fields enclosed by brackets "[]" replaced with your own identifying
# information: Portions Copyright [yyyy] [name of copyright owner]
#
# CDDL HEADER END
#

#
# Copyright (c) 2015, 2017, Oracle and/or its affiliates. All rights reserved.
#

BUILD_BITS= 		NO_ARCH
include ../../../make-rules/shared-macros.mk

COMPONENT_NAME=		mm-common
COMPONENT_VERSION=	0.9.10
COMPONENT_PROJECT_URL=	https://developer.gnome.org/glibmm/stable/
COMPONENT_ARCHIVE_HASH= \
    sha256:16c0e2bc196b67fbc145edaecb5dbe5818386504fe5703de27002d77140fa217
COMPONENT_BUGDB=        gnome/libraries
COMPONENT_ANITYA_ID=	13152

TPNO=			34494

TEST_TARGET= $(NO_TESTS)

include $(WS_MAKE_RULES)/gnome-component.mk

CONFIGURE_OPTIONS += --disable-network

REQUIRED_PACKAGES += shell/ksh93
