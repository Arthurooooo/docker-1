FROM ruby
RUN apt-get update && apt-get install -y nodejs && gem install rails -v 4.2.7 && mkdir -p /opt/app

MAINTAINER argonthi <argonthi@student.42.fr>

ONBUILD WORKDIR /opt/app
ONBUILD COPY app /opt/app

ONBUILD EXPOSE 3000
ONBUILD RUN bundle install
ONBUILD RUN rake db:migrate
ONBUILD RUN rake db:seed