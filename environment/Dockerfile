# default R-version install via rocker, uses debian as a base
FROM rocker/verse:4.1.2

# install some likely used basic unix stuff
#
# uuid-runtime: necessary to get uuidgen for secure cookie key file creation of rserver
RUN apt-get update -qq && apt-get -y --no-install-recommends install \
	wget \
	bzip2 \
	nano \
	vim \
	uuid-runtime \
	libcurl4-openssl-dev \
	zlib1g zlib1g-dev \
	libssl-dev \
	libxml2-dev \
	libmpc-dev \
	libpng-dev \
	libicu-dev \
	liblzma-dev \
	libbz2-dev \
	libpcre3-dev \
	openjdk-8-jdk \
	default-jre \
	openmpi-bin \
	libudunits2-dev \
	libgdal-dev \
	libx11-dev \
	mesa-common-dev \
	libglu1-mesa-dev \
	apt-utils \
	unixodbc-dev \
	cargo \
	wget \
        cmake \
        libnlopt-dev \
        python-dev \
        python3-dev \
        python3-pip

# install libgit2 version 1.5.x as requirement for dvc
RUN cd /usr/local && \
    wget https://github.com/libgit2/libgit2/archive/refs/tags/v1.5.0.tar.gz -O libgit2-1.5.0.tar.gz && tar xzf libgit2-1.5.0.tar.gz && \
    cd libgit2-1.5.0/ && \
    cmake . && \
    make && \
    sudo make install

# install dvc
RUN pip3 install dvc

# install python stuff
RUN pip3 install jupyterlab
RUN pip3 install pandas numpy==1.23.5 joblib scikit-learn matplotlib 
RUN pip3 install pyro-ppl 
RUN pip3 install econml

# install some basic packages
RUN install2.r --error \
	--deps TRUE \
	dplyr \	
	ggplot2 \
        data.table \
        DT \
        knitr
 
# install Bioconductor
RUN R -e "install.packages('BiocManager', version = '0.4.8')"	

# needs to be done via BiocManager!
RUN R -e "library(BiocManager)" \
	-e "BiocManager::install(c('tidyverse', 'tidyr'))"

# install other packages
RUN R -e "install.packages('circlize', version = '0.4.8')"	
RUN R -e "BiocManager::install('ComplexHeatmap')"	

# custom software done
RUN cd /

RUN apt-get -y --no-install-recommends install \
	libasound2 \
	libegl1-mesa

# below libs were missing and starting rstudio lead to a rather cryptic
# error, compare: https://forum.qt.io/topic/93247/qt-qpa-plugin-could-not-load-the-qt-platform-plugin-xcb-in-even-though-it-was-found/9
RUN apt-get update -qq && apt-get -y --no-install-recommends install \
	libxkbcommon-x11-0 \
	libxkbcommon-x11-dev \
	libx11-xcb1

# finally, install some basic packages
RUN install2.r --error \
	--deps TRUE \
	shades \
        ggrepel \
        ggpubr \
        msigdbr

RUN R -e "BiocManager::install('mlr3')"	
RUN R -e "BiocManager::install(c('GSVA','IHW'))"

# show list of all installed packages
RUN R -e "sort(unname(installed.packages()[, 'Package']))"

# some clean up..
RUN rm -rf /var/lib/apt/lists/* \
	&& apt-get clean \
	&& apt-get purge
	
# R
RUN rm -rf /tmp/downloaded_packages/ /tmp/*.rds
# prepare the Rstudio authentication script
COPY ./scripts/rstudio_auth_r4.sh /bin/
RUN chmod +x /bin/rstudio_auth_r4.sh && \
  ln -s -r /bin/rstudio_auth_r4.sh /bin/rstudio_auth_r4

# prepare the rstudio start script
COPY ./scripts/start_rstudio.sh /bin/
RUN chmod +x /bin/start_rstudio.sh && \
  ln -s -r /bin/start_rstudio.sh /bin/start_rstudio
# all done.

# user
#USER rstudio

# server can now be started by calling '/bin/start_rstudio <port>' in the image
