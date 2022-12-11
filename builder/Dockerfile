FROM python:3.11.0-slim-bullseye

RUN set -eux; \
        apt-get update; \
        apt-get install -y --no-install-recommends \
                build-essential \
                libssh-dev \
                libyaml-dev \
        ; \
        rm -rf /var/lib/apt/lists/*

RUN pip install --upgrade pip ansible-builder

RUN mkdir /output
ADD install-from-bindep /output
ADD assemble /usr/local/bin
RUN chmod +x /usr/local/bin/assemble /output/install-from-bindep
