# https://www.docker.elastic.co/
FROM docker.elastic.co/logstash/logstash:7.15.2

# plugin 설치 추가
RUN logstash-plugin install logstash-codec-rubydebug &&\
    logstash-plugin install logstash-filter-json &&\
    logstash-plugin install logstash-filter-mutate