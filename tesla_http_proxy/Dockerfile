ARG BUILD_FROM
FROM $BUILD_FROM as builder

RUN apk add --no-cache go git

# install Tesla Go packages
ARG TESLA_MOTORS_SHA=8be54ba48d3d4356ae65b95a3b5f95506b4dd4d7
RUN TESLA_MOTORS_SHA=${TESLA_MOTORS_SHA} \
  git clone https://github.com/krconv/vehicle-command.git /vehicle-command
WORKDIR /vehicle-command
RUN git checkout ${TESLA_MOTORS_SHA}
RUN go get ./... && \
  go build ./... && \
  go install ./...

FROM $BUILD_FROM

COPY --from=builder /root/go/bin/tesla-http-proxy /usr/bin/
COPY --from=builder /root/go/bin/tesla-keygen /usr/bin

# install dependencies
RUN apk add --no-cache \
  python3 \
  py3-flask \
  py3-requests \
  gpg-agent \
  pass \
  curl \
  openssl

# Python 3 HTTP Server serves the current working dir
WORKDIR /data

# Copy data for add-on
COPY rootfs /
RUN chmod a+x /app/run.sh

CMD [ "/app/run.sh" ]
