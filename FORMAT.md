# bb-feed.json, format 1

One JSON object. Its keys are sorted, so two runs diff line by line.

```json
{
  "format": 1,
  "run": { "date": "2026-11-15", "bruceparser": "0.20.0", "commit": "3fc392e", "feed": "1.2" },
  "site": "https://brucebase.wikidot.com",
  "credit": "The data is Brucebase (brucebase.wikidot.com, Danny van der Linden and its editors); …",
  "reports": {
    "date-parts":      { "count": 3,   "files": ["REPORTING/MISSING-date-parts-in-tags-cleanedup.org"] },
    "dependent-title": { "count": 39,  "files": ["REPORTING/…", "REPORTING/…"] },
    "files-no-images": { "count": 32,  "files": ["REPORTING/MISMATCH-images-files-tag.org"] },
    "first-letter":    { "count": 10,  "files": ["REPORTING/MISSING-first-character-tag-in-title.txt"] },
    "no-parent":       { "count": 105, "files": ["REPORTING/NO-PARENT-detail-pages.org"] },
    "no-tags":         { "count": 38,  "files": ["REPORTING/MISSING-tags.txt"] }
  },
  "pages": {
    "gignote:1967-08-00c-unknown-location-howell-nj": {
      "dependent-title": {
        "title": "1967-08-00 Private Property, Howell, NJ",
        "event": "gig:1967-08-00c-unknown-location-howell-nj",
        "eventTitle": "1967-08-00 Unknown Location, Howell, NJ"
      }
    },
    "files:pass-1989":                   { "files-no-images": true },
    "news:1978-08-22-madison-square-garden-new-york-city-ny": { "date-parts": { "missing": ["22"], "wrong": ["23"] } },
    "ott:i-want-you":                    { "first-letter": "i" },
    "relation:chubby-checker":           { "no-tags": true },
    "story:story-1972-01-21-neptune-nj": { "no-parent": true }
  },
  "anchors": {
    "1972#000472b": ["recording:1972-04-00b-mediasound-studios-new-york-city-ny",
                     "recording:1972-05-00-mediasound-studios-new-york-city-ny"],
    "1972#000672b": ["gig:1972-06-00-unknown-location-point-pleasant-nj"]
  }
}
```

- **`format`:** a reader that does not know the number ignores the whole
  file. A change a format-1 reader would misread raises it. A new kind does
  not: a reader ignores a kind it does not know (`date-parts` and
  `files-no-images` came with feed 1.1, `anchors` with 1.2; both keep
  format 1).
- **`run`:**
  - `date`: the day of the run the data comes from. The data is that day's
    wiki;
  - `bruceparser`: the pipeline's version;
  - `commit`: the pipeline's commit (a `+` means it had local changes);
  - `feed`: the version of the script that wrote the file.
- **`reports`:**
  - `count`: how many pages carry that kind;
  - `files`: the pipeline's report files it was read from (in its run
    directory; not published).
- **`pages`:** keyed by the wiki's page name, without the site and without
  `/noredirect/true`. A page has one entry per kind it carries:
  - `no-parent`, `no-tags`, `files-no-images`: `true`;
  - `first-letter`: the missing tag (`"a"` … `"z"`, `"0-9"`);
  - `dependent-title`:
    - `title`: the page's own title;
    - `eventTitle`: the title it should agree with, its event page's or, when
      no `event` is given, its parent page's (the year page's title for that
      parent);
    - `event`: present when the report names the event page.
  - `date-parts`: the date tags that do not fit the date in the page's name,
    `{ "missing": [tag, …], "wrong": [tag, …] }`, either list left out when
    empty. Only pages that are no event (`gig`, `nogig`, `interview`,
    `rehearsal`, `recording`: ShowAllEntityData checks those itself), whose
    name holds a date, and which have tags at all (`no-tags` covers the rest);
  - `files-no-images`: the page is tagged `files` and shows no image. Not the
    other way round: a venue or retail page with images and no `files` tag
    is the wiki's rule, not an error.
- **`anchors`** (since feed 1.2): the year-page anchors of the shows the
  feed names, a show being a page of an event category (`gig`, `nogig`,
  `interview`, `rehearsal`, `recording`, `nobruce`) in `pages` or a
  `dependent-title`'s `event`. Keyed `"<year page>#<anchor>"` as the year
  page's link has it (`"1975#101175"`, `"1949-64#230949"`), the value always
  a sorted array of page names: an anchor two entries share carries every
  page on it, a reader picks one by the entry's date. A year listing links
  the anchor, not the page, so a reader finds the show through it. A show on
  no year page has no anchor (feed.sh warns).
