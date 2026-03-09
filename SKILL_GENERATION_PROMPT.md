# Skill File Generation Prompt

Use this prompt when generating any new skills file. It enforces documentation
verification before writing a single line of content.

---

## The Prompt

```
You are writing a reference skills file for: **[TOPIC]**

Output file: skills/[category]/[filename].md

---

### PHASE 1 — RESEARCH FIRST, WRITE NOTHING YET

Before writing any content, you MUST:

1. Fetch the official documentation for this topic. Preferred sources in order:
   - docs.oracle.com (official Oracle product docs)
   - Oracle GitHub repositories (oracle-samples, oracle)
   - Oracle blog / thatjeffsmith.com for SQLcl-specific topics
   - Oracle LiveLabs

2. For each major claim you intend to make, identify the exact doc page and
   section that supports it.

3. List every URL you successfully fetched and read.

4. If a doc page returns 404 or you cannot find authoritative documentation
   for a specific claim, note it explicitly — do NOT invent or infer.

---

### PHASE 2 — VERIFICATION CHECKLIST

Before writing the file, answer each question:

- [ ] What is the minimum product version required for each feature covered?
- [ ] Does the file include an explicit `## Oracle Version Notes (19c vs 26ai)` section?
- [ ] What are the exact command/flag/parameter names as shown in the docs?
- [ ] What environment variables are documented? (List only what you found, not what seems plausible)
- [ ] What are the documented limitations or unsupported scenarios?
- [ ] Is there anything I "know" about this topic that I could NOT find confirmed
      in the fetched docs? → If yes, either omit it or mark it explicitly with
      "> ⚠️ Unverified: not found in official docs — confirm before use."

---

### PHASE 3 — WRITE THE FILE

Only now write the skills file. Rules:

- Include ONLY content you found in the docs you fetched in Phase 1.
- Every command, flag, syntax example, and parameter name must match the docs exactly.
- If a feature works differently across versions, say so explicitly with version numbers.
- Add a section named `## Oracle Version Notes (19c vs 26ai)` before `## Sources` when version differences are relevant.
- In that section, identify 19c baseline behavior and 26ai-capable features with compatibility notes.
- Do not extrapolate. If the docs show flag X works one way, do not assume
  related flag Y works similarly unless the docs say so.
- If you are uncertain about anything, use the warning callout:
  > ⚠️ Unverified: [state what is uncertain] — check official docs before use.
- Do not invent environment variable names, command flags, API methods, table
  names, or configuration keys. If you did not read it in the docs, it does not go in.

---

### PHASE 4 — SELF-REVIEW BEFORE SUBMITTING

After writing, re-read the file and for each code block or command example ask:

1. "Did I find this exact syntax in the official docs?" → If no, remove or flag it.
2. "Did I invent any parameter names, env vars, or flags?" → If yes, remove them.
3. "Are version requirements stated where relevant?" → If no, add them.
4. "Did I include `## Oracle Version Notes (19c vs 26ai)` when version-specific features appear?" → If no, add it.
5. "Is there anything here that sounds plausible but I cannot point to a doc source for?" → Remove or flag it.

Report which doc URLs you used as sources at the bottom of the file under a
`## Sources` section.
```

---

## Example Usage

Replace `[TOPIC]`, `[category]`, and `[filename]` with specifics:

```
You are writing a reference skills file for: **SQLcl MCP Server**
Output file: skills/sqlcl/sqlcl-mcp-server.md

[paste the full prompt above]
```

---

## Red Flags — Signs a Generated File Needs Review

If a generated file contains any of the following, treat it as unverified and
re-run with this prompt:

- Environment variable names not found in official docs
- Command flags not shown in the product's CLI help or docs
- Version numbers stated without a citation
- Tool/API names that sound plausible but produce no search results
- Configuration keys or JSON properties not shown in official examples
- Any claim about default behaviour that isn't explicitly stated in the docs

---

## Notes for Reviewers

When reviewing a generated file, spot-check by picking 3-5 specific claims
(commands, flags, env vars, version numbers) and verifying each one against
the linked sources at the bottom of the file. If any claim has no source, flag
it for removal or verification before merging.
