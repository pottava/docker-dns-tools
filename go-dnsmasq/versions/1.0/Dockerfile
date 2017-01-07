# go-dnsmasq based on AlpineLinux
# docker run --rm -it pottava/go-dnsmasq:1.0 sh

FROM alpine:3.5

ENV S6_VERSION=v1.18.1.5 \
    GO_DNSMASQ_VERSION=1.0.7

RUN apk --no-cache add --virtual build-dependencies curl \

  # Install s6-overlay for running multiple processes in a container
  && curl --location --show-error --silent --output s6.tar.gz \
     https://github.com/just-containers/s6-overlay/releases/download/${S6_VERSION}/s6-overlay-amd64.tar.gz \
  && tar xzf s6.tar.gz -C / \
  && rm -f s6.tar.gz \

  # Install go-dnsmasq as a caching DNS server / forwarder
  && curl --location --show-error --silent --output /usr/sbin/go-dnsmasq \
     https://github.com/janeczku/go-dnsmasq/releases/download/${GO_DNSMASQ_VERSION}/go-dnsmasq-min_linux-amd64 \
  && chmod +x /usr/sbin/go-dnsmasq \

  # Configure s6 with /etc/service.d/ for go-dnsmasq
  && mkdir -p /etc/service.d/resolver \
  && echo -e "#!/bin/sh\n \
go-dnsmasq --default-resolver --append-search-domains --hostsfile=/etc/hosts" \
     > /etc/service.d/resolver/run \
  && echo -e "#!/bin/sh\n \
s6-svscanctl -t /var/run/s6/services" \
     > /etc/service.d/resolver/finish \

  # Clean up
  && apk del --purge build-dependencies

ENTRYPOINT ["/init"]
