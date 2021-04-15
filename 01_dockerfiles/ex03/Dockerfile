FROM ubuntu:16.04
WORKDIR /

MAINTAINER argonthi <argonthi@student.42.fr>

RUN apt-get update -q && \
    apt-get install -yq --no-install-recommends \
      ca-certificates \
      openssh-server \
      curl \
      postfix

RUN echo "root:qwerty" | chpasswd
RUN EXTERNAL_URL="http://192.168.99.104"

RUN curl -LO https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh && chmod 777 script.deb.sh && ./script.deb.sh

RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

RUN service ssh restart

RUN apt install -y tzdata

RUN apt-get install -y gitlab-ce 

RUN rm -rf /var/lib/apt/lists/*

EXPOSE 443 80

RUN echo "letsencrypt['enable'] = false" >> /etc/gitlab/gitlab.rb
RUN mkdir -p /etc/gitlab/ssl
RUN chmod 755 /etc/gitlab/ssl
RUN openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
 -subj /C=FR/ST=75/L=Paris/O=rs1/OU=rs1/CN=192.168.99.104/emailAddress=argonthi@student.42.fr \
 -keyout /etc/gitlab/ssl/my-host.internal.key -out /etc/gitlab/ssl/my-host.internal.crt

RUN echo "external_url  \"https://gitlab.example.com\"" >> /etc/gitlab/gitlab.rb
RUN echo "nginx['redirect_http_to_https'] = true" >> /etc/gitlab/gitlab.rb
RUN echo "nginx['ssl_certificate'] = \"/etc/gitlab/ssl/selfsigned.crt\"" >> /etc/gitlab/gitlab.rb
RUN echo "nginx['ssl_certificate_key'] = \"/etc/gitlab/ssl/selfsigned.key\"" >> /etc/gitlab/gitlab.rb
RUN echo "gitlab_rails['gitlab_shell_ssh_port'] = 2222" >> /etc/gitlab/gitlab.rb

RUN gitlab-ctl hup nginx

ENTRYPOINT  service ssh start && (/opt/gitlab/embedded/bin/runsvdir-start &) && gitlab-ctl reconfigure && tail -f /dev/null
