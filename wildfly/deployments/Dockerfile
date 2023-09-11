FROM ubuntu:latest
LABEL authors="barto"

# Login as root
USER root

ENTRYPOINT ["top", "-b"]

# Add JDK
ENV JDK_DOWNLOAD=https://github.com/adoptium/temurin17-binaries/releases/download/jdk-17.0.3%2B7/
ENV JDK_TAR_FILE=OpenJDK17U-jdk_x64_linux_hotspot_17.0.3_7.tar.gz
ENV JDK_SHA256=81f5bed21077f9fbb04909b50391620c78b9a3c376593c0992934719c0de6b73
ENV JDK_HOME=/opt/JDK
RUN cd $HOME \
    && curl -O $JDK_DOWNLOAD$JDK_TAR_FILE -L \
    && sha256sum $JDK_TAR_FILE | grep $JDK_SHA256 \
    && tar xf $JDK_TAR_FILE \
    && rm $JDK_TAR_FILE \
    && mv jdk* $JDK_HOME \
    && $JDK_HOME/bin/keytool -cacerts -storepass changeit -importcert -file $CCAROOTCA_CRT_LOC -noprompt
ENV PATH=$JDK_HOME/bin:${PATH}

# Add WildFly
ENV WILDFLY_VERSION=26.1.1.Final
ENV WILDFLY_SHA1=c11076dd0ea3bb554c5336eeafdfcee18d94551d
ENV WILDFLY_HOME=/opt/wildfly
ENV WILDFLY_USER=admin
ENV WILDFLY_PASS=adminadmin
RUN cd $HOME \
    && curl -O https://github.com/wildfly/wildfly/releases/download/$WILDFLY_VERSION/wildfly-$WILDFLY_VERSION.tar.gz -L \
    && sha1sum wildfly-$WILDFLY_VERSION.tar.gz | grep $WILDFLY_SHA1 \
    && tar xf wildfly-$WILDFLY_VERSION.tar.gz \
    && mv $HOME/wildfly-$WILDFLY_VERSION $WILDFLY_HOME \
    && rm wildfly-$WILDFLY_VERSION.tar.gz \
    && $WILDFLY_HOME/bin/add-user.sh -u $WILDFLY_USER -p $WILDFLY_PASS \
    && useradd -m wildfly \
    && chown -R wildfly:0 ${WILDFLY_HOME} \
    && chmod -R g+rw ${WILDFLY_HOME}

# Login as wildfly
USER wildfly

# Expose the ports (must be run with -p or -P anyway), 8080/HTTP, 8787/Debugger
EXPOSE 8080 8787 9990