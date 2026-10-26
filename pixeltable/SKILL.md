---
name: pixeltable
description: >
  Build multimodal AI apps with Pixeltable. One application file (app.py)
  declares TableModel tables and FastAPIRouter routes. Create tables with
  pxt schema update. Start HTTP with pxt service update. Insert a row or
  POST to try the app. Use computed columns instead of LangChain,
  pandas-as-store, or a separate vector DB. Use when building RAG or
  tool-calling agents, processing images/video/audio/documents, or serving an
  API. Do NOT use for general Python or direct PostgreSQL administration.
license: Apache-2.0
allowed-tools: []
metadata:
  author: Pixeltable
  version: 2.12.0
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
5. **Do not write `while not done:` agent loops.** Insert a row. The chain is `chat_completions(..., tools=pxt.tools(...))`, then that provider's `invoke_tools(tools, response)`, then a second call whose prompt includes `pxtf.json.dumps(tool_output)`. `invoke_tools` is per provider (`openai`, `anthropic`, `gemini`, `bedrock`, `groq`).

See [anti-patterns.md](references/anti-patterns.md) (6 macros).

## What is Pixeltable?

One application file (`app.py`) is the backend.

- `pxt schema update`: creates tables from `TableModel` classes. Does not start HTTP.
- Insert a sample, `.select()`, `pxt dashboard`, or `pxt schema diff`. Compute runs on insert. After `pxt service update`, curl POST.
- `pxt service update`: starts HTTP (local or `pxt://`). `pxt service list` prints the URL. This is the serving command; do not reach for `pxt service run`.

First run: [Quickstart](https://docs.pixeltable.com/overview/quick-start). Why: [Why Pixeltable](https://docs.pixeltable.com/overview/pixeltable).

## Starting a new project

```bash
pip install 'pixeltable[serve]'   # Python 3.11+
pxt init
pxt service example --out app.py
pxt schema check app.py           # validates the file; warns if 'app' is shadowed
pxt schema update app.py my_app
pxt service update app.py my_app -f   # no TTY: without -f a pending start exits 3
pxt service list                  # assigned port; do not hard-code :8000
```

`pxt service example` writes models plus a `FastAPIRouter`. Schema only (no HTTP): `pxt schema example --brief --out app.py`. Then edit `app.py` and run `pxt schema update` again. After a schema change, run `pxt service update ... -f` again if routes exist; until then they answer 409. Do not `python app.py`. Full flags: [cli.md](references/cli.md).

The last argument (`my_app`, or `pxt://org:db` on Cloud) is a catalog directory, not a folder on disk. `pxt init` marks the project root. Local handle: `pxt.get_table('my_app.docs')`, or bind the models: `import app; app.TableModel.bind_all('my_app')`, then `app.Docs.insert(...)` / `app.Docs.select(...).collect()`. Inspect: `pxt ls -l`, `pxt errors my_app/docs`, `pxt dashboard`.

Provider keys (`OPENAI_API_KEY`, ...) come from the environment or `~/.pixeltable/config.toml`, and the `pxt` daemon reads both once, at startup; local services get them from the daemon. After changing either, run `pxt daemon restart` (every command answers 409 until you do), then `pxt service restart my_app/ingest`.

Same file on [Cloud](https://docs.pixeltable.com/howto/deployment/cloud): prefer `--add-config-entry` on `pxt db diff` or `pxt db update` over writing the hosted `[[pixeltable.database]]` entry by hand. The flag appends `name = '<uri>'` (lowercased) unless an entry already matches; a hand edit can put `name` on the local entry. A URI with no matching entry is an error and names `pxt db diff <uri> --add-config-entry`. Then:

```bash
pxt login                                  # browser sign-in; or export PIXELTABLE_API_KEY, which wins
pxt db update pxt://org:db --add-config-entry -f  # project files, image, workers; not secrets, rows, or HTTP
pxt schema update app.py pxt://org:db -f
pxt service update app.py pxt://org:db -f
```

`pxt db diff <uri> --add-config-entry` previews a first deployment; `update --add-config-entry` creates it in one command. Settings (`vars`, `db_*`, and sections such as `[openai]`) reach a hosted database without an upload; `pxt db diff --json` reports those changes under target `config`. Taking capacity away requires `--allow-destructive`. Size a hosted service in `pixeltable.toml` with `[pixeltable.database.services.'<path>']` (`cpu`, `memory_mb`, `workers`, `file_cache_gb`; path is `'<directory>/<service name>'`, or the name alone at the database root). `pxt service update` applies it; a `workers` change needs no restart, and a setting left out keeps the current value.

`pxt db update`, `start`, `stop`, `restart`, `retry`, `build-image`, and `delete` wait until Cloud applies the change. Cloud continues if the command stops: Ctrl-C or a timed-out tool call stops only the waiting. Pass `--no-wait` and follow with `pxt db status pxt://org:db`. After three hours the command exits 2 and the change is still under way. A failed update is retried with `pxt db retry pxt://org:db` or `pxt service retry pxt://org:db/NAME`, or by rerunning the same update. Hosted `pxt service update` waits too; if that wait stops, check `pxt service list pxt://org:db`. An unknown receipt outcome says to upgrade Pixeltable. A client older than the database's proxy protocol says `pip install --upgrade pixeltable`.

Since Pixeltable 0.7.20: `0` done (a catalog dry run such as `pxt drop PATH -n` too), `1` error (a usage error too, in `schema`, `service`, `db`, and `init`), `2` a usage error anywhere else (for example `pxt feedback` without `--kind`), pending changes on a `schema`/`service`/`db` plan (`diff` or `-n`), or a hosted change still under way after the three-hour wait, `3` refused for want of `-f` or `--allow-destructive`, or declined at the prompt. A refusal ends with `next: <command>  # <what it does>` on stderr. With `--json` that is `{"error_code": "CONFIRMATION_REQUIRED", "message", "retryable", "next": [{"command", "effect"}]}`; `schema`, `service`, and `db update` print the refused plan instead. A failed request ends with its code, as in `[TABLE_NOT_FOUND]`, and with `--json` prints `{"error_code", "message", "retryable"}`, plus `retry_after` when Cloud gives a wait. `PLAN_LIMIT`, `PAYMENT_REQUIRED`, and `STORE_FULL` print Cloud's reason alone: tell the user rather than retrying. `pxt errors` prints a `next:` line for each recomputable failed column (a snapshot column names none). Run that command when it does what the user asked; ask first when it drops data, stops or restarts what others call, or recomputes rows the user did not mention. When Pixeltable cannot do what the task needs, or you work around it, show the user `pxt feedback --kind gap '<what was needed, what you tried, what happened>'`, which prints a preview and sends nothing; add `--send` only once they agree. With `DO_NOT_TRACK` set to `1`, `true`, or `yes` (trimmed, case-insensitive), `--send` exits 3 and sends nothing, and there is no `next:` line; any other value, including empty, still sends. `--json` is also on `pxt usage` and `pxt feedback`. Single quotes keep the shell from running anything in the text. Use placeholder data, never customer names, hosts, paths, or secrets.

Provider calls retry on their own. OpenAI, Anthropic, and Gemini use reported rate limits and retry up to 10 times; other providers retry up to 3 times, paced by `rate_limit` (600 requests a minute when unset). HTTP 429, 503, 408, and 504, connection errors, and timeouts are retried, honoring `Retry-After`. A cell that still fails is listed by `pxt errors`, and `pxt recompute PATH COLUMN --errors-only -f` runs it again. Do not wrap a model call in a retry loop, `tenacity`, or a second client. OpenAI-compatible calls reach a proxy through `OPENAI_BASE_URL`, or `base_url` under `[openai]` in `config.toml`. On Cloud, `pxt secret set pxt://org OPENAI_BASE_URL=https://<proxy>/v1`, then restart. Only the OpenAI client reads a base URL from Pixeltable's config. Under a coding agent, ask the user to run `pxt login` in their own terminal (it waits up to 10 minutes), and never ask for a secret's value in the chat.

Provider keys on Cloud: `pxt secret set pxt://org OPENAI_API_KEY=...`, never in `pixeltable.toml`. A `secrets` mapping on `[[pixeltable.database]]` fails to load, a name starting with `PIXELTABLE_` is refused, and for a hosted database `pxt db diff` and `pxt db update` also refuse a secret key in the project file, such as `[openai] api_key`. A process reads secrets once, at startup; after `pxt secret set` or `pxt secret delete`, run `pxt db restart pxt://org:db` for tables and `pxt service restart pxt://org:db/NAME` for services. Cloud handle: `pxt.get_table('pxt://org:db/docs')`. Media goes to the database's managed home bucket unless a column sets `destination=`; share a file by link or `/sign` (`references/cli.md`, Cloud). Try the app with a dashboard insert plus `pxt schema diff`. Read Cloud failures with `pxt service logs` / `pxt db logs`. A local `pxt service logs` exits 1 and names the log file in the error.

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

Annotation is a stored column. Assignment is a computed column. Optional is `T | None`. Primary key is `pxt.Column(..., primary_key=True)`; `add_update_route` matches rows by it, so the request body carries `id` even though `inputs` does not list it. Indexes on the model: `__indexes__ = [pxt.EmbeddingIndex(...)]`.

Already have FastAPI: after schema update, `ingest.bind('my_app')` then `app.include_router(ingest)`. Or define the `fastapi.FastAPI` object in `app.py` and `include_router()` each router there; `pxt service update` then serves that one application. Call `pxt.get_table()` inside custom handlers, which are `def`, not `async def`: an async handler that calls Pixeltable blocks the event loop. A provider function has no public imperative call from a handler; use the provider's own SDK there. [workflows.md](references/workflows.md).

RAG, views, and search: [workflows.md](references/workflows.md). Do not add Hugging Face or spaCy unless the user asked.

## Apps vs notebooks

- **Apps:** `app.py` + `pxt schema update` + `pxt service update`. Indexes on the model.
- **Notebooks / REPL:** `pxt.create_table()`, `add_computed_column()`, `add_embedding_index()`. The appendix below uses that form.

## Where to look

| Need | Open |
|------|------|
| `pxt schema`, `pxt service`, inspect | [cli.md](references/cli.md) |
| Types, views, UDFs, UDAs, tool calling | [core-api.md](references/core-api.md) |
| Provider import, arguments, and output shape | [providers.md](references/providers.md) |
| Serving, FastAPIRouter, routes | [workflows.md](references/workflows.md) |
| Wrong stack | [anti-patterns.md](references/anti-patterns.md) |

Add video, audio, agents, or a UI by editing `app.py`. A view is either a filter (`base=Docs.where(...)`) or an iterator (`frame_iterator`, `audio_splitter`, `document_splitter`, `video_splitter`, `string_splitter`, `list_iterator`, `tile_iterator`). Check `pixeltable.functions` before writing a UDF. Start from `pxt service example` or `pxt schema example`. Do not invent a second `pxt schema update` path.

## API traps

| Wrong | Correct |
|-------|---------|
| `openai.vision(...)` or `{'image_url': {'url': col}}` | `vision` is the only deprecated function in `pixeltable.functions`. In `chat_completions`, pass the column: `{'type': 'image_url', 'image_url': t.image}`. In `responses`, `{'type': 'input_image', 'image_url': t.image}` |
| `chat_completions(..., temperature=0)` | Extra request parameters go in `model_kwargs={...}`, not as top-level keywords. Anthropic `messages` still requires `max_tokens=` as its own argument |
| `from pixeltable.iterators import ...` | The whole `pixeltable.iterators` package is a deprecated shim (`FrameIterator`, `VideoSplitter`, `DocumentSplitter`, `StringSplitter`, `AudioSplitter`, `TileIterator`). Import the function from `pixeltable.functions.*` -- e.g. `from pixeltable.functions.video import frame_iterator` |
| `similarity(query)` | `similarity(string=query)`. Also `image=` / `audio=` / `video=` / `document=` / `vector=`; `idx=` picks among several indexes on one column |
| Re-run with `if_exists='ignore'` to fix logic | App: edit the expression, `pxt schema update`, then `pxt recompute my_app/docs summary -f`: the update keeps the old values. Notebook: `t.alter_computed_column(summary=...)`, which recomputes it and its dependents |
| Change a column's type in place (`T` to `T \| None`, stored to computed, `stored=`) | `UNSUPPORTED`: the whole update applies nothing, and `--allow-destructive` does not help. A computed column: rename it (one `--allow-destructive -f` pass). A stored column: renaming drops its data, so declare `T \| None` up front |
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
| `uuid.astype(pxt.String)` | `uuid.to_string()` (`from pixeltable.functions.uuid import to_string`). `astype` does not cast UUID to String |
| `json_col.astype(pxt.String)` on a dict or list | Fails at insert: `Expected string, got dict`. Serialize with `pxtf.json.dumps(json_col)` |

Extract the field (`.text`, `.choices[0].message.content`). The field is Json holding a string: `.astype(pxt.String)` it before concatenating or embedding.

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

Extra request parameters go in `model_kwargs={...}`, not as keywords on `chat_completions`.

Views: `document_splitter`, `frame_iterator` (from `pixeltable.functions.video`), `string_splitter`, `audio_splitter`. Notebook indexes: `t.add_embedding_index('content', embedding=embed_fn, if_exists='ignore')`.

Query: `t.where(...).select(...).collect()`. Similarity: `t.content.similarity(string=query)`. In `@pxt.query`, alias as `score=sim`.

UDFs are recorded as a module path relative to the project root (`app.excerpt`).

Always `if_exists='ignore'` on notebook `create_*` / `add_*`. Failed cells: `t.recompute_columns('summary', errors_only=True)`, or `pxt recompute my_project/documents summary --errors-only -f`. `string_splitter` / `document_splitter(..., separators='sentence')` need spaCy. Embedding indexes need `.using(...)` to bind model arguments.

## Resources

- [Quickstart](https://docs.pixeltable.com/overview/quick-start)
- [CLI](https://docs.pixeltable.com/platform/cli)
- [Developer MCP](https://github.com/pixeltable/mcp-server-pixeltable-developer)
- [Cloud MCP](https://pixeltable.com/developers/mcp-cloud) (optional; reads and operates Cloud, see [cli.md](references/cli.md))
- [Docs](https://docs.pixeltable.com/llms-full.txt)
