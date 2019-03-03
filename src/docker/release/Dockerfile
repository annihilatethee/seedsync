FROM ubuntu:16.04


# Install dependencies
RUN apt-get update && apt-get install -y \
    libssl-dev \
    lftp \
    openssh-client \
    rar \
    unrar \
    p7zip \
    python3-pip

# Disable the known hosts prompt
RUN mkdir -p /root/.ssh && echo "StrictHostKeyChecking no\nUserKnownHostsFile /dev/null" > /root/.ssh/config

RUN mkdir /app
COPY /src/python /app/python
COPY /src/html /app/html
COPY /src/scanfs /app/scanfs
ADD /src/docker/release/setup_default_config.sh /scripts/

RUN pip3 install --upgrade pip
RUN pip3 install -r /app/python/requirements.txt

# Create non-root user and directories under that user
RUN groupadd -g 1547 annihilatethee && \
    useradd -r -u 1547 -g annihilatethee annihilatethee
RUN mkdir /config && \
    mkdir /downloads && \
    chown 1547:1547 /config && \
    chown 1547:1547 /downloads && \
	chmod -R 777 /config && \
	chmod -R 777 /downloads

# Switch to non-root user
USER annihilatethee

# First time config setup and replacement
RUN /scripts/setup_default_config.sh

# Must run app inside shell
# Otherwise the container crashes as soon as a child process exits
CMD [ \
    "python3.5", \
    "/app/python/seedsync.py", \
    "-c", "/config", \
    "--html", "/app/html", \
    "--scanfs", "/app/scanfs" \
]

EXPOSE 8800
