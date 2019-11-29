ARG BASE_IMAGE_BUILDER_GO=golang
ARG ALPINE_VERSION=3.10
ARG GO_VERSION=1.13.0

ARG BASE_IMAGE_BUILDER_PYTHON=python
ARG BASE_IMAGE_BUILDER_NODE=alpine
ARG PYTHON_VERSION=3.7
ARG IKY_VERSION=v2.2.0


# Build Nodej
FROM ${BASE_IMAGE_BUILDER_NODE}:${ALPINE_VERSION} AS frontend
ARG IKY_VERSION
WORKDIR /opt
RUN apk add -q --progress --update --no-cache git npm
RUN wget -q https://gitlab.com/kennbroorg/iKy/-/archive/${IKY_VERSION}/iKy-${IKY_VERSION}.tar.gz  -O iKy.tar.gz && \
    tar -xzf iKy.tar.gz --strip-components=1 && \
    rm iKy.tar.gz
#ENV NODE_ENV production
WORKDIR /opt/frontend
RUN pwd && ls -lash && node --version ; npm --version
RUN npm install
RUN npm run build


# Frontend final
FROM nginx:1.17.6-alpine AS final
ARG BUILD_DATE
ARG VCS_REF
ARG IKY_VERSION
LABEL org.label-schema.schema-version="1.0.0-rc1" \
    maintainer="puneyk@rop.la" \
    org.label-schema.build-date=$BUILD_DATE \
    org.label-schema.vcs-ref=$VCS_REF \
    org.label-schema.version=$IKY_VERSION
EXPOSE 80
#HEALTHCHECK --start-period=1s --interval=100s --timeout=2s --retries=1 CMD ["/server","healthcheck"]
USER 1000
#ENTRYPOINT ["/server"]
COPY --from=frontend --chown=1000 /stackedit/dist /usr/share/nginx/htm
#COPY --from=server --chown=1000 /tmp/gobuild/app /server

