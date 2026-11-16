# bb-feed.json, format 1

One JSON object. Its keys are sorted, so two runs diff line by line.

```json
{
  "format": 1,
  "run": { "date": "2026-11-15", "bruceparser": "0.18.0", "commit": "3fc392e", "feed": "1.0" },
  "site": "https://brucebase.wikidot.com",
  "credit": "The data is Brucebase (brucebase.wikidot.com, Danny van der Linden and its editors); …",
  "reports": {
    "dependent-title": { "count": 39,  "files": ["REPORTING/…", "REPORTING/…"] },
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
    "ott:i-want-you":                    { "first-letter": "i" },
    "relation:chubby-checker":           { "no-tags": true },
    "story:story-1972-01-21-neptune-nj": { "no-parent": true }
  }
}
```

- **`format`:** a reader that does not know the number ignores the whole
  file. A change a format-1 reader would misread raises it.
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
  - `no-parent`, `no-tags`: `true`;
  - `first-letter`: the missing tag (`"a"` … `"z"`, `"0-9"`);
  - `dependent-title`:
    - `title`: the page's own title;
    - `eventTitle`: the title it should agree with, its event page's or, when
      no `event` is given, its parent page's (the year page's title for that
      parent);
    - `event`: present when the report names the event page.
