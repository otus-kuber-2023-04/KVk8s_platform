FROM python:alpine

RUN adduser -u 1001 -D appuser

RUN mkdir /app
RUN chmod -R 777 /app

EXPOSE 8000

USER appuser

CMD [ "python", "-m", "http.server", "8000", "--directory", "/app" ]
