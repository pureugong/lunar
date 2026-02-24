# CLAUDE.md

## Project Overview

**Lunar** is a Korean lunar calendar ICS file generator. It converts lunar (음력) dates to solar (양력/Gregorian) dates and generates downloadable `.ics` calendar files covering all matching dates up to 2050. The app is hosted as a static GitHub Pages site at `pureugong.github.io/lunar/`.

- **Version**: 1.0.2
- **Author**: Yunho, Jo (pureugong)
- **License**: UNLICENSED

## Tech Stack

- **Framework**: Vue.js 2.x (single-file components, `.vue` files)
- **Bundler**: Webpack 3 with Babel (ES2015/env presets)
- **UI**: Bootstrap 4 (loaded via CDN in `index.html`)
- **Language**: JavaScript (ES6), Korean UI text throughout

### Key Dependencies

| Package | Purpose |
|---------|---------|
| `vue` | Frontend framework |
| `holiday-kr` | Lunar-to-solar date conversion for Korean calendar |
| `ics` | Generates ICS (iCalendar) format event files |
| `faker` | Random filename generation for downloaded `.ics` files |

## Repository Structure

```
lunar/
├── index.html              # Entry HTML (loads Bootstrap CDN, mounts Vue app)
├── package.json            # Dependencies and scripts
├── webpack.config.js       # Webpack 3 config (entry: src/main.js → dist/build.js)
├── table.js                # Lunar calendar lookup table (1901–2050, raw data)
├── dist/
│   └── build.js            # Production bundle (committed, served by GitHub Pages)
├── src/
│   ├── main.js             # Vue app entry point
│   ├── App.vue             # Root component (displays version, renders EventForm)
│   └── components/
│       └── EventForm.vue   # Core component: date input, lunar→solar conversion, ICS generation
└── README.md               # Minimal readme
```

## Build & Development Commands

```bash
# Install dependencies
npm install

# Start dev server with hot reload (localhost:8080)
npm run dev

# Production build (outputs to dist/build.js)
npm run build
```

- `npm run dev` → `webpack-dev-server --inline --hot`
- `npm run build` → `cross-env NODE_ENV=production webpack --progress --hide-modules`

There are no test scripts, linter configurations, or CI pipelines configured.

## Architecture & Data Flow

1. `index.html` loads Bootstrap from CDN and includes `dist/build.js`
2. `src/main.js` creates a Vue instance mounting `App.vue` to `#app`
3. `App.vue` renders a header card with the version (read from `package.json`) and the `<event-form>` component
4. `EventForm.vue` contains all business logic:
   - User enters a lunar event name and selects a date
   - `holiday-kr.getSolar()` converts the lunar date to solar for each year up to 2050
   - Both regular and leap month (윤달) dates are handled
   - The `ics` library generates ICS content as a data URI
   - `faker` generates a random whimsical filename for the download

## Code Conventions

- **Language**: Korean is used for all user-facing strings (placeholder text, labels, badge text). Code variables and comments are in English.
- **Component style**: Vue 2 options API with `data` as a function, `computed` properties, and `methods`.
- **Date handling**: Dates are stored as `YYYY-MM-DD` strings, split and joined manually. The `padder()` utility zero-pads numbers to 2 digits.
- **No TypeScript**: Pure JavaScript throughout.
- **No linting/formatting tools**: No ESLint, Prettier, or editorconfig is configured.
- **Committed build artifacts**: `dist/build.js` is committed to the repo for GitHub Pages deployment. After changing source code, run `npm run build` and commit the updated `dist/build.js`.

## Important Notes for AI Assistants

- The `dist/build.js` file is a minified production bundle — do not edit it directly. Edit source files in `src/` and rebuild.
- The `table.js` file contains a hardcoded lunar calendar lookup table spanning 1901–2050. Each row is an array of 12 values representing month lengths. Values of 3–6 indicate leap months.
- The app only supports dates from 1900 to 2050 due to the lunar table data range.
- Bootstrap 4 is loaded from CDN in `index.html`, not installed via npm. Vue components use Bootstrap CSS classes directly.
- The webpack config mocks `dns` and `net` Node modules (required by the `ics` dependency).
- There are no tests. When modifying logic, manually verify by running `npm run dev` and testing in the browser.
- Commit messages in this repo are informal and short. Follow the existing style.
