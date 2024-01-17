FROM alpine:3.6

WORKDIR /work

RUN apk update && \
    apk add alpine-sdk git curl python python-dev py-pip libffi-dev libressl-dev memcached libmemcached-dev zlib-dev cyrus-sasl-dev mysql-client mysql-dev
RUN apk add python3 py3-pip ca-certificates libressl-dev

RUN wget https://bootstrap.pypa.io/pip/2.7/get-pip.py -O get-pip.py && python get-pip.py
RUN pip install simplejson pylibmc MySQL-python pycrypto twisted==16.4.0 

RUN pip install --upgrade setuptools \
    && pip install -U pyopenssl

#stratum-minig setup 
RUN cd ~ && easy_install -U distribute && pip install autobahn && pip install --upgrade urllib3 && pip install --upgrade python-language-server


RUN git clone https://github.com/ahmedbodi/stratum-mining.git && \
    git clone https://github.com/ahmedbodi/stratum.git && \
    git clone https://github.com/mecab/ytn_yescrypt_python.git

RUN pip install --upgrade pip setuptools && easy_install stratum 

# RUN cd stratum-mining && \
    # git submodule init && \
    # git submodule update

RUN cd stratum-mining && \
    curl https://gist.githubusercontent.com/mecab/bb3e1f1eaef9b7d06b1ec7cfa5f87f0d/raw/03c0f87b0faa84c0dc290443085953ac30a07ef2/stratum.patch | \
         tail -n +4 \
         > stratum.patch && \
    patch -p1 < stratum.patch

RUN cd ytn_yescrypt_python && python setup.py install

RUN cd stratum-mining && \
    sed -i -e 's/config = None/raise/' lib/settings.py && \
    sed -i -e "/^sys\.path =/s/.*/sys.path = [os.path.join(os.getcwd(), 'conf'),os.path.join(os.getcwd(), '.'),os.path.join(os.getcwd(), 'externals', 'stratum-mining-proxy'),] + sys.path/" launcher.tac

COPY entrypoint /entrypoint
RUN chmod +x /entrypoint

EXPOSE 3333
ENTRYPOINT ["/entrypoint"]
