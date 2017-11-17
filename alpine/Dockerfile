FROM alpine:3.6

MAINTAINER zhang_lin66@foxmail.com

ARG TZ="Asia/Shanghai"

ENV TZ ${TZ}

RUN apk upgrade --update \

    && apk add bash tzdata \

    && ln -sf /usr/share/zoneinfo/${TZ} /etc/localtime \

    && echo ${TZ} > /etc/timezone \

    && rm -rf /var/cache/apk/*



CMD ["/bin/bash"]
