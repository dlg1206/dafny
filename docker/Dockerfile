FROM alpine/curl:8.14.1 AS bin_download
ARG DAFNY_VERSION=4.11.0
ENV DAFNY_SRC="https://github.com/dafny-lang/dafny/releases/download/v$DAFNY_VERSION/dafny-$DAFNY_VERSION-x64-ubuntu-22.04.zip"
ENV MICROSOFT_REGISTRY="https://packages.microsoft.com/config/ubuntu/22.04/packages-microsoft-prod.deb"
WORKDIR /tmp
RUN curl -Lo packages-microsoft-prod.deb $MICROSOFT_REGISTRY
RUN curl -Lo dafny.zip $DAFNY_SRC && unzip dafny.zip

# base image supports python and c#
FROM ubuntu:22.04 AS runtime
ENV DOTNET_CLI_TELEMETRY_OPTOUT=1
ARG DAFNY_VERSION=4.11.0
# install dotnet 8
WORKDIR /tmp
RUN apt update && apt install -y apt-transport-https software-properties-common
COPY --from=bin_download /tmp/packages-microsoft-prod.deb .
RUN dpkg -i packages-microsoft-prod.deb \
    && rm packages-microsoft-prod.deb \
    && apt update && apt install -y dotnet-sdk-8.0 \
    && rm -rf /var/lib/apt/lists/*
# setup dafny cli
COPY --from=bin_download /tmp/dafny /opt/dafny
RUN ln -s /opt/dafny/dafny /usr/local/bin/dafny
# launch dafny cli
WORKDIR /dafny
ENTRYPOINT ["dafny"]
CMD ["-h"]