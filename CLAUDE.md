# CLAUDE.md — evzin coming soon / evzinhomecare.gr

Στατικό site (HTML/CSS/JS, χωρίς build step). Hosting: **Cloudflare Pages**, από το `main` του repo, με root directory τη ρίζα του repo.

## Τι κάναμε (τρέχουσα κατάσταση)

Το site δείχνει μόνο 10 υπηρεσίες. Οι υπόλοιπες 16 είναι κρυμμένες, όχι διαγραμμένες.

**Κρατιούνται ορατές (10):**
- `service-katakliseis` — Περιποίηση κατακλίσεων
- `service-traumata` — Αλλαγή τραυμάτων εγκαυμάτων
- `service-eileostomia` — Φροντίδα και αλλαγή κολοστομίας
- `service-ypokysmos` — Υποκλισμός
- `service-atomiki-ygieini` — Ατομική υγιεινή
- `service-banio-klinis` — Μπάνιο επί κλίνης
- `service-banio-wc` — Μπάνιο στο WC
- `service-kathariotita-pana` — Σωματική καθαριότητα και αλλαγή πάνας
- `service-paketa-ygieinis` — Μηνιαία πακέτα ατομικής υγιεινής
- `service-zotika-simeia` — Παρακολούθηση ζωτικών σημείων

**Κρυμμένες (16):** `aimolepsia`, `exetaseis-ouron`, `enesi`, `emvoliasmos`, `anarofisi`, `ourokathitiras-foley`, `rinogastrikos-levin`, `flevokentisi`, `endoflevia-oros`, `endoflevia-port`, `enteriki-sitisi`, `rammata`, `dialeippon-katheterismos`, `episkepsi-nosilevti`, `sakcharo-insulini`, `oxygonotherapia`.

### Πού έγιναν αλλαγές

1. **`services.html`** και **`index.html`** (πλέγμα υπηρεσιών της αρχικής): οι κρυμμένες κάρτες είναι τυλιγμένες σε `<!-- HIDDEN: ... -->`.
2. **`area-*.html`** (25 σελίδες περιοχών): οι λίστες υπηρεσιών (`service-item` και `service-link`) έχουν τις κρυμμένες γραμμές μέσα σε `<!-- HIDDEN: ... -->`.
3. **`sitemap.xml`**: οι κρυμμένες υπηρεσίες είναι σε σχόλια.
4. **`_redirects`** (νέο, στη ρίζα): κάθε κρυμμένο URL (με και χωρίς `.html`) κάνει 302 redirect στο `/services`. Η σύνταξη `302!` σημαίνει ότι το redirect εφαρμόζεται και αν υπάρχει το αρχείο.

Τα αρχεία `service-*.html` των κρυμμένων υπηρεσιών **δεν έχουν διαγραφεί ή αλλαχθεί**.

## Πώς να τεστάρεις

**Τοπικά (χωρίς redirects):** Τα `_redirects` δεν τρέχουν στο `npx serve`, οπότε τα redirects τεστάρονται μόνο στο deploy.
```
npx serve -l 8000
```
Άνοιξε `http://localhost:8000/services`, `http://localhost:8000/` και μία σελίδα περιοχής, π.χ. `area-kentro`. Πρέπει να φαίνονται μόνο οι 10 υπηρεσίες.

**Στο deploy (Cloudflare Pages):**
- Άνοιξε `https://evzinhomecare.gr/services` και έλεγξε ότι φαίνονται 10 κάρτες.
- Άνοιξε ένα κρυμμένο URL, π.χ. `https://evzinhomecare.gr/service-aimolepsia`, και έλεγξε ότι καταλήγει στο `/services`.
- Έλεγξε ένα κρυμμένο `.html` URL, π.χ. `/service-aimolepsia.html`.
- Έλεγξε ένα ορατό URL, π.χ. `/service-katakliseis`, που πρέπει να μένει ανοιχτό.

## Πώς επαναφέρεις μια υπηρεσία

1. **Στη λίστα:** στα `services.html`, `index.html` και `area-*.html`, βρες `<!-- HIDDEN: <a href="service-SLUG" ...` και αφαίρεσε το `<!-- HIDDEN: ` και το ` -->` γύρω από τη γραμμή ή την κάρτα. Αν υπάρχει κάρτα σε πολλαπλές γραμμές, αφαίρεσε το wrapper από αυτήν. Αν δουλεύεις με το Claude, μπορείς να του πεις "επανέφερε το SLUG".
2. **Στο sitemap:** αφαίρεσε το `<!-- HIDDEN: ` και το ` -->` γύρω από το `<url>` του SLUG.
3. **Redirect:** σβήσε τις δύο γραμμές του SLUG (με και χωρίς `.html`) από το `_redirects`.
4. Κάνε commit και push. Το Cloudflare Pages κάνει deploy αυτόματα.

Αν θες να επαναφέρεις τα πάντα, το `git log` έχει το commit πριν από αυτή την αλλαγή. `git revert <hash>` αναιρεί όλα μαζί.

## Σημειώσεις
- Οι κρυμμένες σελίδες είναι ακόμα προσβάσιμες από το site, μόνο μέσω redirect. Το Google μπορεί να τις έχει ήδη στο index, και ο redirect 302 δεν τις αφαιρεί άμεσα.
- Οι ρυθμίσεις του site (canonical, favicon, sitemap) είναι στα αρχεία HTML. Όποια νέα σελίδα προστεθεί, πρέπει να μπει και στο `sitemap.xml`.
