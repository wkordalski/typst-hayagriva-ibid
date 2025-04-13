This repository demonstrates a bug in Typst citation supplements handling.

Currently, Typst treats all supplements as equal, and therefore, for two consecutive citations
to the same entry but with different supplements Typst emits "ibid" for second citation
(although "ibid-with-locator" should be emitted).

Proposed fix is to use `plain_text()` of supplement to distinguish different supplements.
This approach has a downside that supplements with different formatting but the same text inside
are treated equal. Especially two image-only supplements are treated equal.
Fortunatelly, there is a simple workaround for this --- one needs to add textual "identifier" to supplement:
`@citation-entry[#image("path.png")#text(size: 0pt)[supplement-identifier]]`

The zero-sized text is not present in final PDF, but allows for differentiating supplements.

The repository contains:
- `main.typ`
- `bib.yml`
- `sty.csl` (fixed¹ `polish-legal.csl` from <https://github.com/citation-style-language/styles>)
- `img-a.png` and `img-b.png` (example images)
- `output-main.pdf` (compiled `main.typ` with Typst from `main` branch)
- `output-fix.pdf` (compiled `main.typ` with Typst with the fix applied)


¹ It turned out that this is not `polish-legal.csl` bug, but Hayagriva bug.
PR with fix is [here](https://github.com/typst/hayagriva/pull/301).
When it gets merged, I will undo the `polish-legal.csl` "fix".
