FROM ubuntu:18.04

ENV DEBIAN_FRONTEND noninteractive
# Set locale.
RUN apt-get -y update && apt-get install -y locales software-properties-common  build-essential  \
  && locale-gen en_US.UTF-8 && update-locale LANG=en_US.UTF-8 LC_ALL=en_US.UTF-8 \
  # Add PPAs.
 # && add-apt-repository ppa:cartodb/gcc \
 # && add-apt-repository ppa:cartodb/gis \
 # && add-apt-repository ppa:cartodb/nodejs \
 # && add-apt-repository ppa:cartodb/postgresql-10 \
  # Install apt packages.
  && apt-get update && apt-get -y --no-install-recommends install \
    autoconf \
    automake \
    binutils-doc \
    bison \
    flex \
    # Git
    git \
    # PostgreSQL
    libpq5 \
    libpq-dev \
    postgresql-client-10 \
    postgresql-client-common \
    libcurl4-gnutls-dev \
    libffi-dev \
    # GIS:
    # - Proj
    proj-bin \
    proj-data \
    libproj-dev \
    # - JSON
    libjson-c-dev  \
    python-simplejson \
    # - GEOS
    libgeos-c1v5 \
    libgeos-dev \
    # - GDAL
    gdal-bin \
    libgdal-dev \
    # libgdal1-dev \
    # gdal2.1-static-bin \
    # Postgis
    zlib1g \
    zlib1g-dev \
    libc6-dev \
    liblzma-dev  \
    pkg-config \ 
    #libxml2 \
    libxml2-dev \
    libxslt-dev \
    liblwgeom-2.4-0 \
    postgis \
    postgresql-10-postgis-2.4 \
    postgresql-10-postgis-scripts \
    # NodeJS and dependencies
    libcairo2-dev \
    libgif-dev \
    libjpeg-dev \
    libpango1.0-dev \
    libpixman-1-0 \
    libpixman-1-dev \
    nodejs \
    # Ruby dependencies
    libreadline6-dev \
    openssl \
    # Python dependencies
    imagemagick \
    python-all-dev \
    unp \
    zip \
    # Other dependencies
    unzip \
    wget \
  # Install Pip.
  && wget -O /tmp/get-pip.py https://bootstrap.pypa.io/get-pip.py \
  && python /tmp/get-pip.py \
  && rm /tmp/get-pip.py \
  && rm -rf /var/lib/apt/lists/*

# https://github.com/CartoDB/cartodb/blob/4906f4bba025300e9d5c52ecd509eda5b2ccb36d/doc/manual/source/install.rst
RUN apt-add-repository ppa:brightbox/ruby-ng && apt-get update  \
  &&  apt-get install  -y ruby2.4 ruby2.4-dev   \
  &&  apt-get install  -y ruby-bundler
RUN gem install compass

#ENV CC=/usr/bin/gcc-4.9
#ENV CXX=/usr/bin/g++-4.9
# ENV PATH=/opt/rubies/ruby-2.4.1/bin:$PATH

# CartoDB/cartodb version
ENV CARTODB_VERSION=4.26.0

# Download the Editor program, using the specified release tag.
RUN git clone --recursive --branch v${CARTODB_VERSION} --depth 1 https://github.com/CartoDB/cartodb.git

# Environment type.
ENV RAILS_ENV=development

#RUN whereis libxml2
#RUN ls -lah /usr/include/libxml2
#RUN ls -lah /usr/local/

# RUN gem install nokogiri -v 1.8.5   --debug --backtrace --verbose

# RUN gem install nokogiri -v '1.8.5' -- \
#     --use-system-libraries \
#     --with-xml2-lib=/usr/lib \
#     --with-xml2-include=/usr/include/libxml2 \
#     --with-xslt-lib=/usr/lib \
#     --with-xslt-include=/usr/include/libxslt

# https://nokogiri.org/tutorials/installing_nokogiri.html
RUN  gem install nokogiri -- --use-system-libraries 
# RUN  gem install nokogiri -- --use-system-libraries   \
#    --with-xml2-include=/usr/include/libxml2/libxml \
#    --with-xml2-lib=/usr/lib \
#    --with-xslt-include=/usr/include/libxslt \
#    --with-xslt-lib=/usr/lib
# RUN apt-get install  patch   &&  gem install nokogiri
# RUN gem install nokogiri
# RUN gem install nokogiri -v '1.8.5' --source 'http://rubygems.org/' 
# RUN gem install nokogiri --  --with-xslt-dir=/usr/include/ibxslt  --with-xml2-dir=/usr/include/libxml2

# RUN gem install bundler --version=1.17.3  

# RUN bundle config build.nokogiri --use-system-libraries   \
#     --with-xslt-dir=/usr/local/opt/libxslt  \
#     --with-xml2-dir=/usr/local/opt/libxml2 &&  \
#     bundle install 
# RUN  bundle config build.nokogiri --use-system-libraries


WORKDIR /cartodb

RUN gem install  archive-tar-minitar rack && \
    bundle update thin 




# Install Ruby dependencies.
RUN bundle install \
  # Install Node dependencies.
  && npm install \
  # Add dependencies of configuration merge script.
  && npm install \
    js-yaml \
    yargs \
  # Install Python dependencies.
  && CPLUS_INCLUDE_PATH=/usr/include/gdal \
    C_INCLUDE_PATH=/usr/include/gdal \
    PATH=$PATH:/usr/include/gdal \
    pip install -r python_requirements.txt \
  # Build the project.
  && bundle install \
  && PATH=$PATH:/cartodb/node_modules/grunt-cli/bin \
    bundle exec grunt --environment=${RAILS_ENV} \
  # Clean up.
  && rm -rf \
    /root/.cache \
    /root/.bundle \
    /root/.gem \
    /root/.npm \
  && mkdir -p /cartodb/docker-entrypoint-util

# Copy resources.
COPY util/* /cartodb/docker-entrypoint-util/
COPY config/* /cartodb/config/
COPY docker-entrypoint.sh /cartodb/

EXPOSE 3000

# We configure the application using just the base config files here. Additional configuration files
# can be merged with the base by supplying their filenames as --app_config or --database option
# arguments in the CMD.
ENTRYPOINT [ \
  "./docker-entrypoint.sh", \
  "--app_config", "base.app_config.yml", \
  "--database", "base.database.yml" \
]

# We merge the sample config files with the base configs by default.
# Custom configuration may be used by extending this image as follows:
# 1) COPY one or more config files into /cartodb/config/.
# 2) Pass the filename(s) as --app_config or --database option arguments in the CMD.
CMD [ \
  "--app_config", "app_config.yml.sample", \
  "--database", "database.yml.sample" \
]
