FROM ubuntu:18.04

ENV LANG='en_US.UTF-8' LANGUAGE='en_US:en' LC_ALL='en_US.UTF-8'

RUN apt-get update \
    && apt-get install -y --no-install-recommends curl ca-certificates fontconfig locales unzip \
    && echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen \
    && locale-gen en_US.UTF-8 \
    && rm -rf /var/lib/apt/lists/*

# JDK preparation start

ARG JDK_URL='https://corretto.aws/downloads/resources/8.242.07.1/amazon-corretto-8.242.07.1-linux-x64.tar.gz'

RUN set -eux; \
    curl -LfsSo /tmp/openjdk.tar.gz ${JDK_URL}; \
    mkdir -p /opt/java/openjdk; \
    cd /opt/java/openjdk; \
    tar -xf /tmp/openjdk.tar.gz --strip-components=1; \
    rm -rf /tmp/openjdk.tar.gz;

ENV JAVA_HOME=/opt/java/openjdk \
    JRE_HOME=/opt/java/openjdk/jre \
    PATH="/opt/java/openjdk/bin:$PATH"

RUN update-alternatives --install /usr/bin/java java ${JRE_HOME}/bin/java 1 && \
    update-alternatives --set java ${JRE_HOME}/bin/java && \
    update-alternatives --install /usr/bin/javac javac ${JRE_HOME}/../bin/javac 1 && \
    update-alternatives --set javac ${JRE_HOME}/../bin/javac

# JDK preparation end
##################################


ENV TEAMCITY_DATA_PATH=/data/teamcity_server/datadir \
    TEAMCITY_DIST=/opt/teamcity \
    TEAMCITY_LOGS=/opt/teamcity/logs \
    TEAMCITY_SERVER_MEM_OPTS="-Xmx2g -XX:ReservedCodeCacheSize=350m" \
    LANG=C.UTF-8

EXPOSE 8111
LABEL dockerImage.teamcity.version="latest" \
      dockerImage.teamcity.buildNumber="latest"

RUN apt-get update && \
    apt-get install -y git mercurial ca-certificates wget && \
    apt-get clean all

COPY welcome.sh /welcome.sh
COPY run-server.sh /run-server.sh
COPY run-services.sh /run-services.sh

RUN chmod +x /welcome.sh /run-server.sh /run-services.sh && sync && \
    groupadd -g 1000 tcuser && \
    useradd -r -u 1000 -g tcuser tcuser && \
    echo '[ ! -z "$TERM" -a -x /welcome.sh -a -x /welcome.sh ] && /welcome.sh' >> /etc/bash.bashrc


RUN wget -q https://download.jetbrains.com/teamcity/TeamCity-2019.2.1.tar.gz
RUN tar -xf TeamCity-2019.2.1.tar.gz -C /opt/
RUN mv /opt/TeamCity $TEAMCITY_DIST


CMD ["/bin/bash"]