#-------------------------------------------------------------------------------------------------------------
# Flutter Dev Container - Lucas Hilleshein dos Santos.
# Licensed under the MIT License.
# See https://github.com/lucashilles/flutter-dev-container/blob/master/LICENSE for license information.
#-------------------------------------------------------------------------------------------------------------

FROM ubuntu:focal

#Locale
ENV LANG C.UTF-8

#
# Android SDK
# https://developer.android.com/studio#command-tools
ENV ANDROID_SDK_TOOLS_VERSION=8512546
ENV ANDROID_TOOLS_URL=https://dl.google.com/android/repository/commandlinetools-linux-${ANDROID_SDK_TOOLS_VERSION}_latest.zip
ENV ANDROID_PLATFORM_VERSION=33
ENV ANDROID_BUILD_TOOLS_VERSION=33.0.0
ENV ANDROID_HOME=/opt/android-sdk-linux
ENV ANDROID_SDK_ROOT="$ANDROID_HOME"
ENV PATH=${PATH}:${ANDROID_HOME}/cmdline-tools/${ANDROID_BUILD_TOOLS_VERSION}/bin:${ANDROID_HOME}/platform-tools:${ANDROID_HOME}/emulator

#
# Flutter SDK
# https://flutter.dev/docs/development/tools/sdk/releases?tab=linux
ENV FLUTTER_CHANNEL="stable"
ENV FLUTTER_VERSION="3.3.2"
ENV FLUTTER_URL=https://storage.googleapis.com/flutter_infra_release/releases/${FLUTTER_CHANNEL}/linux/flutter_linux_${FLUTTER_VERSION}-${FLUTTER_CHANNEL}.tar.xz
# Set this variable as "enable" to auto config flutter web-server.
# Make sure to use the needed channel and version for this.
ENV FLUTTER_WEB="enable"
ENV FLUTTER_HOME=/opt/flutter
ENV PATH=${PATH}:${FLUTTER_HOME}/bin

# This Dockerfile adds a non-root user with sudo access. Use the "remoteUser"
# property in devcontainer.json to use it. On Linux, the container user's GID/UIDs
# will be updated to match your local UID/GID (when using the dockerFile property).
# See https://aka.ms/vscode-remote/containers/non-root-user for details.
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

#
# Install needed packages, setup user anda clean up.
RUN apt-get update \
	&& apt-get install -y sudo \
	&& apt-get install -y openjdk-8-jdk-headless --no-install-recommends \
	&& apt-get install -y wget curl git xz-utils zip unzip --no-install-recommends \
	# Clean Up
	&& apt-get autoremove -y \
	&& apt-get clean -y \
	&& rm -rf /var/lib/apt/lists/* \
	# Create a non-root user to use if preferred - see https://aka.ms/vscode-remote/containers/non-root-user.
	# [Optional] Add sudo support for the non-root user
	&& groupadd --gid $USER_GID $USERNAME \
	&& useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
	&& echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
	&& chmod 0440 /etc/sudoers.d/$USERNAME

#
# Android SDK
RUN cd /opt \
	&& curl -C - --output android-sdk-tools.zip $ANDROID_TOOLS_URL \
	&& mkdir -p ${ANDROID_HOME}/cmdline-tools/ \
	&& unzip -q android-sdk-tools.zip -d ${ANDROID_HOME}/cmdline-tools/ \
	&& mv ${ANDROID_HOME}/cmdline-tools/cmdline-tools ${ANDROID_HOME}/cmdline-tools/${ANDROID_BUILD_TOOLS_VERSION} \
	&& rm android-sdk-tools.zip \
	&& yes | sdkmanager --licenses \
	&& touch $HOME/.android/repositories.cfg \
	&& sdkmanager platform-tools \
	&& sdkmanager emulator \
	&& sdkmanager "platforms;android-$ANDROID_PLATFORM_VERSION" "build-tools;$ANDROID_BUILD_TOOLS_VERSION"

#
# Flutter SDK
RUN cd /opt \
	&& curl -C - --output flutter.tar.xz $FLUTTER_URL \
	&& tar xf flutter.tar.xz -C . \
	&& rm flutter.tar.xz \
	&& git config --global --add safe.directory /opt/flutter \
	&& yes | flutter doctor --android-licenses \
	&& flutter config --no-analytics \
	&& if [ "$FLUTTER_WEB" = "enable" ]; then flutter config --enable-web; fi \
	&& flutter update-packages
