FROM ubuntu:16.04

#install lib
RUN cd ~ && apt-get update \
    && apt-get dist-upgrade -y \
    && apt-get install -y git \
    && apt-get install -y build-essential libboost-all-dev \
    libcurl4-openssl-dev libdb5.3-dev libdb5.3++-dev \
    python-twisted python-mysqldb python-dev python-setuptools python-memcache \
    python-simplejson python-pip  python-pylibmc make autoconf libtool g++ build-essential \
    pkg-config unzip libboost-all-dev apache2 memcached php7.0-mysqlnd php7.0-curl \
    php7.0-json php7.0-curl libapache2-mod-php7.0 build-essential make automake \
    gcc php-memcached wget php-mbstring php-dom php-mysql php-mysqli vim nano curl 


#berkeley-db
RUN wget http://download.oracle.com/berkeley-db/db-4.8.30.zip \
    && unzip db-4.8.30.zip && cd db-4.8.30 && cd build_unix \
    && ../dist/configure --prefix=/usr/local --enable-cxx \
    && make && make install && cd .. && echo "Berkeley libs installed"


#update pip version
RUN wget https://bootstrap.pypa.io/pip/2.7/get-pip.py -O get-pip.py && python get-pip.py

#run Update 
RUN pip install --upgrade setuptools \
    && pip install -U pyopenssl

#stratum-minig setup 
RUN cd ~ && easy_install -U distribute && pip install autobahn && pip install --upgrade urllib3 && pip install --upgrade python-language-server

WORKDIR /var

RUN mkdir /var/www/html/mpos
COPY . /var/www/html/mpos


# RUN cd ~ &&  git clone https://github.com/Tydus/litecoin_scrypt.git && git clone https://github.com/ahmedbodi/stratum-mining.git \
#     && git clone https://github.com/ahmedbodi/stratum.git
# RUN cd ~ &&  cd stratum-mining && git submodule init && git submodule update && cd externals/litecoin_scrypt && python setup.py install

# RUN cd ~ &&  cd stratum-mining/externals/stratum  && python setup.py install

#config statum 
# COPY ./stratum-mining/conf/config.py /stratum-mining/conf/config.py

#install database 
# RUN apt-get update  \
#     && echo 'mysql-server mysql-server/root_password password 1234' |  debconf-set-selections \
#     && echo 'mysql-server mysql-server/root_password_again password 1234' |  debconf-set-selections \
#     && apt-get install -y mysql-server  libssl-dev

# RUN service mysql start && MYSQL_PWD=1234 mysql -u root -e "CREATE DATABASE mpos; SHOW DATABASES;"
# RUN service mysql start && MYSQL_PWD=1234 mysql -u root mpos < /var/www/html/mpos/sql/000_base_structure.sql \
#     && MYSQL_PWD=1234 mysql -u root -e "USE mpos; SHOW TABLES;"

#install MPOS
RUN cd /var/www/html/mpos &&  php composer.phar install && chown -R www-data templates/compile templates/cache\
    && sed -i '/# vim: syntax=apache ts=4 sw=4 sts=4 sr noet/ a\ServerName 127.0.0.1' /etc/apache2/apache2.conf

RUN sed -i 's|DocumentRoot /var/www/html|DocumentRoot /var/www/html/mpos/public|' /etc/apache2/sites-available/000-default.conf


# start stratum
# RUN cd ~ && cd /stratum-mining && twistd -ny launcher.tac -l -
# RUN twistd -ny launcher.tac

CMD ["apache2ctl", "-D", "FOREGROUND"]
EXPOSE 80
#stratum-minig start 
