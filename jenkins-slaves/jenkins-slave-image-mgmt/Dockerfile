FROM centos:7 AS builder

ARG SKOPEO_VERSION=0.1.39 
ARG GO_VERSION=1.13.1
ENV GOPATH=/ \
    PATH=$PATH:/usr/local/go/bin \
    GOPATH=/

RUN INSTALL_PKGS="curl gcc make gpgme-devel libassuan-devel btrfs-progs-devel device-mapper-devel ostree-devel" && \
    yum install -y --setopt=tsflags=nodocs $INSTALL_PKGS && \
    rpm -V $INSTALL_PKGS && \
    yum clean all && \
    curl -LO https://dl.google.com/go/go1.13.1.linux-amd64.tar.gz && \
    tar zxf go${GO_VERSION}.linux-amd64.tar.gz -C /usr/local && \
    mkdir -p /src/github.com/containers && \
    curl -LO https://github.com/containers/skopeo/archive/v${SKOPEO_VERSION}.tar.gz && \
    tar zxf v${SKOPEO_VERSION}.tar.gz -C /src/github.com/containers && \
    ln -s /src/github.com/containers/skopeo-${SKOPEO_VERSION} /src/github.com/containers/skopeo && \
    cd /src/github.com/containers/skopeo && \
    make binary-local

FROM registry.access.redhat.com/ubi7/ubi
USER root
COPY --from=builder /src/github.com/containers/skopeo/skopeo /usr/local/bin/skopeo
USER 1001
CMD ["skopeo", "-v"]

#FROM registry.access.redhat.com/openshift3/jenkins-slave-base-rhel7:v3.11
#
#MAINTAINER Andrew Block <ablock@redhat.com>
#
#LABEL com.redhat.component="jenkins-slave-image-mgmt" \
#      name="jenkins-slave-image-mgmt" \
#      architecture="x86_64" \
#      io.k8s.display-name="Jenkins Slave Image Management" \
#      io.k8s.description="Image management tools on top of the jenkins slave base image" \
#      io.openshift.tags="openshift,jenkins,slave,copy"
#USER root
#
#RUN INSTALL_PKGS="skopeo" && \
#    if [ -z $RHEL_RELEASEVER ] ; then RHEL_RELEASEVER=7Server ; fi && \
#    yum install -y --setopt=tsflags=nodocs \
#      --enablerepo=rhel-7-server-rpms \
#      --enablerepo=rhel-7-server-extras-rpms \
#      --releasever=${RHEL_RELEASEVER} \
#      $INSTALL_PKGS && \
#    rpm -V $INSTALL_PKGS && \
#    yum clean all

