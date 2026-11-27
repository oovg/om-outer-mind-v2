# landing/

Items that have just come through the airlock: cleaned, checked by the local model, and approved by a person. They're safe to publish but not yet shaped into entries.

`om land` has Claude shape each one into `public/` (following `../CLAUDE.md`), then moves it to `.done/<item>/` with a receipt. The airlock reads those receipts for the write-back, after checking each one.

Sites never read this folder. (`outbox/`, if it ever appears, is reserved for private sends: not this.)
