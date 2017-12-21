FROM cmdy/alpine-glibc:2.23-r3

MAINTAINER  cmdy<zhang_lin66@foxmail.com>

ENV CMDY_DIR="/usr/local/cmdy" \
    JAVA_VERSION=8 \
    JAVA_UPDATE=152 \
    JAVA_BUILD=16 \
    JAVA_TYPE="server-jre" \
    JAVA_STR="aa0333dd3019491ca4f6ddbe78cdb6d0"

ENV TOMCAT_MAJOR 8
ENV TOMCAT_VERSION 8.0.48
ENV JAVA_HOME /usr/local/cmdy/server-jre-8
ENV CATALINA_HOME /usr/local/tomcat
ENV PATH $CATALINA_HOME/bin:$PATH
ENV TOMCAT_NATIVE_LIBDIR $CATALINA_HOME/native-jni-lib
ENV LD_LIBRARY_PATH ${LD_LIBRARY_PATH:+$LD_LIBRARY_PATH:}$TOMCAT_NATIVE_LIBDIR
ENV TOMCAT_TGZ_URL http://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-${TOMCAT_MAJOR}/v${TOMCAT_VERSION}/bin/apache-tomcat-${TOMCAT_VERSION}.tar.gz 


RUN apk add --no-cache --virtual .build-deps \
        ca-certificates \
        tar \
        unzip \
        wget \
        openssl \
        openssl-dev \
        apr-dev \
        coreutils \
        dpkg-dev dpkg \
        gcc \
        libc-dev \
        make \
        #server-jre
    && mkdir -p "${CMDY_DIR}" \
    && cd "${CMDY_DIR}"  \
    && wget --header "Cookie: oraclelicense=accept-securebackup-cookie;" \
       "http://download.oracle.com/otn-pub/java/jdk/${JAVA_VERSION}u${JAVA_UPDATE}-b${JAVA_BUILD}/${JAVA_STR}/${JAVA_TYPE}-${JAVA_VERSION}u${JAVA_UPDATE}-linux-x64.tar.gz" \
    && tar -xzf "${JAVA_TYPE}-${JAVA_VERSION}u${JAVA_UPDATE}-linux-x64.tar.gz" \
    && ln -s "${CMDY_DIR}/jdk1.${JAVA_VERSION}.0_${JAVA_UPDATE}"  "${CMDY_DIR}/server-jre-${JAVA_VERSION}" \
    #tomcat8
    && mkdir -p ${CATALINA_HOME} \
    && wget -O tomcat.tar.gz ${TOMCAT_TGZ_URL} \
    && tar -xvf tomcat.tar.gz -C ${CATALINA_HOME} --strip-components=1 \
    && nativeBuildDir=$(mktemp -d) \
    && tar -xvf ${CATALINA_HOME}/bin/tomcat-native.tar.gz -C ${nativeBuildDir} --strip-components=1 \
    && ( cd ${nativeBuildDir}/native \
    && gnuArch=$(dpkg-architecture --query DEB_BUILD_GNU_TYPE) \
    && ./configure \
        --build=${gnuArch} \
        --libdir=${TOMCAT_NATIVE_LIBDIR} \
        --prefix=${CATALINA_HOME} \
        --with-apr=$(which apr-1-config) \
        --with-java-home=${JAVA_HOME} \
        --with-ssl=yes \
    && make -j $(nproc) \
    && make install) \
    && runDeps=$( \
        scanelf --needed --nobanner --recursive ${TOMCAT_NATIVE_LIBDIR} \
            | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
            | sort -u \
            | xargs -r apk info --installed \
            | sort -u \
        ) \
    && apk add --virtual .tomcat-native-rundeps ${runDeps} \
    && nativeLines=$(catalina.sh configtest 2>&1) \
    && nativeLines=$(echo ${nativeLines} | grep 'Apache Tomcat Native') \
    && nativeLines=$(echo ${nativeLines} | sort -u) \
    && if ! echo ${nativeLines} | grep 'INFO: Loaded APR based Apache Tomcat Native library' >&2; then \
           echo >&2 ${nativeLines}; \
           exit 1; \
       fi \
    && rm -rf   ${CATALINA_HOME}/bin/*.bat \
                ${CATALINA_HOME}/bin/tomcat-native.tar.gz \
                ${CATALINA_HOME}/webapps/* \
                tomcat.tar.gz \
    #jre-8
    && rm -rf ${CMDY_DIR} \
    && export JAVA_TYPE=jre \
    && mkdir -p "${CMDY_DIR}" \
    && cd "${CMDY_DIR}"  \
    && wget --header "Cookie: oraclelicense=accept-securebackup-cookie;" \
       "http://download.oracle.com/otn-pub/java/jdk/${JAVA_VERSION}u${JAVA_UPDATE}-b${JAVA_BUILD}/${JAVA_STR}/${JAVA_TYPE}-${JAVA_VERSION}u${JAVA_UPDATE}-linux-x64.tar.gz" \
    && tar -xzf "${JAVA_TYPE}-${JAVA_VERSION}u${JAVA_UPDATE}-linux-x64.tar.gz" \
    && ln -s "${CMDY_DIR}/jre1.${JAVA_VERSION}.0_${JAVA_UPDATE}"  "${CMDY_DIR}/jre-${JAVA_VERSION}" \
    && apk del .build-deps \
    && rm -f "${JAVA_TYPE}-${JAVA_VERSION}u${JAVA_UPDATE}-linux-x64.tar.gz" \
    && cd "${CMDY_DIR}/${JAVA_TYPE}-${JAVA_VERSION}" \
    && rm -Rf \
       COPYRIGHT \
       LICENSE \
       README.html \
       THIRDPARTYLICENSEREADME-JAVAFX.txt \
       THIRDPARTYLICENSEREADME.txt \
       release \
       src.zip \
       man/
    
ENV PATH="${CMDY_DIR}/${JAVA_TYPE}-${JAVA_VERSION}/bin:${PATH}"    

EXPOSE 8080

CMD ["catalina.sh", "run"] 
