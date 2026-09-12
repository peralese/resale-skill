# Resale research and selling skills

Three self-contained skills live in `skills/`. No existing project instructions or skill conventions were present when they were created. The project is managed in Git. The workspace's `.agents` and `.codex` directories were read-only during implementation, so Codex discovery setup remains a separate installation step.

## Routing

| Intent | Item | Skill |
| --- | --- | --- |
| Investigation / considering a purchase | Book or non-book | `resale-item-investigation` |
| Sell an owned book | Book | `sell-books-online` |
| Sell an owned item on eBay | Non-book | `sell-general-items-ebay` |

Each skill uses standard `name` and `description` YAML frontmatter. Automatic invocation remains enabled by default. Each contains its own evidence and economics rules so it can be copied independently. There are no runtime scripts, external skill dependencies, fixed fee tables, or extra reference files. Research requires current web access and photographs; unavailable data must be reported rather than fabricated.

## Installation and use

### ChatGPT Project (primary use)

Upload all three `skills/*/SKILL.md` files as project sources, using their containing skill names to distinguish them. If your upload interface makes identical filenames confusing, name the uploaded copies `resale-item-investigation.md`, `sell-books-online.md` and `sell-general-items-ebay.md`. Their frontmatter also identifies each workflow. Paste the following into the project's instructions:

> Use the uploaded resale workflow documents for applicable requests. For Investigation or purchase-evaluation intent, read and follow resale-item-investigation for either books or non-books. For Sell intent on an owned book, read and follow sell-books-online. For Sell intent on an owned non-book item, read and follow sell-general-items-ebay. Choose by intent first, then item type; do not generate a sales listing during investigation unless subsequently requested. Follow the selected document's evidence, calculation and output requirements. Ask only for material missing information and complete supported work meanwhile. Use current web research and direct evidence links; disclose unavailable browsing or market data. Do not purchase, publish, contact sellers or make external changes without explicit authorization. If a workflow document is unavailable, say which one is missing rather than pretending to have read it.

Attach the actual item photos to each request. This is manual project-source setup, not a claim that uploading Markdown installs native skills or guarantees automatic routing. Re-upload the changed source when updating a skill. [Official project documentation](https://learn.chatgpt.com/docs/projects) explains shared files and project instructions.

### Codex CLI

The documented repository discovery location is `.agents/skills/<name>/SKILL.md`; symlinked skill folders are supported. This session cannot write to `.agents`, so the complete source implementation is staged in `skills/` and **Codex automatic discovery still requires installation**. In a writable checkout, run these commands from the project root (they do not force-replace existing links):

```bash
mkdir -p .agents/skills
for name in resale-item-investigation sell-books-online sell-general-items-ebay; do
  ln -s "../../skills/$name" ".agents/skills/$name"
done
```

If a destination already exists, inspect it before replacing anything. On systems without symlink support, copy each complete skill directory into `.agents/skills/` and update those copies when the sources change. Start Codex at the project root and check skill discovery; restart if necessary. Explicit invocation uses `$resale-item-investigation`, `$sell-books-online` or `$sell-general-items-ebay`. [Official skill documentation](https://learn.chatgpt.com/docs/build-skills).

### Claude Code

Project entries already exist at `.claude/skills/<name>` as relative symlinks to the source folders. These resolve to standard project skill paths, so no additional copying is needed in this workspace if symlinks are preserved. Check discovery in Claude Code; this session has not run a live Claude selection test. If a transfer does not preserve symlinks, copy the three source directories into `.claude/skills/`. Invoke `/resale-item-investigation`, `/sell-books-online` or `/sell-general-items-ebay`, or use natural-language intent. [Official Claude Code skill documentation](https://code.claude.com/docs/en/skills).

## Manual test prompts

Attach appropriate item photographs when running each prompt:

1. `Investigation: Asking price is $15. I may resell it or keep it. Photos attached.` Expected: investigation even if the photo shows a book; confidence, linked comparables, conditional economics and purchase ceiling; no full listing.
2. `Sell book: Purchase price was $5. Condition is Very Good. Photos attached.` Expected: exact-edition checks, evidence-based condition, three-marketplace comparison, Amazon limitations, a primary venue, list-price/format/Best Offer strategy, offer floor and walk-away guidance, and one buyer-facing listing with seller advice separated.
3. `Sell item: Purchase price was $20. Condition appears excellent. Photos attached.` Use a non-book. Expected: eBay strategy, supported condition, net/profit, title at most 80 characters and appropriate packing.

Useful variations: omit acquisition cost (continue research, withhold profit); include a hidden accepted-offer comparable (final price unknown); show damage despite “excellent” (explain and disclose); omit copyright page (request the specific evidence, do not infer first edition).

### Book-listing boundary example

Synthetic writing-only regression case; the following book details are invented test inputs, not market evidence. Input: `Sell book: Purchase price $5. Exterior Very Good. Supplied cover/spine evidence reads Garden Notes, Alex Reed, hardcover, with light shelf wear. No interior, copyright-page or jacket evidence. Draft the listing using only these facts.`

Expected: omit ISBN, publication year, edition/printing, signed status, jacket status and interior claims. Keep any verification checks outside section 10. In a full Sell response, retain the three-marketplace comparison and practical pricing/offer guidance; this excerpt tests only the listing boundary.

```markdown
### 10. Ready-to-use listing

**Title:** Garden Notes by Alex Reed Hardcover

**Condition:** Pre-owned hardcover with light shelf wear to the exterior.

**Description:** Garden Notes by Alex Reed in hardcover. The exterior shows light shelf wear. Please review the photographs closely for condition details.

**Item specifics:**
- Author: Alex Reed
- Title: Garden Notes
- Format: Hardcover

### Seller Verification Needed

Check the interior for writing, highlighting, stains, missing pages and ownership marks before publication. Provide the title and copyright pages to establish publication and edition details.

### Shipping recommendation

Measure and weigh the packed book before quoting postage; use rigid packaging and protect the corners.
```

Acceptance checks: section 10 contains none of `if confirmed`, `should be confirmed before listing`, `verify before listing`, `seller should check`, `interior condition should be confirmed`, `TODO` or placeholders. Its title is at most 80 characters. Unknown specifics are omitted and known wear is disclosed. Seller verification and shipping are separate peer sections. Negative cases such as `First edition, if confirmed` or `Interior condition should be confirmed before listing` inside section 10 must fail this check; omit the claim or move the verification task outside the listing. This example and the skill's final review rule guard the boundary; they do not guarantee every future model response will comply.

## Validation

Run from the project root using the available system validator (Python 3 and PyYAML required):

```bash
for skill in skills/*/; do
  python3 /home/peralese/.codex/skills/.system/skill-creator/scripts/quick_validate.py "$skill" || exit 1
done
```

All three returned `Skill is valid!`. Additional inspection checked nonempty YAML names/descriptions, directory/name agreement, unfinished scaffold markers, and resolution of the three Claude links. A requirement review confirmed the requested inputs, output sections, book/non-book boundaries, market-evidence distinctions, Amazon limitations, calculation assumptions and listing constraints. These checks validate structure and instruction coverage, not real-world appraisal quality or live automatic selection; the prompts above provide manual behavioral tests. No purchases, listings, messages or account changes were made.

## File tree

```text
README.md
skills/
  resale-item-investigation/SKILL.md
  sell-books-online/SKILL.md
  sell-general-items-ebay/SKILL.md
.claude/skills/
  resale-item-investigation -> ../../skills/resale-item-investigation
  sell-books-online -> ../../skills/sell-books-online
  sell-general-items-ebay -> ../../skills/sell-general-items-ebay
```

The protected `.agents/` and `.codex/` directories and Git metadata are omitted from the implementation tree.
