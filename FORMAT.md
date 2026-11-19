# bb-feed.json, format 1

One JSON object. Its keys are sorted, so two runs diff line by line.

```json
{
  "format": 1,
  "run": { "date": "2026-11-15", "bruceparser": "0.21.0", "commit": "3fc392e", "feed": "1.3" },
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
  },
  "mb": {
    "f60ec13f-0c48-4c64-b80d-b4a226207017": {
      "kind": "no-entry", "link": "2023#120923", "date": "2023-09-12",
      "candidates": [
        { "anchor": "2023#120923a", "page": "interview:2023-09-12a-stone-hill-farm-colts-neck-nj",
          "heading": "2023-09-12 - STONE HILL FARM, COLTS NECK, NJ" },
        { "anchor": "2023#120923b", "page": "nogig:2023-09-12b-ppg-paints-arena-pittsburgh-pa",
          "heading": "2023-09-12 - PPG PAINTS ARENA, PITTSBURGH, PA", "likely": true }
      ]
    }
  }
}
```

- **`format`:** a reader that does not know the number ignores the whole
  file. A change a format-1 reader would misread raises it. A new kind does
  not: a reader ignores a kind it does not know (`date-parts` and
  `files-no-images` came with feed 1.1, `anchors` with 1.2, `mb` with 1.3,
  `tagTables` with 1.4, `songTags` and `venueTags` with 1.5; all keep
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
- **`mb`** (since feed 1.3): the artist's MusicBrainz events whose Brucebase
  link is doubtful, keyed by event MBID. From the run's
  `search/event_linked_to_artist.org` (search.sh) against
  `brucebase-event.txt`. Each:
  - `kind`: `no-entry` (the link's anchor is not on the wiki; most often the
    wiki writes that date with a letter now), `shared` (two or more events
    link one entry) or `not-anchor` (the link is no year-page anchor);
  - `link`: the link as MusicBrainz has it, without the site;
  - `date`: the event's date, when it has one;
  - `page`, `heading`: for `shared`, the entry the link reaches;
  - `dateDiffers`: for `shared`, the event's date is not the entry's (likely
    this link is the wrong one);
  - `others`: for `shared`, the other events' MBIDs;
  - `candidates`: the wiki's entries of the event's date (for `no-entry`
    also its anchor with a letter), each `{anchor, page, heading}`, with
    `likely: true` when the heading names the event's place.
- **`tagTables`** (since feed 1.4): the lookup tables of ShowAllEntityData's
  expected-tag checks of a show, hand-edited in BruceParser
  (`brucebase/tag-tables.json`, seeded from BruceBaseParser's tables), so a
  change reaches the userscript with the next feed, without a release:
  - `tours`: `[{name, tag, ranges: [[from, to], …]}, …]`, dates
    `YYYY-MM-DD`, both ends included. One tag can have several entries (two
    legs named differently), and two tours can cover one date;
  - `usStates`, `caProvinces`: abbreviation → name (`"NJ": "New Jersey"`);
  - `countryRegions`: a country as a title writes it → the region tags it
    comes with (`"England": ["unitedkingdom", "europe"]`);
  - `venueAlias`, `relationAlias`, `eStreetDotted`, `songCombos`: a lower-case
    name → the tag the wiki uses for it; a `venueAlias` value `null` means
    "no tag is expected for this name";
  - `expectedRegions` (since feed 1.4.1): the regions of `countryRegions` a
    show's place is checked for (`["europe", "oceania"]`; the wiki tags the
    others too rarely);
  - `placeTags` (since feed 1.4.1): a city or country as a title writes it →
    the tags the wiki uses for it, the first named when one is missing
    (`"West Germany": ["germany"]`, `"The Netherlands": ["thenetherlands",
    "netherlands"]`). Without an entry the name's own slug is expected.
- **`songTags`** (since feed 1.5): a song page → the tags the shows that
  link it carry, the first the most carried (`"song:born-to-run": ["btr",
  "borntorun"]`): every tag of the song page's own that is carried (almost)
  only by shows whose setlist links the song (at least 90 % of the shows
  carrying it). Only songs whose tags at least 90 % of their shows carry are
  kept (the wiki tags the others unevenly). Built by BruceParser's
  `tag-maps.awk` from the run's event and song pages.
- **`venueTags`** (since feed 1.5): a venue page → its tag, as a one-element
  list (`"venue:stone-pony-asbury-park-nj": ["stonepony"]`): the venue page's
  own tag that best fits the shows whose "Venue" link names it. Every venue
  linked by a show.
