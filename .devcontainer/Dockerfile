# Use the official Python image with a specific version as the base image
ARG VARIANT=3.9
FROM python:${VARIANT}

# Avoid warnings by switching to noninteractive
ENV DEBIAN_FRONTEND=noninteractive

# This Dockerfile adds a non-root 'vscode' user with sudo access. However, for Linux,
# this user's GID/UID should match your local user UID/GID to avoid permission issues
# with bind mounts. Update USER_UID / USER_GID if it's not `1000`.
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Install necessary packages
RUN apt-get update && apt-get install -y \
    sudo \
    wget \
    && apt-get clean -y && rm -rf /var/lib/apt/lists/*

# Install Google Chrome
RUN wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb \
    && apt install ./google-chrome-stable_current_amd64.deb -y \
    && rm ./google-chrome-stable_current_amd64.deb

# Create the vscode user
RUN groupadd --gid $USER_GID vscode \
    && useradd --uid $USER_UID --gid $USER_GID -m vscode \
    && echo vscode ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/vscode \
    && chmod 0440 /etc/sudoers.d/vscode

# Clean up
RUN apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists/* /tmp/library-scripts

# Use the official image as a parent image.
FROM python:3.9-slim

# Set the working directory.
WORKDIR /usr/src/app

# Copy the file from your host to your current location.
COPY . .

# Run the command inside your image filesystem.
RUN pip install --no-cache-dir -r requirements.txt

# Inform Docker that the container is listening on the specified port at runtime.
EXPOSE 8000

# Run the specified command within the container.
CMD [ "python", "./your-daemon-or-script.py" ]

# Switch back to dialog for any ad-hoc use of apt-get
ENV DEBIAN_FRONTEND=dialog

# Switch to the vscode user
USER vscode

# Set the default shell to bash rather than sh
SHELL ["/bin/bash", "-c"]
