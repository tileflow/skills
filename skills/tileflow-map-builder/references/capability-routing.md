# Capability routing

`docs-manifest.json` is the decision contract. Do not keep a second capability list here.

1. Match the request against `capabilities[].userIntents` and `environments`, then read the pages in
   `locations.markdown`.
2. Use `maturity`:
   - `current`: supported.
   - `experimental`: usable; tell the user it may change.
   - `planned` or `unsupported`: not available in this release, unless the check in step 3 shows
     otherwise.
3. Use `implementationMode`:
   - `native`: Tileflow directly owns the path.
   - `composite`: follow only the documented Tileflow-plus-application recipe.
   - `external`: name the separately owned service; do not imply Tileflow supplies it.
   - `manual`: tell the user about the required human step.
   - `unavailable`: not an implementation path, unless the check below shows otherwise.
4. Treat `evidenceGap` as what remains unproven, and tell the user if it affects the request.

The manifest and `llms.txt` can disagree, for example when a guide exists for a capability the
manifest lists as unavailable. Resolve it with evidence, in this order:

1. Fetch the guide. A missing page means the capability is not available.
2. Check that the guide's package has a published version: `npm view <package> dist-tags --json`.
3. Install that exact version and confirm the API exists in the installed types or `tileflow
language manifest --json`.

If all three hold, use the capability and tell the user about the disagreement. Otherwise explain
the boundary and stop, or propose an application-owned alternative without calling it Tileflow
support.

Examples: prefer snippets classified `executable-verified`. Treat `syntax-checked` and
`copyable-unverified` as starting points that you must run. `illustrative` snippets are not
instructions.

Select the environment deliberately. Keep browser code free of server credentials, and distinguish a
trusted-server capability from an unauthenticated browser request.
