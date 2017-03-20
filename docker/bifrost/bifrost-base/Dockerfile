FROM intelonp/centos-source-openstack-base:2.4.20170320
MAINTAINER Intel Corp. (http://www.intel.com)








RUN usermod --append --home /var/lib/bifrost --groups kolla bifrost \
    && mkdir -p /var/lib/bifrost \
    && chown -R 42404:42404 /var/lib/bifrost




ENV VENV /var/lib/kolla/venv

RUN mkdir -p /bifrost-base-source && git clone git://git.openstack.org/openstack/bifrost /bifrost-base-source/bifrost-base && pushd /bifrost-base-source/bifrost-base && git checkout 6c7a80e2307d4cad3120716bdd5132e868907b36 && popd

COPY build_arg.yml /tmp/build_arg.yml
RUN ln -s bifrost-base-source/* bifrost \
    && /var/lib/kolla/venv/bin/pip --no-cache-dir install --upgrade -c requirements/upper-constraints.txt /bifrost

WORKDIR /bifrost



RUN bash -c './scripts/env-setup.sh && source ./env-vars && \
    ansible-playbook -vvvv -i /bifrost/playbooks/inventory/localhost /bifrost/playbooks/install.yaml \
    -e @/tmp/build_arg.yml && \

    yum clean all'



COPY bifrost_sudoers /etc/sudoers.d/kolla_bifrost_sudoers

RUN chmod 750 /etc/sudoers.d \
    && chmod 440 /etc/sudoers.d/kolla_bifrost_sudoers \
    && chown -R bifrost:bifrost /bifrost