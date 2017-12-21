FROM cmdy/alpine-glibc:2.23-r3
MAINTAINER zhang_lin66@foxmail.com

ENV CMDY_DIR="/usr/local/cmdy" \
    JAVA_VERSION=8 \
    JAVA_UPDATE=152 \
    JAVA_BUILD=16 \
    JAVA_TYPE="jre" \
    JAVA_STR="aa0333dd3019491ca4f6ddbe78cdb6d0"

# Install wget & ca-certs so we can download the server JRE
# Work within the CMDY_DIR to remove the need to move files around
RUN apk add --no-cache --virtual=build-dependencies wget ca-certificates && \
    mkdir -p "${CMDY_DIR}" && \
    cd "${CMDY_DIR}" && \
    # wget file allows us to dynamically change the version via variables.
    wget --header "Cookie: oraclelicense=accept-securebackup-cookie;" \
        "http://download.oracle.com/otn-pub/java/jdk/${JAVA_VERSION}u${JAVA_UPDATE}-b${JAVA_BUILD}/${JAVA_STR}/${JAVA_TYPE}-${JAVA_VERSION}u${JAVA_UPDATE}-linux-x64.tar.gz" && \
    tar -xzf "${JAVA_TYPE}-${JAVA_VERSION}u${JAVA_UPDATE}-linux-x64.tar.gz" && \
    ln -s "${CMDY_DIR}/jdk1.${JAVA_VERSION}.0_${JAVA_UPDATE}"  "${CMDY_DIR}/server-jre-${JAVA_VERSION}" && \
    apk del build-dependencies && \
    # Remove the tar.gz
    rm -f "${JAVA_TYPE}-${JAVA_VERSION}u${JAVA_UPDATE}-linux-x64.tar.gz" && \
    cd "${CMDY_DIR}/${JAVA_TYPE}-${JAVA_VERSION}" && \
    # Remove unnecessary files from the archive
    rm -Rf \
      COPYRIGHT \
      LICENSE \
      README.html \
      THIRDPARTYLICENSEREADME-JAVAFX.txt \
      THIRDPARTYLICENSEREADME.txt \
      release \
      src.zip \
      man/

# Update PATH to include Java/bin
ENV PATH="${CMDY_DIR}/${JAVA_TYPE}-${JAVA_VERSION}/bin:${PATH}"

# Should we only provide an entry point to Java?
CMD ["/bin/sh"]
