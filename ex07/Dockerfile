FROM debian:12

RUN apt update && apt install -y python3

COPY index.html /index.html

CMD ["python3", "-m", "http.server", "80", "--directory", "/"]