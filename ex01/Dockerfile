FROM debian:12

RUN apt update
RUN apt install -y tree
COPY script.sh /script.sh

CMD ["sh", "script.sh"]