---
name: pixeltable
description: >
  Build multimodal AI apps with Pixeltable. One application file (app.py)
  declares TableModel tables and FastAPIRouter routes. Create tables with
  pxt schema update. Start HTTP with pxt service update. Insert a row or
  POST to try the app. Use computed columns instead of LangChain,
  pandas-as-store, or a separate vector DB. Use when building RAG,
  processing images/video/audio/documents, or serving an API. Do NOT use for
  general Python or direct PostgreSQL administration.
license: Apache-2.0
allowed-tools: []
metadata:
  author: Pixeltable
  version: 2.10.3
  type: documentation
  executes-code: false
  category: data-infrastructure
  tags: [multimodal, ai, data, tables, embeddings, rag, udf, video, audio, images, documents, agents, tools, fastapi, declarative, computed-columns, vector-search]
  documentation: https://docs.pixeltable.com/
  support: https://github.com/pixeltable/pixeltable/discussions
  priority: 6
  pathPatterns: ["**/*.py"]
  importPatterns: ["pixeltable", "import pixeltable as pxt", "from pixeltable"]
  bashPatterns: ['^\s*pxt(?:\s|$)']
  promptSignals:
    phrases: ["pixeltable", "computed column", "embedding index", "add_embedding_index", "TableModel", "create_view", "document_splitter", "invoke_tools", "pxt schema", "pxt service", "pxt shell", "pxt errors", "pxt dashboard"]
    minScore: 6
---

## STOP

If you find yourself importing any of these, you are off-path:

1. **Do not use LangChain / LlamaIndex / Haystack / LangGraph.** Chunking is `document_splitter`. Search is `.similarity()`. Tools are `pxt.tools()` + `invoke_tools()`.
2. **Do not use pandas as a working store.** Tables are the store. `.collect().to_pandas()` is export only.
3. **Do not write `for row in ...:` loops calling models.** Wrap the call in a computed column.
4. **Do not install a separate vector database.** In an app, `__indexes__ = [pxt.EmbeddingIndex(...)]` on the model. In a notebook, `t.add_embedding_index(col, embedding=fn)`. Search with `.similarity(string=query)`.
5. **Do not write `while not done:` agent loops.** Insert a row. The computed-column chain runs.

See [anti-patterns.md](references/anti-patterns.md) (6 macros).

## What is Pixeltable?

One application file (`app.py`) is the backend.

- `pxt schema update`: creates tables from `TableModel` classes. Does not start HTTP.
- Insert a sample, `.select()`, `pxt dashboard`, or `pxt schema diff`. Compute runs on insert. After `pxt service update`, curl POST.
- `pxt service update`: starts HTTP (local or `pxt://`). `pxt service list` prints the URL. This is the serving command; do not reach for `pxt service run`.

`pxt db update` sets hosted image, secrets, and workers. It does not insert rows and does not start app HTTP.

First run: [Quickstart](https://docs.pixeltable.com/overview/quick-start). Why: [Why Pixeltable](https://docs.pixeltable.com/overview/pixeltable).

## Starting a new project

```bash
pip install 'pixeltable[serve]'   # Python 3.11+
pxt init
pxt service example --out app.py
pxt schema check app.py           # validates the file; warns if 'app' is shadowed
pxt schema update app.py my_app
pxt service update app.py my_app
pxt service list                  # assigned port; do not hard-code :8000
```

`pxt service example` writes models plus a `FastAPIRouter`. Schema only (no HTTP): `pxt schema example --brief --out app.py`. Then edit `app.py` and run `pxt schema update` again. After a schema change, run `pxt service update` again if routes exist. Do not `python app.py`. Full flags: [cli.md](references/cli.md).

The last argument (`my_app`, or `pxt://org:db` on Cloud) is a catalog directory, not a folder on disk. `pxt init` marks the project root. Schema does not start HTTP. Service does not create tables. Non-interactive: `pxt service update ... -f`. Local handle: `pxt.get_table('my_app.docs')`.

Same file on Cloud: set `PIXELTABLE_API_KEY`, add `[[pixeltable.database]]` with `name = 'pxt://org:db'`, then `pxt db update pxt://org:db -f`, then `pxt schema update app.py pxt://org:db -f`, then `pxt service update app.py pxt://org:db -f`. Cloud handle: `pxt.get_table('pxt://org:db/docs')`. Cloud databases store media in their managed home bucket by default; set a column `destination=` only to override it. On Cloud, try the app with dashboard insert plus `pxt schema diff`, and inspect failures with `pxt service logs` / `pxt db logs`. [Cloud](https://docs.pixeltable.com/howto/deployment/cloud).

## The application file

`pxt service example --out app.py` writes this shape. Edit it. Then `pxt schema update app.py my_app`.

```python
import pixeltable as pxt
import pixeltable.functions as pxtf
from pixeltable.serving import FastAPIRouter

TableModel = pxt.model_base()


@pxt.udf
def excerpt(text: str, n: int = 12) -> str:
    return text if len(text) <= n else f'{text[:n]}...'


class Docs(TableModel, name='docs'):
    id = pxt.Column(value=pxtf.uuid.uuid7(), primary_key=True)
    title: pxt.String
    body: pxt.String | None
    title_upper = pxtf.string.upper(title)
    summary = excerpt(title)


ingest = FastAPIRouter(name='ingest')
ingest.add_insert_route(
    Docs, path='/docs', inputs=[Docs.title, Docs.body],
    outputs=[Docs.id, Docs.title_upper, Docs.summary],
)
ingest.add_update_route(
    Docs, path='/docs/update', inputs=[Docs.title],
    outputs=[Docs.id, Docs.title_upper],
)
ingest.add_compute_route(Docs, path='/titles', inputs=[Docs.title], outputs=[Docs.title_upper])
```

Annotation is a stored column. Assignment is a computed column. Optional is `T | None`. Primary key is `pxt.Column(..., primary_key=True)`. Indexes on the model: `__indexes__ = [pxt.EmbeddingIndex(...)]`. `from pixeltable.serving import FastAPIRouter`.

Already have FastAPI: after schema update, `ingest.bind('my_app')` then `app.include_router(ingest)`. Call `pxt.get_table()` inside custom handlers. [workflows.md](references/workflows.md).

RAG, views, and search: [workflows.md](references/workflows.md). Do not add Hugging Face or spaCy unless the user asked.

## Apps vs notebooks

- **Apps:** `app.py` + `pxt schema update` + `pxt service update`. Indexes on the model.
- **Notebooks / REPL:** `pxt.create_table()`, `add_computed_column()`, `add_embedding_index()`. The appendix below uses that form.

## Where to look

| Need | Open |
|------|------|
| `pxt schema`, `pxt service`, inspect | [cli.md](references/cli.md) |
| Types, views, UDFs, UDAs | [core-api.md](references/core-api.md) |
| Provider import and output shape | [providers.md](references/providers.md) |
| Serving, FastAPIRouter, routes | [workflows.md](references/workflows.md) |
| Wrong stack | [anti-patterns.md](references/anti-patterns.md) |

Add video, audio, agents, or a UI by editing `app.py`. A view is either a filter (`base=Docs.where(...)`) or an iterator (`frame_iterator`, `audio_splitter`, `document_splitter`, `video_splitter`, `string_splitter`, `list_iterator`, `tile_iterator`). Check `pixeltable.functions` before writing a UDF. Start from `pxt service example` or `pxt schema example`. Do not invent a second `pxt schema update` path.

## API traps

| Wrong | Correct |
|-------|---------|
| `openai.vision(...)` | Deprecated (the only deprecated function in `pixeltable.functions`). Use `chat_completions` with `image_url`, or `responses` |
| `from pixeltable.iterators import ...` | The whole `pixeltable.iterators` package is a deprecated shim (`FrameIterator`, `VideoSplitter`, `DocumentSplitter`, `StringSplitter`, `AudioSplitter`, `TileIterator`). Import the function from `pixeltable.functions.*` -- e.g. `from pixeltable.functions.video import frame_iterator` |
| `similarity(query)` | `similarity(string=query)`. Also `image=` / `audio=` / `video=` / `document=` / `vector=`; `idx=` picks among several indexes on one column |
| Re-run with `if_exists='ignore'` to fix logic | Notebook: `add_computed_column(..., if_exists='replace')`. App: **rename** the column, then `pxt schema update --allow-destructive` |
| Edit a computed column's expression in place, then `--allow-destructive` | Editing an existing column's expression is `UNSUPPORTED`; the flag does not help and the whole update applies nothing. Rename the column |
| `t.summary_errortype` | `t.summary.errortype` / `t.summary.errormsg`, on stored computed or media columns. `t.<col>.fileurl` / `.localpath` for media |
| `pxt.Required[pxt.String]` | Non-nullable by default. Optional: `T \| None` |
| `@pxt.udf def f(x: str)` fed a nullable column | A non-nullable parameter that receives `None` **skips the call**: the cell is `None` and `errormsg` is empty. Annotate `x: str \| None` and handle `None` in the body |
| `whisper.load_model(...)` inside a UDF body | Weights reload on every row. Use the shipped wrapper (`pxtf.whisper.transcribe`, `clip.using(...)`), or a module-scope cached loader |
| `recompute_columns(columns=['summary'])` | `t.recompute_columns('summary', errors_only=True)` |
| TOML routes or a retired serve CLI | `FastAPIRouter` + `pxt schema update` + `pxt service update` |
| `add_embedding_index()` in `app.py` | `__indexes__` on the TableModel. Note the DSL names an index `name=`, the SDK `idx_name=` |
| `make_video(order_by=...)` / `stitch_tiles(order_by=...)` | Both are `requires_order_by` UDAs: the ordering expression is the **first positional** argument -- `make_video(t.pos, t.frame, fps=25)`. `order_by=` raises |
| `pxt.create_table()` / `get_table()` at import in `app.py` | `TableModel` + `pxt schema update`. Import must not mutate the catalog |
| `EmbeddingIndex(frame, image_embed=clip)` | `embedding=clip` (covers text and image). Or both `string_embed=` and `image_embed=`. `image_embed=` alone cannot answer `similarity(string=...)` |
| `uuid.astype(pxt.String)` | `uuid.to_string()` (`from pixeltable.functions.uuid import to_string`). `astype` is not UUID→String |

Extract the field (`.text`, `.choices[0].message.content`). Cast Json with `.astype(pxt.String)` only before embedding or concatenating.

## Notebook / REPL appendix

```python
import pixeltable as pxt

pxt.create_dir('my_project', if_exists='ignore')
t = pxt.create_table('my_project.documents', {
    'title': pxt.String,
    'content': pxt.String,
    'image': pxt.Image,
    'video': pxt.Video,
    'audio': pxt.Audio,
    'doc': pxt.Document,
}, if_exists='ignore')
```

Types are non-nullable by default. Optional is `T | None`. Do not use `pxt.Required`.

```python
from pixeltable.functions.uuid import uuid7

t = pxt.create_table('my_project.items', {
    'content': pxt.String,
    'uuid': uuid7(),
}, primary_key=['uuid'], if_exists='ignore')
```

Insert: `t.insert([{...}])`. Computed column:

```python
from pixeltable.functions.openai import chat_completions

t.add_computed_column(
    summary=chat_completions(
        messages=[{'role': 'user', 'content': t.content}],
        model='gpt-4o-mini',
    ).choices[0].message.content,
    if_exists='ignore',
)
```

Views: `document_splitter`, `frame_iterator` (from `pixeltable.functions.video`), `string_splitter`, `audio_splitter`. Notebook indexes: `t.add_embedding_index('content', embedding=embed_fn, if_exists='ignore')`.

Query: `t.where(...).select(...).collect()`. Similarity: `t.content.similarity(string=query)`. In `@pxt.query`, alias as `score=sim`.

UDFs are recorded as a module path relative to the project root (`app.excerpt`).

Always `if_exists='ignore'` on notebook `create_*` / `add_*`. Failed cells: `t.recompute_columns('summary', errors_only=True)`. `string_splitter` / `document_splitter(..., separators='sentence')` need spaCy. Embedding indexes need `.using(...)`.

## pxt CLI

```bash
pxt init
pxt service example --out app.py
pxt schema check app.py
pxt schema update app.py my_app
pxt service update app.py my_app
pxt service list
pxt ls -l
pxt errors my_app/docs
pxt dashboard
```

[cli.md](references/cli.md).

## Resources

- [Quickstart](https://docs.pixeltable.com/overview/quick-start)
- [CLI](https://docs.pixeltable.com/platform/cli)
- [MCP Server](https://github.com/pixeltable/mcp-server-pixeltable-developer)
- [Docs](https://docs.pixeltable.com/llms-full.txt)
