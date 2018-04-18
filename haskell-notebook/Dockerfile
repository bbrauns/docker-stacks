# Credits: https://github.com/glor/haskell-datascience-notebook

FROM jupyter/datascience-notebook

USER root

RUN apt-get update && \
    apt-get -y --no-install-recommends install nano vim joe mc man less \
    curl nano vim joe mc man less \
    python3-pip git libtinfo-dev libzmq3-dev libcairo2-dev libpango1.0-dev libmagic-dev libblas-dev liblapack-dev && \
    apt-get clean; apt-get autoclean

# --- haskell ---
RUN mkdir -p /srv/ihaskell && chown $NB_UID:$NB_GID /srv/ihaskell
WORKDIR /srv/ihaskell
RUN curl -sSL https://get.haskellstack.org/ | sh

USER $NB_USER

RUN git clone https://github.com/gibiansky/IHaskell
RUN cd /srv/ihaskell/IHaskell && pip3 install -r requirements.txt
ENV HOME /srv/ihaskell

RUN cd /srv/ihaskell/IHaskell && stack install gtk2hs-buildtools
RUN cd /srv/ihaskell/IHaskell && stack install --fast

# install ihaskell kernel, use kernelspec files in later step
RUN /srv/ihaskell/.local/bin/ihaskell install --stack

ENV HOME /home/$NB_USER
WORKDIR $HOME

# Must be set, otherwise a local ghc will be downloaded and installed on every new container spawn
ENV STACK_ROOT /srv/ihaskell/.stack

# Put kernelspec in datadir which is listed under `:~# jupyter --path`
#
# Do not use `ihaskell install --stack` because it installs the kernelspec in the
# current homedir which is not available at build time -> when the container is started
# a local bind mount to /home/jovyan is used to persist the user data
USER root
RUN mkdir -p /usr/local/share/jupyter/kernels/haskell
RUN cp /srv/ihaskell/.local/share/jupyter/kernels/haskell/kernel.json /usr/local/share/jupyter/kernels/haskell
RUN cp /srv/ihaskell/.local/share/jupyter/kernels/haskell/kernel.js /usr/local/share/jupyter/kernels/haskell
RUN cp /srv/ihaskell/.local/share/jupyter/kernels/haskell/logo-64x64.png /usr/local/share/jupyter/kernels/haskell
RUN chown -R $NB_UID:$NB_GID /usr/local/share/jupyter/kernels/haskell
USER $NB_USER
