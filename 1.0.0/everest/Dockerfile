FROM alpine:3.9.4

RUN apk update \
  && apk add \
  openssh-client \
  ca-certificates \
  bash \
  git;

ENV JAVA_HOME /opt/openjdk-13
ENV PATH $JAVA_HOME/bin:$PATH

# https://jdk.java.net/
ENV JAVA_VERSION 13-ea+19
ENV JAVA_URL https://download.java.net/java/early_access/alpine/19/binaries/openjdk-13-ea+19_linux-x64-musl_bin.tar.gz
ENV JAVA_SHA256 010ea985fba7e3d89a9170545c4e697da983cffc442b84e65dba3baa771299a5
# "For Alpine Linux, builds are produced on a reduced schedule and may not be in sync with the other platforms."

RUN set -eux; \
	\
	wget -O /openjdk.tgz "$JAVA_URL"; \
	echo "$JAVA_SHA256 */openjdk.tgz" | sha256sum -c -; \
	mkdir -p "$JAVA_HOME"; \
	tar --extract --file /openjdk.tgz --directory "$JAVA_HOME" --strip-components 1; \
	rm /openjdk.tgz; \
	\
# https://github.com/docker-library/openjdk/issues/212#issuecomment-420979840
# https://openjdk.java.net/jeps/341
	java -Xshare:dump; \
	\
# basic smoke test
	java --version; \
	javac --version

ENV M2_HOME /opt/apache-maven-3.6.1
ENV MAVEN_HOME $M2_HOME
ENV PATH $M2_HOME/bin:$PATH
ENV MAVEN_VERSION 3.6.1
ENV M2_VERSION $MAVEN_VERSION

ENV MAVEN_URL https://www-us.apache.org/dist/maven/maven-3/3.6.1/binaries/apache-maven-3.6.1-bin.tar.gz

RUN set -eux; \
	\
	wget -O /maven.tgz "$MAVEN_URL"; \
	mkdir -p "$M2_HOME"; \
	tar --extract --file /maven.tgz --directory "$M2_HOME" --strip-components 1; \
	rm /maven.tgz; \
	ls -ltr $M2_HOME/bin; \
	mvn --version;

CMD ["/bin/sh"]