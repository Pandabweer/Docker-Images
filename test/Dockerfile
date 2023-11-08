FROM enriquebos/python-poetry:py-slim-3.12-poetry-1.7.0

WORKDIR /usr/src/app
COPY . .

RUN poetry install --with dev --no-interaction --no-ansi --no-root

ENTRYPOINT [ "poetry" ]
CMD ["run", "python", "-m", "app"]