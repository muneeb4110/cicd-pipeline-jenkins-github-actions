FROM jenkins/jenkins:lts-jdk21

USER root

RUN apt-get update \
    && apt-get install -y --no-install-recommends python3 python3-venv python3-pip curl ca-certificates \
    && rm -rf /var/lib/apt/lists/*

COPY --from=docker:cli /usr/local/bin/docker /usr/local/bin/docker

RUN curl -fsSL --retry 5 --retry-all-errors -o /usr/local/bin/kubectl https://dl.k8s.io/release/v1.36.1/bin/linux/amd64/kubectl \
    && chmod +x /usr/local/bin/kubectl

USER jenkins
