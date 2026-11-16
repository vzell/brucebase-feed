# brucebase-feed

`bb-feed.json` lists a few data problems on [Brucebase](https://brucebase.wikidot.com),
the Bruce Springsteen wiki. Each one can only be seen from the whole wiki, not
from a single page. The userscript
[ShowAllEntityData](https://github.com/vzell/mb-userscripts) reads this file
and marks the affected pages and rows on brucebase.wikidot.com, so editors see
the problem where they can fix it. It reads it at most once a day and never
asks the wiki for anything extra.

The file comes from an offline run over a copy of the whole wiki. Every
finding names the date of that run; the wiki may have been fixed since.

## What it holds

| Kind              | A page is listed when                                                        |
|-------------------|------------------------------------------------------------------------------|
| `no-parent`       | it should have a parent page (a story, a news item, …) but has none          |
| `dependent-title` | its title differs from the title of its event, or its parent page            |
| `no-tags`         | it has no tag at all                                                         |
| `first-letter`    | it lacks the single-letter tag of its title's first character (`a`, `0-9`)   |

The exact layout is in [FORMAT.md](FORMAT.md).

## Thanks

The data is Brucebase's: **Danny van der Linden** and the editors of
[brucebase.wikidot.com](https://brucebase.wikidot.com), who have documented
every show, rehearsal and recording for decades. This feed only points at the
places where the wiki disagrees with itself, so they can be found and fixed.

## Updates

A new file arrives after a full run, usually every few weeks. Each commit
names its run's date and the count of every kind.
