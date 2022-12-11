FROM kalos/ansible-builder:latest

# runtime dependencies
RUN set -eux; \
	apt-get update; \
	apt-get install -y --no-install-recommends \
		openssh-client \
		dumb-init \
	; \
	rm -rf /var/lib/apt/lists/*


COPY python_requirements.txt /tmp/
#RUN pip install --upgrade pip ansible-builder
#RUN pip install --upgrade pip
RUN pip install -r /tmp/python_requirements.txt

# In OpenShift, container will run as a random uid number and gid 0. Make sure things
# are writeable by the root group.
RUN for dir in \
      /home/runner \
      /home/runner/.ansible \
      /home/runner/.ansible/tmp \
      /runner \
      /home/runner \
      /runner/env \
      /runner/inventory \
      /runner/project \
      /runner/artifacts ; \
    do mkdir -m 0775 -p $dir ; chmod -R g+rwx $dir ; chgrp -R root $dir ; done && \
    for file in \
      /home/runner/.ansible/galaxy_token \
      /etc/passwd \
      /etc/group ; \
    do touch $file ; chmod g+rw $file ; chgrp root $file ; done

WORKDIR /runner


# NB: this appears to be necessary for container builds based on this container, since we can't rely on the entrypoint
# script to run during a build to fix up /etc/passwd. This envvar value, and the fact that all user homedirs are
# set to /home/runner is an implementation detail that may change with future versions of runner and should not be
# assumed by other code or tools.
ENV HOME=/home/runner

ADD entrypoint.sh /bin/entrypoint
RUN chmod +x /bin/entrypoint

# debug
#RUN set -eux; \
#        apt-get update; \
#        apt-get install -y --no-install-recommends \
#                sudo \
#        ; \
#        rm -rf /var/lib/apt/lists/*
#
#RUN echo "%root ALL=(ALL:ALL) NOPASSWD: ALL" | tee /etc/sudoers.d/nopass


ENTRYPOINT ["entrypoint"]
CMD ["ansible-runner", "run", "/runner"]
