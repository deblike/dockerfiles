# Run slack desktop app in a container
#
# docker run --rm -it \
#	-v /etc/localtime:/etc/localtime:ro \
#	-v /tmp/.X11-unix:/tmp/.X11-unix \
#	-e DISPLAY=unix$DISPLAY \
#	--device /dev/snd \
#	--device /dev/dri \
#	--device /dev/video0 \
#	--group-add audio \
#	--group-add video \
#	-v "${HOME}/.slack:/root/.config/Slack" \
#	--ipc="host" \
#	--name slack \
#	jess/slack "$@"

FROM debian:bullseye-slim
LABEL maintainer "Jessie Frazelle <jess@linux.com>"

ENV LC_ALL en_US.UTF-8
ENV LANG en_US.UTF-8

RUN apt-get update && apt-get install -y \
	apt-transport-https \
	ca-certificates \
	curl \
	gnupg \
	locales \
	--no-install-recommends \
	&& rm -rf /var/lib/apt/lists/*

RUN echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen \
	&& locale-gen en_US.utf8 \
	&& /usr/sbin/update-locale LANG=en_US.UTF-8

# Add Sid repo to install dependencies then remove them
RUN echo "deb http://deb.debian.org/debian/ sid main contrib non-free" > /etc/apt/sources.list.d/sid_temp.list
RUN apt update && apt -y install libappindicator3-1
RUN rm /etc/apt/sources.list.d/sid_temp.list

# Add the slack debian repo
RUN curl -sSL https://packagecloud.io/slacktechnologies/slack/gpgkey | apt-key add -
RUN echo "deb https://packagecloud.io/slacktechnologies/slack/debian/ jessie main" > /etc/apt/sources.list.d/slacktechnologies_slack.list

RUN apt-get update && apt-get -y install --allow-downgrades \
	libasound2 \
    libdrm2 \
    libgbm1 \
	libgtk-3-0 \
	libx11-xcb1 \
    libx11-6=2:1.7.2-1 \
	libxkbfile1 \
	slack-desktop \
	--no-install-recommends \
	&& rm -rf /var/lib/apt/lists/*

#Add a user with userid 8877 and name slack
RUN useradd slack

# Ensures that the docker container always start with this user, unless we
# explicit override it with docker run --user username ....
USER slack

ENTRYPOINT ["/usr/lib/slack/slack"]
