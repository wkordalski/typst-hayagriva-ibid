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

---

### Side topic (bug in Hayagriva or CSL file)
¹ Original `polish-legal.csl` checked for `ibid` and `ibid-with-locator` in the wrong order (`ibid-with-locator` matcher first, which catches both `ibid` and `ibid-with=locator`).
Changed the order, so `ibid` is checked first (`ibid` matcher catches `ibid` but not `ibid-with-locator`). **OR** the CSL interpretation in Hayagriva is wrong.

Compare:

```rust
TestPosition::Ibid => props.speculative.ibid == IbidState::Ibid,
TestPosition::IbidWithLocator => {
    props.speculative.ibid.is_ibid_with_locator()  // matches!(self, Self::IbidWithLocator | Self::Ibid)
}
```

and:

> Whenever position=”ibid-with-locator” tests true, position=”ibid” also tests true. And whenever position=”ibid” or position=”near-note” test true, position=”subsequent” also tests true.
