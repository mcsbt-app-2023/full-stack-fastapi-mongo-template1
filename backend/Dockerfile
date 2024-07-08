# Use the official base image from tiangolo
FROM tiangolo/uvicorn-gunicorn-fastapi:python3.10

# Set the working directory in the container
WORKDIR /app/

# Install Poetry
RUN curl -sSL https://install.python-poetry.org | POETRY_HOME=/opt/poetry python && \
    cd /usr/local/bin && \
    ln -s /opt/poetry/bin/poetry && \
    poetry config virtualenvs.create false

# Copy the PyProject toml and Poetry lock file (if exists)
COPY ./pyproject.toml ./poetry.lock* /app/

# Install dependencies conditionally based on whether dev dependencies are needed
ARG INSTALL_DEV=false
RUN bash -c "if [ $INSTALL_DEV == 'true' ] ; then poetry install --no-root ; else poetry install --no-root --only main ; fi"

# Set PYTHONPATH to make Python modules in /app available
ENV PYTHONPATH=/app

# Copy scripts and application code
COPY ./scripts/ /app/
COPY ./prestart.sh /app/
COPY ./tests-start.sh /app/
COPY ./app /app/app

# The EXPOSE instruction is ignored by Cloud Run, but good for documentation
EXPOSE 8000

# Command to run the application, $PORT is set by Cloud Run
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "$PORT"]
