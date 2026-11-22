FROM python:3.11-slim AS corpus

WORKDIR /tmp/corpus
COPY data/corpus.zip /tmp/corpus.zip
RUN python -c "import zipfile; zipfile.ZipFile('/tmp/corpus.zip').extract('lex.jsonl', '/tmp/corpus')"

FROM python:3.11-slim

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    RAG_CORPUS=/app/data/lex.jsonl \
    RAG_DB=/app-data/rag.sqlite3

WORKDIR /app
COPY requirements.txt .
RUN python -m pip install --no-cache-dir -r requirements.txt

COPY app ./app
COPY --from=corpus /tmp/corpus/lex.jsonl ./data/lex.jsonl

RUN useradd --system --uid 10001 --create-home app \
    && mkdir -p /app-data \
    && chown -R app:app /app /app-data

USER 10001:10001
EXPOSE 10000

CMD ["sh", "-c", "uvicorn app.main:create_app --factory --host 0.0.0.0 --port ${PORT:-10000}"]