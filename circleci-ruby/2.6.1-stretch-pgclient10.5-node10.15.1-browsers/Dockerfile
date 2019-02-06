FROM circleci/ruby:2.6.1-stretch

USER root


#### PG CLIENT

ENV PG_VERSION 10

RUN \
  echo "deb http://apt.postgresql.org/pub/repos/apt/ stretch-pgdg main" > /etc/apt/sources.list.d/pgdg.list \
  && curl https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add -

RUN apt-get update

RUN apt-get install "postgresql-client-$PG_VERSION"


#### NODE

# Source: https://github.com/CircleCI-Public/circleci-dockerfiles/blob/master/ruby/images/2.5.1-stretch/node/Dockerfile

## Using node installation from https://raw.githubusercontent.com/nodejs/docker-node/72dd945d29dee5afa73956ebc971bf3a472442f7/8/jessie/Dockerfile

RUN groupadd --gid 1000 node \
  && useradd --uid 1000 --gid node --shell /bin/bash --create-home node

# gpg keys listed at https://github.com/nodejs/node#release-team
RUN set -ex \
  && for key in \
  94AE36675C464D64BAFA68DD7434390BDBE9B9C5 \
  FD3A5288F042B6850C66B31F09FE44734EB7990E \
  71DCFD284A79C3B38668286BC97EC7A07EDE3FC1 \
  DD8F2338BAE7501E3DD5AC78C273792F7D83545D \
  C4F0DFFF4E8C1A8236409D08E73BC641CC11F4C8 \
  B9AE9905FFD7803F25714661B63B535A4C206CA9 \
  56730D5401028683275BD23C23EFEFE93C4CFFFE \
  77984A986EBC2AA786BC0F66B01FBB92821C587A \
  8FCCA13FEF1D0C2E91008E09770F7A9A5AE15600 \
  ; do \
  gpg --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys "$key" || \
  gpg --keyserver hkp://ipv4.pool.sks-keyservers.net --recv-keys "$key" || \
  gpg --keyserver hkp://pgp.mit.edu:80 --recv-keys "$key" ; \
  done

ENV NODE_VERSION 10.15.1

RUN ARCH= && dpkgArch="$(dpkg --print-architecture)" \
  && case "${dpkgArch##*-}" in \
  amd64) ARCH='x64';; \
  ppc64el) ARCH='ppc64le';; \
  s390x) ARCH='s390x';; \
  arm64) ARCH='arm64';; \
  armhf) ARCH='armv7l';; \
  i386) ARCH='x86';; \
  *) echo "unsupported architecture"; exit 1 ;; \
  esac \
  && curl -fsSLO --compressed "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-$ARCH.tar.xz" \
  && curl -fsSLO --compressed "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc" \
  && gpg --batch --decrypt --output SHASUMS256.txt SHASUMS256.txt.asc \
  && grep " node-v$NODE_VERSION-linux-$ARCH.tar.xz\$" SHASUMS256.txt | sha256sum -c - \
  && tar -xJf "node-v$NODE_VERSION-linux-$ARCH.tar.xz" -C /usr/local --strip-components=1 --no-same-owner \
  && rm "node-v$NODE_VERSION-linux-$ARCH.tar.xz" SHASUMS256.txt.asc SHASUMS256.txt \
  && ln -s /usr/local/bin/node /usr/local/bin/nodejs

ENV YARN_VERSION 1.13.0

RUN set -ex \
  && for key in \
  6A010C5166006599AA17F08146C2130DFD2497F5 \
  ; do \
  gpg --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys "$key" || \
  gpg --keyserver hkp://ipv4.pool.sks-keyservers.net --recv-keys "$key" || \
  gpg --keyserver hkp://pgp.mit.edu:80 --recv-keys "$key" ; \
  done \
  && curl -fsSLO --compressed "https://yarnpkg.com/downloads/$YARN_VERSION/yarn-v$YARN_VERSION.tar.gz" \
  && curl -fsSLO --compressed "https://yarnpkg.com/downloads/$YARN_VERSION/yarn-v$YARN_VERSION.tar.gz.asc" \
  && gpg --batch --verify yarn-v$YARN_VERSION.tar.gz.asc yarn-v$YARN_VERSION.tar.gz \
  && mkdir -p /opt \
  && tar -xzf yarn-v$YARN_VERSION.tar.gz -C /opt/ \
  && ln -s /opt/yarn-v$YARN_VERSION/bin/yarn /usr/local/bin/yarn \
  && ln -s /opt/yarn-v$YARN_VERSION/bin/yarnpkg /usr/local/bin/yarnpkg \
  && rm yarn-v$YARN_VERSION.tar.gz.asc yarn-v$YARN_VERSION.tar.gz

# Basic smoke test
RUN node --version

USER circleci


#### BROWSERS

# Source: https://github.com/CircleCI-Public/circleci-dockerfiles/blob/master/ruby/images/2.5.1-stretch/node-browsers/Dockerfile

# install java 8
#
RUN if grep -q Debian /etc/os-release && grep -q jessie /etc/os-release; then \
  echo "deb http://http.us.debian.org/debian/ jessie-backports main" | sudo tee -a /etc/apt/sources.list \
  && echo "deb-src http://http.us.debian.org/debian/ jessie-backports main" | sudo tee -a /etc/apt/sources.list \
  && sudo apt-get update; sudo apt-get install -y -t jessie-backports openjdk-8-jre openjdk-8-jre-headless openjdk-8-jdk openjdk-8-jdk-headless \
  ; elif grep -q Ubuntu /etc/os-release && grep -q Trusty /etc/os-release; then \
  echo "deb http://ppa.launchpad.net/openjdk-r/ppa/ubuntu trusty main" | sudo tee -a /etc/apt/sources.list \
  && echo "deb-src http://ppa.launchpad.net/openjdk-r/ppa/ubuntu trusty main" | sudo tee -a /etc/apt/sources.list \
  && sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-key DA1A4A13543B466853BAF164EB9B1D8886F44E2A \
  && sudo apt-get update; sudo apt-get install -y openjdk-8-jre openjdk-8-jre-headless openjdk-8-jdk openjdk-8-jdk-headless \
  ; else \
  sudo apt-get update; sudo apt-get install -y openjdk-8-jre openjdk-8-jre-headless openjdk-8-jdk openjdk-8-jdk-headless \
  ; fi

## install phantomjs
#
RUN PHANTOMJS_URL="https://circle-downloads.s3.amazonaws.com/circleci-images/cache/linux-amd64/phantomjs-latest.tar.bz2" \
  && sudo apt-get install libfontconfig \
  && curl --silent --show-error --location --fail --retry 3 --output /tmp/phantomjs.tar.bz2 ${PHANTOMJS_URL} \
  && tar -x -C /tmp -f /tmp/phantomjs.tar.bz2 \
  && sudo mv /tmp/phantomjs-*-linux-x86_64/bin/phantomjs /usr/local/bin \
  && rm -rf /tmp/phantomjs.tar.bz2 /tmp/phantomjs-* \
  && phantomjs --version

# install firefox

# If you are upgrading to any version newer than 47.0.1, you must check the compatibility with
# selenium. See https://github.com/SeleniumHQ/selenium/issues/2559#issuecomment-237079591

RUN FIREFOX_URL="https://s3.amazonaws.com/circle-downloads/firefox-mozilla-build_47.0.1-0ubuntu1_amd64.deb" \
  && curl --silent --show-error --location --fail --retry 3 --output /tmp/firefox.deb $FIREFOX_URL \
  && echo 'ef016febe5ec4eaf7d455a34579834bcde7703cb0818c80044f4d148df8473bb  /tmp/firefox.deb' | sha256sum -c \
  && sudo dpkg -i /tmp/firefox.deb || sudo apt-get -f install  \
  && sudo apt-get install -y libgtk3.0-cil-dev libasound2 libasound2 libdbus-glib-1-2 libdbus-1-3 \
  && rm -rf /tmp/firefox.deb \
  && firefox --version

# install geckodriver—disabling this temporarily, we will likely want this code in the future, but until we're ready to upgrade our version of firefox to 53+, geckodriver wont' be compatible...

# RUN export GECKODRIVER_LATEST_RELEASE_URL=$(curl https://api.github.com/repos/mozilla/geckodriver/releases/latest | jq -r ".assets[] | select(.name | test(\"linux64\")) | .browser_download_url") \
#      && curl --silent --show-error --location --fail --retry 3 --output /tmp/geckodriver_linux64.tar.gz "$GECKODRIVER_LATEST_RELEASE_URL" \
#      && cd /tmp \
#      && tar xf geckodriver_linux64.tar.gz \
#      && rm -rf geckodriver_linux64.tar.gz \
#      && sudo mv geckodriver /usr/local/bin/geckodriver \
#      && sudo chmod +x /usr/local/bin/geckodriver \
#      && geckodriver --version

# install chrome

RUN curl --silent --show-error --location --fail --retry 3 --output /tmp/google-chrome-stable_current_amd64.deb https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb \
  && (sudo dpkg -i /tmp/google-chrome-stable_current_amd64.deb || sudo apt-get -fy install)  \
  && rm -rf /tmp/google-chrome-stable_current_amd64.deb \
  && sudo sed -i 's|HERE/chrome"|HERE/chrome" --disable-setuid-sandbox --no-sandbox|g' \
  "/opt/google/chrome/google-chrome" \
  && google-chrome --version

RUN export CHROMEDRIVER_RELEASE=$(curl --location --fail --retry 3 http://chromedriver.storage.googleapis.com/LATEST_RELEASE) \
  && curl --silent --show-error --location --fail --retry 3 --output /tmp/chromedriver_linux64.zip "http://chromedriver.storage.googleapis.com/$CHROMEDRIVER_RELEASE/chromedriver_linux64.zip" \
  && cd /tmp \
  && unzip chromedriver_linux64.zip \
  && rm -rf chromedriver_linux64.zip \
  && sudo mv chromedriver /usr/local/bin/chromedriver \
  && sudo chmod +x /usr/local/bin/chromedriver \
  && chromedriver --version

# install libgconf-2-4 manually since chrome no longer pulls it in automatically
RUN sudo apt-get install -y libgconf-2-4

# start xvfb automatically to avoid needing to express in circle.yml
ENV DISPLAY :99
RUN printf '#!/bin/sh\nXvfb :99 -screen 0 1280x1024x24 &\nexec "$@"\n' > /tmp/entrypoint \
  && chmod +x /tmp/entrypoint \
  && sudo mv /tmp/entrypoint /docker-entrypoint.sh

# ensure that the build agent doesn't override the entrypoint
LABEL com.circleci.preserve-entrypoint=true

ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["/bin/sh"]
