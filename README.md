# Issue

It seems like we're not resolving aliased packages correctly when using subpath imports of an aliased package.

This import should work when using `deno.json`:

```ts
import "@supabase/functions-js/edge-runtime.d.ts";
```

```json
{
  "imports": {
    "@supabase/functions-js": "jsr:@supabase/functions-js@^2.4.4",
    "@supabase/supabase-js": "jsr:@supabase/supabase-js@^2.48.0"
  }
}
```

# Steps to reproduce

1. `pnpm i`
2. `supabase start`
3. `supabase functions serve`
4.

```sh
curl -X POST http://127.0.0.1:54321/functions/v1/queue_worker \
  -H "Content-Type: application/json" \
  -d '{"name": "Bob"}'
```

Error:

```
worker boot error: failed to create the graph: Relative import path "@supabase/functions-js/edge-runtime.d.ts" not prefixed with / or ./ or ../ and not in import map from "file:///Users/jgoux/Code/supabase/deno-json-are-u-ok/supabase/functions/queue_worker/index.ts"
    at file:///Users/jgoux/Code/supabase/deno-json-are-u-ok/supabase/functions/queue_worker/index.ts:6:8
worker boot error: failed to create the graph: Relative import path "@supabase/functions-js/edge-runtime.d.ts" not prefixed with / or ./ or ../ and not in import map from "file:///Users/jgoux/Code/supabase/deno-json-are-u-ok/supabase/functions/queue_worker/index.ts"
    at file:///Users/jgoux/Code/supabase/deno-json-are-u-ok/supabase/functions/queue_worker/index.ts:6:8
InvalidWorkerCreation: worker boot error: failed to create the graph: Relative import path "@supabase/functions-js/edge-runtime.d.ts" not prefixed with / or ./ or ../ and not in import map from "file:///Users/jgoux/Code/supabase/deno-json-are-u-ok/supabase/functions/queue_worker/index.ts"
    at file:///Users/jgoux/Code/supabase/deno-json-are-u-ok/supabase/functions/queue_worker/index.ts:6:8
    at async UserWorker.create (ext:sb_user_workers/user_workers.js:139:15)
    at async Object.handler (file:///root/index.ts:156:22)
    at async respond (ext:sb_core_main_js/js/http.js:197:14) {
  name: "InvalidWorkerCreation"
}
```

It works as expected both in VSCode Deno LSP and using Deno CLI:

```sh
deno run --allow-all supabase/functions/queue_worker/index.ts
curl -X POST http://127.0.0.1:8888 \
  -H "Content-Type: application/json" \
  -d '{"name": "Bob"}'
```
