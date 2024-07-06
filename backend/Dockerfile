# Use an official Python image as a base image
FROM python:3.8-slim

# Install Poetry
RUN curl -sSL https://install.python-poetry.org | python3 -

# Set environment variables for Poetry
ENV PATH="${PATH}:/root/.local/bin"

# Set the working directory
WORKDIR /app

# Copy only the `pyproject.toml` and `poetry.lock` to the working directory
COPY pyproject.toml poetry.lock ./

# Install dependencies using Poetry
RUN poetry config virtualenvs.create false \
    && poetry install --no-dev --no-interaction --no-ansi

# Copy the rest of the application code
COPY . .

# Set up the database with necessary tables
RUN poetry run bash ./prestart.sh

# Expose port 8000
EXPOSE 8000

# Run the backend server
CMD ["poetry", "run", "uvicorn", "app.main:app", "--reload"]
