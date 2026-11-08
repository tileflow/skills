# Verification

Verify the requested outcome, not merely file creation. Run the commands from the project root
through the locally installed CLI, and pass `--json` where a command offers it.

## Checks, in order

1. `npx tileflow validate --json`. Correct the reported `path` before anything else.
2. `npx tileflow capture <scene> --json`. The first run can download the pinned browser. On success
   it writes a PNG and a receipt under `.tileflow/captures/` and prints their paths. Open the image
   and read the receipt for failed resources. A warning that capture requested remote resources is
   normal for a map on hosted data.
3. For a claim about appearance, look at the image, once per theme the map ships. A produced file
   does not show that the area is right, that labels are legible against their background, or that
   the user's data stands out from the basemap.
4. For application behavior such as popups or layout, check the running application. A map scene does
   not test them.

Read a command's `--help` before using an option you have not seen in the guide.

## When capture fails

Read `code` and `diagnostics`. Do not change the map to make a capture pass when `validate` succeeds
and the failure concerns a resource, identity, or browser step, such as `WORLD_RESOLUTION_FAILED` or
`MAP_LOAD_FAILED`. Those are environment or release problems. Instead:

1. Start `npx tileflow preview --no-open --port <free port>` as a background process.
2. Wait for the printed ready line, then run
   `npx tileflow inspect session --url http://127.0.0.1:<port> --json`. Before the server is up it
   fails with `WORKBENCH_UNAVAILABLE`; its default origin is port 3333. `status: "ready"` means the
   map compiled, not that it rendered.
3. If you have a browser tool, open the printed URL, confirm the map draws, read the console, and
   take a screenshot. Without one, ask the user to open the URL.
4. Stop the preview.
5. Report that capture failed, with its code, and that the rendered result was checked in preview
   or was not checked. Never describe an unrendered map as visually verified.

## Other tools

- `npx tileflow inspect features --layers <names> --center <lng,lat> --zoom <z> --json` shows which
  vector features exist near a camera.
- `npx tileflow explain` shows why the compiler produced a layer.
- `npx tileflow compare` and `npx tileflow visual` compare captures. Compare a baseline read-only;
  do not update one to obtain a pass.

## What to report

State the package versions, which checks ran, the paths of any images and receipts, and anything that
depended on the network or stayed unverified.
