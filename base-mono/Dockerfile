FROM ubuntu:focal

# This links to github packages
LABEL org.opencontainers.image.source https://github.com/shanehughes1990/docker-containers

ARG DEBIAN_FRONTEND="noninteractive"

ENV \
	APP_DIR="/app" \
	CONFIG_DIR="/config" \
	PUID="1000" \
	PGID="1000" \
	UMASK="002" \
	TZ="Etc/UTC" \
	DEBUG="no"

# Setup user
RUN \
	mkdir "${CONFIG_DIR}" \
	&& useradd -u 1000 -U -d "${CONFIG_DIR}" -s /bin/false user \
	&& usermod -G users user

# Install & setup dependent packages
RUN \
	apt update \
	&& apt install -y --no-install-recommends --no-install-suggests \
	ca-certificates \
	curl \
	gnupg \
	jq \
	libmediainfo0v5 \
	locales \
	p7zip-full \
	python3 \
	tzdata \
	unrar \
	unzip \
	wget2 \
	# Generate locale
	&& locale-gen en_US.UTF-8 \
	# Cleanup
	&& apt autoremove -y \
	&& apt clean \
	&& rm -rf /tmp/* /var/lib/apt/lists/* /var/tmp/*

# Setup S6-overlay
# https://github.com/just-containers/s6-overlay/releases
ARG S6_VERSION=2.1.0.2

RUN \
	file="/tmp/s6-overlay.tar.gz" \
	&& curl -fsSL -o "${file}" "https://github.com/just-containers/s6-overlay/releases/download/v${S6_VERSION}/s6-overlay-amd64.tar.gz" \
	&& tar xzf "${file}" -C / --exclude="./bin" \
	&& tar xzf "${file}" -C /usr ./bin \
	&& rm "${file}"

# https://download.mono-project.com/repo/ubuntu/dists/focal/snapshots/
ARG MONO_VERSION=6.12

RUN \
	apt update \
	&& apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF \
	&& echo "deb https://download.mono-project.com/repo/ubuntu focal/snapshots/${MONO_VERSION} main" | tee /etc/apt/sources.list.d/mono-official.list \
	&& apt update \
	&& apt install -y --no-install-recommends --no-install-suggests \
	mono-complete=${MONO_VERSION}\* \
	# Cleanup
	&& apt autoremove -y \
	&& apt clean \
	&& rm -rf /tmp/* /var/lib/apt/lists/* /var/tmp/*

VOLUME ["${CONFIG_DIR}"]
ENTRYPOINT ["/init"]
