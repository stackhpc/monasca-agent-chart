FROM monasca/agent-collector:stable-train
RUN apk add git && \
    pip install git+https://github.com/stackhpc/stackhpc-monasca-agent-plugins.git && \
    ln -s /usr/local/lib/python3.6/site-packages/usr/lib/monasca/ /usr/lib/
