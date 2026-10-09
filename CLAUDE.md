# CLAUDE.md — evzin coming soon / evzinhomecare.gr

Στατικό site (HTML/CSS/JS, χωρίς build step). Hosting: **Cloudflare Pages**, από το `main` του repo, με root directory τη ρίζα του repo.

## Τι κάναμε (τρέχουσα κατάσταση)

Το site δείχνει μόνο 10 υπηρεσίες και τα 10 αντίστοιχα άρθρα. Οι υπόλοιπες 16 υπηρεσίες και τα 16 άρθρα τους είναι κρυμμένα, όχι διαγραμμένα.

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

**Κρυμμένες υπηρεσίες (16):** `aimolepsia`, `exetaseis-ouron`, `enesi`, `emvoliasmos`, `anarofisi`, `ourokathitiras-foley`, `rinogastrikos-levin`, `flevokentisi`, `endoflevia-oros`, `endoflevia-port`, `enteriki-sitisi`, `rammata`, `dialeippon-katheterismos`, `episkepsi-nosilevti`, `sakcharo-insulini`, `oxygonotherapia`.

**Κρυμμένα άρθρα (16)** (αντιστοιχούν στις παραπάνω υπηρεσίες): `article-levin`, `article-aimolepsia`, `article-flevokentisi`, `article-oxygono`, `article-exetaseis-ouron`, `article-enesi`, `article-emvoliasmos`, `article-anarofisi`, `article-ourokathitiras`, `article-endoflevia-orou`, `article-endoflevia-port`, `article-enteriki-sitisi`, `article-rammata`, `article-dialeippon-katheterismos`, `article-episkepsi-nosilevti`, `article-saccharo-insulini`.

### Πού έγιναν αλλαγές

1. **`services.html`** και **`index.html`** (πλέγμα υπηρεσιών της αρχικής): οι κρυμμένες κάρτες είναι τυλιγμένες σε `<!-- HIDDEN: ... -->`.
2. **`area-*.html`** (25 σελίδες περιοχών): οι λίστες υπηρεσιών (`service-item` και `service-link`) έχουν τις κρυμμένες γραμμές μέσα σε `<!-- HIDDEN: ... -->`.
3. **`sitemap.xml`**: οι κρυμμένες υπηρεσίες και άρθρα είναι σε σχόλια.
4. **`articles.html`**: οι κρυμμένες κάρτες άρθρων είναι σε `<!-- HIDDEN: ... -->`.
5. **`_redirects`** (νέο, στη ρίζα): κάθε κρυμμένο URL υπηρεσίας (`/service-*`) κάνει 302 redirect στο `/services`, και κάθε κρυμμένο άρθρο (`/article-*`) στο `/articles`. Ισχύει με και χωρίς `.html`.
   - **Σημείωση:** μην βάζεις `!` μετά το status code (π.χ. `302!`). Το Cloudflare Pages το απορρίπτει και αγνοεί όλο το αρχείο (`Parsed 0 valid redirect rules`). Αυτό συνέβη σε ένα deploy.

Τα αρχεία `service-*.html` και `article-*.html` των κρυμμένων σελίδων **δεν έχουν διαγραφεί ή αλλαχθεί**.

## Πώς να τεστάρεις

**Τοπικά (χωρίς redirects):** Τα `_redirects` δεν τρέχουν στο `npx serve`, οπότε τα redirects τεστάρονται μόνο στο deploy.
```
npx serve -l 8000
```
Άνοιξε `http://localhost:8000/services`, `http://localhost:8000/articles`, `http://localhost:8000/` και μία σελίδα περιοχής, π.χ. `area-kentro`. Πρέπει να φαίνονται μόνο οι 10 υπηρεσίες και τα 10 άρθρα.

**Στο deploy (Cloudflare Pages):**
- Άνοιξε `https://evzinhomecare.gr/services` και έλεγξε ότι φαίνονται 10 κάρτες.
- Άνοιξε ένα κρυμμένο URL, π.χ. `https://evzinhomecare.gr/service-aimolepsia`, και έλεγξε ότι καταλήγει στο `/services`.
- Άνοιξε ένα κρυμμένο άρθρο, π.χ. `https://evzinhomecare.gr/article-levin`, και έλεγξε ότι καταλήγει στο `/articles`.
- Έλεγξε ένα κρυμμένο `.html` URL, π.χ. `/service-aimolepsia.html`.
- Έλεγξε ένα ορατό URL, π.χ. `/service-katakliseis`, που πρέπει να μένει ανοιχτό.

## Πώς επαναφέρεις μια υπηρεσία

1. **Στη λίστα:** στα `services.html`, `index.html`, `articles.html` και `area-*.html`, βρες `<!-- HIDDEN: <a href="service-SLUG" ...` και αφαίρεσε το `<!-- HIDDEN: ` και το ` -->` γύρω από τη γραμμή ή την κάρτα. Αν υπάρχει κάρτα σε πολλαπλές γραμμές, αφαίρεσε το wrapper από αυτήν. Αν δουλεύεις με το Claude, μπορείς να του πεις "επανέφερε το SLUG".
2. **Στο sitemap:** αφαίρεσε το `<!-- HIDDEN: ` και το ` -->` γύρω από το `<url>` του SLUG (υπηρεσίας ή άρθρου).
3. **Redirect:** σβήσε τις δύο γραμμές του SLUG (με και χωρίς `.html`) από το `_redirects`, για υπηρεσία ή άρθρο.
4. Κάνε commit και push. Το Cloudflare Pages κάνει deploy αυτόματα.

Αν θες να επαναφέρεις τα πάντα, το `git log` έχει το commit πριν από αυτή την αλλαγή. `git revert <hash>` αναιρεί όλα μαζί.

## Σημειώσεις
- Οι κρυμμένες σελίδες είναι ακόμα προσβάσιμες μόνο μέσω redirect. Το Google μπορεί να τις έχει ήδη στο index, και ο redirect 302 δεν τις αφαιρεί άμεσα.
- Οι ρυθμίσεις του site (canonical, favicon, sitemap) είναι στα αρχεία HTML. Όποια νέα σελίδα προστεθεί, πρέπει να μπει και στο `sitemap.xml`.
