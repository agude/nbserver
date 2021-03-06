FROM lab41/base-notebook

MAINTAINER Lab41

USER root

# Update apt-get
RUN apt-get update && apt-get install -yq --no-install-recommends \
    apt-transport-https \
    ca-certificates \
    curl \ 
    rsyslog \
    socat \
    sudo \
    vim \
    && apt-get clean && \
    rm -rf /var/lib/apt/lists/*

#### ======= Ganymede logging stuff below ======

# Install Ganymede logging Extension + its dependencies
RUN git clone https://github.com/Lab41/ganymede_nbextension /srv/ganymede_nbextension && \
    pip install -r /srv/ganymede_nbextension/requirements.txt /srv/ganymede_nbextension/. && \
    rm -rf /srv/ganymede_nbextension

# Copy startup script: ganymede_nbserver.sh
# Copy requirements.txt file to install logstash-python
COPY ganymede/* /srv/ganymede_nbserver/
RUN chmod -R a+rx /srv/ganymede_nbserver/ && \
    pip install -r /srv/ganymede_nbserver/requirements.txt

#### ======= nbgallery integration stuff below ======

RUN git clone https://github.com/nbgallery/nbgallery-extensions.git /srv/nbgallery_extension && \
    pip install /srv/nbgallery_extension/. && \
    rm -rf /srv/nbgallery_extension

#### ======= Nopleats logging stuff below ======

# Install Mustache templater for replacing variables (maybe switch to Make?)
RUN cd /opt/ && \
    git clone https://github.com/Lab41/mo.git && \
    ln -s /opt/mo/mo /usr/bin/mo

# Install filebeat
RUN cd /tmp && \
    curl -Ls -O https://download.elastic.co/beats/filebeat/filebeat_1.2.0_amd64.deb && \
    dpkg -i filebeat_1.2.0_amd64.deb && \
    rm filebeat_1.2* && \
    mkdir -p /opt/nopleats && \
    mkdir -p /opt/super && \
    mkdir -p /opt/user

# Copy in the helper files
COPY nopleats/* /opt/nopleats/

# Make it easy for the user to conda install libraries
RUN chmod -R 777 /opt/conda

# Execute the notebook server via bash script.
CMD ["/bin/bash", "/srv/ganymede_nbserver/ganymede_nbserver.sh"]
