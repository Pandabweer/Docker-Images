FROM enriquebos/python-poetry:py-3.12-poetry-1.8

WORKDIR /app
COPY . .

RUN poetry install --with dev --no-interaction --no-ansi --no-root
USER appuser

ENTRYPOINT [ "poetry" ]
CMD [ "run", "python", "-m", "src" ]