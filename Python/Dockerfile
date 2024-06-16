# syntax=docker/dockerfile:1.8
# Set the prefered syntax file to assure the same build
# Learn more about Docker syntax here -> https://docs.docker.com/build/dockerfile/frontend/
# Learn more about Docker references at -> https://docs.docker.com/go/dockerfile-reference/

# Setting the preferred versions, exluding patches/bug fixes.
ARG PYTHON_VER=3.12
ARG POETRY_VER=1.8

# Setting the platform ensures the same results on every platform.
# It's also preferred since linux/amd64 is what most Docker containers run on in the cloud.
# I recommend setting it to the platform you're going to push production to.
FROM --platform=linux/amd64 python:${PYTHON_VER}-slim AS base

# Create a non-privileged user that the app will run under.
# See https://docs.docker.com/go/dockerfile-user-best-practices/
# To switch to the non-privileged user use the following syntax: USER appuser
ARG UID=10001
RUN adduser \
    --disabled-password \
    --gecos "" \
    --home "/nonexistent" \
    --shell "/sbin/nologin" \
    --no-create-home \
    --uid "${UID}" \
    appuser

# Python environment settings.
ENV \
    # Prevents Python from writing pyc files.
    PYTHONDONTWRITEBYTECODE=1 \
    # A random value is used to seed the hashes of str and bytes objects.
    PYTHONHASHSEED=random \
    # Keeps Python from buffering stdout and stderr to avoid situations where
    # the application crashes without emitting any logs due to buffering.
    PYTHONUNBUFFERED=1

# Pip environment settings.
ENV \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_DEFAULT_TIMEOUT=100 \
    PIP_ROOT_USER_ACTION=ignore

# Poetry environment settings.
ENV \
    POETRY_VERSION=${POETRY_VER} \
    POETRY_NO_INTERACTION=1 \
    # Prevent venvs from being created inside an image.
    POETRY_VIRTUALENVS_CREATE=false \
    POETRY_CACHE_DIR='/var/cache/pypoetry' \
    POETRY_HOME='/usr/local'

FROM base AS extensions

# Ensure latests versions of setuptools and pip.
RUN python -m pip install -U setuptools pip

# Getting usable packages.
RUN apt-get update && apt-get upgrade -y \
    && apt-get install --no-install-recommends -y \
    bash \
    build-essential \
    curl \
    git

# Installing `poetry` package manager.
# https://github.com/python-poetry/poetry
RUN curl -sSL 'https://install.python-poetry.org' | python - \
    && poetry self add poetry-plugin-up

# Cleaning packages cache.
RUN apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*