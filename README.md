# Video Reach Map

A static web page that shows where a video has been watched. Schools submit a Google Form; the responses are plotted on a map with live totals and a breakdown chart.

## How it works

- `index.html` is the whole site (no build step). It is served with GitHub Pages.
- Data comes from a Google Apps Script web app that returns the form-response sheet as JSON.
- A second Apps Script function (`onFormSubmit`) geocodes each new address into `lat`/`lng` and records a `geocode_quality` value. Rows marked `VAGUE` (only matched to a country or state) are not plotted.
- The page cleans the data on load: viewer counts are parsed (implausible values count as 0), swapped or generic organisation names are fixed, and popups are rendered as plain text.
- If the live feed fails, the page shows the last saved copy (kept in the browser) with a notice.

## Notes

- The Google Maps key in `index.html` is a browser key. It must stay restricted to this site's domain in Google Cloud.
- The geocoding key is stored in Apps Script Script Properties (`GEO_API_KEY`), not in this repository.
