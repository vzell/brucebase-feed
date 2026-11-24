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
    "dependent-name":  { "count": 36,  "files": ["gignote/*.html", "gallery/*.html", "eye/*.html", "media/*.html", "recnote/*.html"] },
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
    "gallery:1967-11-00-first-presbyterian-church-freehold-nj": {
      "dependent-name": {
        "event": "gig:1967-11-00a-first-presbyterian-church-freehold-nj",
        "expected": "gallery:1967-11-00a-first-presbyterian-church-freehold-nj"
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
  `tagTables` with 1.4, `songTags` and `venueTags` with 1.5,
  `dependent-name` with 1.6, the `mb` kind `date-differs` with 1.7,
  `mbWorks` with 1.8, `dependent-name` rows of `onstage`, `offstage` and
  `soundcheck` pages with 1.9, `relationTags` with 1.10; all keep format 1).
- **`run`:**
  - `date`: the day of the run the data comes from. The data is that day's
    wiki;
  - `bruceparser`: the pipeline's version;
  - `commit`: the pipeline's commit (a `+` means it had local changes);
  - `feed`: the version of the script that wrote the file.
- **`reports`:**
  - `count`: how many pages carry that kind;
  - `files`: the pipeline's report files it was read from (in its run
    directory; not published); for `dependent-name` the run's pages it read
    and the stage page report.
- **`pages`:** keyed by the wiki's page name, without the site and without
  `/noredirect/true`. A page has one entry per kind it carries:
  - `no-parent`, `no-tags`, `files-no-images`: `true`;
  - `no-parent`: a dated `media`, `news`, `recnote`, `story`, `gignote`,
    `gallery` or `eye` page with no link in its breadcrumbs (since feed 1.8.3,
    BruceParser 0.38.0; until then only the dated subtype pages of the first
    four were checked);
  - `first-letter`: the missing tag (`"a"` … `"z"`, `"0-9"`);
  - `dependent-title`:
    - `title`: the page's own title;
    - `eventTitle`: the title it should agree with, its event page's or, when
      no `event` is given, its parent page's (the year page's title for that
      parent);
    - `event`: present when the report names the event page.
    A page whose title differs from `eventTitle` only in blanks (a double
    blank in the wiki's title) is not listed (feed.sh 1.8.1): a browser shows
    the two the same. BruceParser's reports keep it.
  - `date-parts`: the date tags that do not fit the date in the page's name,
    `{ "missing": [tag, …], "wrong": [tag, …] }`, either list left out when
    empty. Only pages that are no event (`gig`, `nogig`, `interview`,
    `rehearsal`, `recording`: ShowAllEntityData checks those itself), whose
    name holds a date, and which have tags at all (`no-tags` covers the rest);
  - `files-no-images`: the page is tagged `files` and shows no image. The
    wiki's tag means "files attached to this page": a page that only shows
    pictures stored under other pages (a retail page's covers) has no tag by
    rule, and a page with its own pictures but no tag is not in the feed
    either. Since feed 1.8.3 (BruceParser 0.37.0) news, recnote and relation
    pages are checked too;
  - `dependent-name` (since feed 1.6): a show's dependent page (`gignote`,
    `gallery`, `eye`, `media`, `recnote`) whose name differs from the show
    its breadcrumbs name (their last link, when it is a page of an event
    category):
    - `event`: that show;
    - `expected`: the name a page of the show would have, the page's prefix
      and the show's name after its colon, cut to 60 characters.

    A name fits when it is the expected one, or that with a `-1`/`-2` of two
    pages of one date, or with the `dl-` of a download's note
    (`recnote:dl-2020-04-08-…`), or when Wikidot's cut of a long name
    explains it (one name starts with the other, the cut one has 55
    characters or more; a `-1`/`-2` page cut to 60 minus its suffix). Left
    out: `news` (named by the article's date) and `story` (its own
    prefixes). Since feed 1.9 (BruceParser 0.40.0) also `onstage`,
    `offstage` and `soundcheck` pages: they have no breadcrumbs, so their
    show is the one show page of their date (a day with several shows, or
    none, is left out).
- **`anchors`** (since feed 1.2): the year-page anchors of the shows the
  feed names, a show being a page of an event category (`gig`, `nogig`,
  `interview`, `rehearsal`, `recording`, `nobruce`) in `pages` or a
  `dependent-title`'s or `dependent-name`'s `event`. Keyed `"<year page>#<anchor>"` as the year
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
    link one entry), `not-anchor` (the link is no year-page anchor) or,
    since feed 1.7, `date-differs` (none of those, and the anchor's date is
    outside the event's date: compared as far as both go, an anchor's `00`
    left out, a span `begin - end` holding it fits);
  - `link`: the link as MusicBrainz has it, without the site;
  - `date`: the event's date, when it has one (`begin - end` for a span);
  - `page`, `heading`: for `shared` and `date-differs`, the entry the link
    reaches;
  - `dateDiffers`: for `shared`, the event's date is not the entry's (likely
    this link is the wrong one);
  - `others`: for `shared`, the other events' MBIDs;
  - `candidates`: the wiki's entries of the event's date (for `no-entry`
    also its anchor with a letter), each `{anchor, page, heading}`, with
    `likely: true` when the heading names the event's place.
- **`mbWorks`** (since feed 1.8): the artist's MusicBrainz works whose
  Brucebase link is doubtful, keyed by work MBID. From the run's
  `search/work_linked_to_artist.xml` (search.sh -e work) against the run's
  pages and `song.txt` (`mb-works.awk`). One entry per work, the first kind
  that applies:
  - `kind`: `no-target` (the link's page is not on the wiki, or its anchor
    is on none of the page's ids), `shared` (two or more works link one
    `song:` page) or `title` (the work's title, and none of its aliases, is
    the linked song's title, compared loosely: case, quotes, dashes, the
    ellipsis, blanks, and the wiki's `( - Artist - )` after a cover's title).
    A studio-sessions row (`stats:…#anchor`) is checked for its page and
    anchor only;
  - `link`: the link as MusicBrainz has it, without the site;
  - `work`, `dis`: the work's title and its disambiguation (when it has one);
  - `missing`: for `no-target`, `page` or `anchor`;
  - `title`: for `shared` and `title`, the song page's title;
  - `others`: for `shared`, the other works, each `{mbid, title}`;
  - `candidates`: for `no-target`, where the link likely should go, each
    `{link}` with `likely: true` on one: for a page, the pages of its
    category carrying its anchor that share the longest start with its name
    (the shortest of them likely: a renamed page); for an anchor, its page's
    anchors that start like it, or it like them (likely when only one).
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
- **`relationTags`** (since feed 1.10): a relation page → its own tags that
  count as its name (`"relation:donald-dunn": ["duckdunn"]`): every tag of the
  page that at least half of the shows listing it on their On Stage tab carry
  and that is no other relation page's name (a band's tag on a member's page
  is not kept). A stage name the wiki uses, as here, is one. Built by
  BruceParser's `relation-tags.awk` from the run's On stage, relation and
  event pages.
