#############################################################################
# Base image is set to Alpine:3.9.4
#############################################################################
FROM alpine:3.9.4

#############################################################################
# Install bash and git commands
#############################################################################
ENV LANG='en_US.UTF-8' LANGUAGE='en_US:en' LC_ALL='en_US.UTF-8'

RUN apk update \
	&& apk add \
	openssh-client \
	ca-certificates \
	bash \
	git;

RUN apk add --no-cache --virtual .build-deps curl binutils \
    && GLIBC_VER="2.29-r0" \
    && ALPINE_GLIBC_REPO="https://github.com/sgerrand/alpine-pkg-glibc/releases/download" \
    && GCC_LIBS_URL="https://archive.archlinux.org/packages/g/gcc-libs/gcc-libs-8.2.1%2B20180831-1-x86_64.pkg.tar.xz" \
    && GCC_LIBS_SHA256=e4b39fb1f5957c5aab5c2ce0c46e03d30426f3b94b9992b009d417ff2d56af4d \
    && ZLIB_URL="https://archive.archlinux.org/packages/z/zlib/zlib-1%3A1.2.11-3-x86_64.pkg.tar.xz" \
    && ZLIB_SHA256=17aede0b9f8baa789c5aa3f358fbf8c68a5f1228c5e6cba1a5dd34102ef4d4e5 \
    && curl -LfsS https://alpine-pkgs.sgerrand.com/sgerrand.rsa.pub -o /etc/apk/keys/sgerrand.rsa.pub \
    && SGERRAND_RSA_SHA256="823b54589c93b02497f1ba4dc622eaef9c813e6b0f0ebbb2f771e32adf9f4ef2" \
    && echo "${SGERRAND_RSA_SHA256} */etc/apk/keys/sgerrand.rsa.pub" | sha256sum -c - \
    && curl -LfsS ${ALPINE_GLIBC_REPO}/${GLIBC_VER}/glibc-${GLIBC_VER}.apk > /tmp/glibc-${GLIBC_VER}.apk \
    && apk add /tmp/glibc-${GLIBC_VER}.apk \
    && curl -LfsS ${ALPINE_GLIBC_REPO}/${GLIBC_VER}/glibc-bin-${GLIBC_VER}.apk > /tmp/glibc-bin-${GLIBC_VER}.apk \
    && apk add /tmp/glibc-bin-${GLIBC_VER}.apk \
    && curl -Ls ${ALPINE_GLIBC_REPO}/${GLIBC_VER}/glibc-i18n-${GLIBC_VER}.apk > /tmp/glibc-i18n-${GLIBC_VER}.apk \
    && apk add /tmp/glibc-i18n-${GLIBC_VER}.apk \
    && /usr/glibc-compat/bin/localedef --force --inputfile POSIX --charmap UTF-8 "$LANG" || true \
    && echo "export LANG=$LANG" > /etc/profile.d/locale.sh \
    && curl -LfsS ${GCC_LIBS_URL} -o /tmp/gcc-libs.tar.xz \
    && echo "${GCC_LIBS_SHA256} */tmp/gcc-libs.tar.xz" | sha256sum -c - \
    && mkdir /tmp/gcc \
    && tar -xf /tmp/gcc-libs.tar.xz -C /tmp/gcc \
    && mv /tmp/gcc/usr/lib/libgcc* /tmp/gcc/usr/lib/libstdc++* /usr/glibc-compat/lib \
    && strip /usr/glibc-compat/lib/libgcc_s.so.* /usr/glibc-compat/lib/libstdc++.so* \
    && curl -LfsS ${ZLIB_URL} -o /tmp/libz.tar.xz \
    && echo "${ZLIB_SHA256} */tmp/libz.tar.xz" | sha256sum -c - \
    && mkdir /tmp/libz \
    && tar -xf /tmp/libz.tar.xz -C /tmp/libz \
    && mv /tmp/libz/usr/lib/libz.so* /usr/glibc-compat/lib \
    && apk del --purge .build-deps glibc-i18n \
    && rm -rf /tmp/*.apk /tmp/gcc /tmp/gcc-libs.tar.xz /tmp/libz /tmp/libz.tar.xz /var/cache/apk/*

#############################################################################
# Set Java version and URL of AdoptOpenJDK12 (built on Open JDK 12)
#############################################################################
ENV JAVA_VERSION jdk12u
ENV JAVA_SHA256 59868804a7fd478eca6a4b131338d67a6e0e7cf2fef364987c2821bd29e682e9
ENV JAVA_URL https://github.com/AdoptOpenJDK/openjdk12-binaries/releases/download/jdk12u-2019-06-12-22-41/OpenJDK12U-jdk_x64_linux_hotspot_2019-06-12-22-41.tar.gz

#############################################################################
# # Download and extract AdoptOpenJDK12 (built on Open JDK 12)
#############################################################################
RUN set -eux; \
    apk add --virtual .fetch-deps curl; \
    curl -LfsSo /tmp/openjdk.tar.gz ${JAVA_URL}; \
    echo "${JAVA_SHA256} */tmp/openjdk.tar.gz" | sha256sum -c -; \
    mkdir -p /opt/java/openjdk; \
    cd /opt/java/openjdk; \
    tar -xf /tmp/openjdk.tar.gz --strip-components=1; \
    apk del --purge .fetch-deps; \
    rm -rf /var/cache/apk/*; \
    rm -rf /tmp/openjdk.tar.gz;

ENV JAVA_HOME=/opt/java/openjdk \
    PATH="/opt/java/openjdk/bin:$PATH"

#############################################################################
# Set Ant version and URL of Ant download
#############################################################################
ENV ANT_HOME /opt/apache-ant-1.10.6
ENV PATH $ANT_HOME/bin:$PATH
ENV ANT_VERSION 1.10.6
ENV ANT_URL https://www-eu.apache.org/dist//ant/binaries/apache-ant-1.10.6-bin.tar.gz

#############################################################################
# Download and extract apache-ant-1.10.6
#############################################################################
RUN set -o errexit -o nounset; \
	wget --no-verbose -O /ant.tgz "$ANT_URL"; \
	mkdir -p "$ANT_HOME"; \
	tar --extract --file /ant.tgz --directory "$ANT_HOME" --strip-components 1; \
	rm /ant.tgz;

#############################################################################
# Set Maven version and URL of Maven download
#############################################################################
ENV M2_HOME /opt/apache-maven-3.6.1
ENV MAVEN_HOME $M2_HOME
ENV PATH $M2_HOME/bin:$PATH
ENV MAVEN_VERSION 3.6.1
ENV M2_VERSION $MAVEN_VERSION
ENV MAVEN_URL https://www-us.apache.org/dist/maven/maven-3/3.6.1/binaries/apache-maven-3.6.1-bin.tar.gz

#############################################################################
# Download and extract apache-maven-3.6.1
#############################################################################
RUN set -o errexit -o nounset; \
	wget --no-verbose -O /maven.tgz "$MAVEN_URL"; \
	mkdir -p "$M2_HOME"; \
	tar --extract --file /maven.tgz --directory "$M2_HOME" --strip-components 1; \
	rm /maven.tgz;

#############################################################################
# Set env for Gradle
#############################################################################
ENV GRADLE_HOME /opt/gradle-5.4.1
ENV PATH $GRADLE_HOME/bin:$PATH
ENV GRADLE_VERSION 5.4.1
ENV GRADLE_URL https://services.gradle.org/distributions/gradle-5.4.1-bin.zip
ARG GRADLE_DOWNLOAD_SHA256=7bdbad1e4f54f13c8a78abc00c26d44dd8709d4aedb704d913fb1bb78ac025dc

#############################################################################
# Download and extract gradle-5.4.1
#############################################################################
RUN set -o errexit -o nounset; \
	echo "Downloading Gradle"; \
	wget --no-verbose -O /gradle.zip https://services.gradle.org/distributions/gradle-5.4.1-bin.zip; \
	echo "Checking download hash"; \
	echo "$GRADLE_DOWNLOAD_SHA256 */gradle.zip" | sha256sum -c -; \
	mkdir -p /opt/gradle; \
	unzip /gradle.zip -d /opt/gradle; \
	rm /gradle.zip; \
	mv /opt/gradle/gradle-5.4.1 /opt; \
	rm -R /opt/gradle;

#############################################################################
# Testing installation
#############################################################################
RUN set -o errexit -o nounset; \
	echo "Testing installation"; \
	java --version; \
	javac --version; \
	ant -version; \
	mvn --version; \
	gradle -v;

#############################################################################
# Set sh prompt
#############################################################################
CMD ["/bin/sh"]
