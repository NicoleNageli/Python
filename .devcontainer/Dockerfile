# Built from the repo root (see "build.context" in devcontainer.json) so the
# dependency files below are in the build context.
FROM mcr.microsoft.com/devcontainers/python:latest

# Copy the uv binaries from the official image instead of `pipx install`, which
# as root leaves a root-owned pipx lock that breaks the vscode user's
# `pipx install ... uv` in postCreateCommand.
COPY --from=ghcr.io/astral-sh/uv:0.13.7 /uv /uvx /usr/local/bin/

# Install dependencies from pyproject.toml + uv.lock (replaces requirements.txt).
# uv honors .python-version (3.15t) and downloads that interpreter. The venv
# lives outside the bind-mounted workspace so it is baked into the image, and
# UV_PROJECT_ENVIRONMENT makes `uv run`/`uv sync` in the workspace reuse it.
ENV UV_PROJECT_ENVIRONMENT=/opt/venv \
    UV_PYTHON_INSTALL_DIR=/opt/uv-python \
    UV_LINK_MODE=copy
COPY pyproject.toml uv.lock .python-version /tmp/uv-project/
RUN cd /tmp/uv-project \
    && uv sync --frozen --no-install-project --no-cache \
    && chown -R vscode:vscode /opt/venv /opt/uv-python \
    && rm -rf /tmp/uv-project
