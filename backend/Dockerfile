FROM python:3.10
WORKDIR /var/www/app
COPY pyproject.toml poetry.lock ./
RUN pip install poetry && \
   poetry config virtualenvs.create false && \
   poetry install --no-dev
COPY . .
ENV PYTHONPATH "${PYTHONPATH}:/var/www/app"
RUN poetry run bash ./prestart.sh
EXPOSE 8000
CMD [ "poetry", "run", "uvicorn", "app.main:app", "--reload" ]