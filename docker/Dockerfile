
FROM conda/miniconda2@sha256:d30ea858b9d3425127c2e835d39653cdca74368af832d65748cf912f6c1da67b

MAINTAINER loipf

RUN apt-get update && apt install -y wget unzip gzip procps gcc
#RUN apt install -y build-essential aptitude libxml2-dev libssl-dev libcurl4-openssl-dev libxtst6 libgl1-mesa-glx

RUN conda config --add channels defaults
RUN conda config --add channels bioconda
RUN conda config --add channels conda-forge

RUN conda install r-base=3.6.3 
#RUN conda install r-essentials libiconv mesa-libgl-cos6-x86_64 libxml2 xorg-libxtst

RUN apt install -y git-all

RUN R -e 'install.packages("pacman",repos = "http://cran.us.r-project.org")'
RUN R -e 'pacman::p_load(data.table, magrittr, glue)'

RUN curl https://sh.rustup.rs -sSf | sh -s -- -y
RUN /root/.cargo/bin/rustup install 1.53.0
RUN /root/.cargo/bin/rustup override set 1.53.0


