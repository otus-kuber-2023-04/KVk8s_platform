FROM python:alpine

RUN adduser -u 1001 -D appuser

RUN mkdir /app
RUN chmod -R 777 /app
COPY ./index.html /app/homework.html

EXPOSE 8000

USER appuser

CMD [ "python", "-m", "http.server", "8000" ]
