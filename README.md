# Lunar

[![Deploy](https://github.com/pureugong/lunar/actions/workflows/deploy.yml/badge.svg)](https://github.com/pureugong/lunar/actions/workflows/deploy.yml)

Korean lunar calendar (음력) to solar date converter with ICS file generation. Enter a recurring lunar date and download a calendar file with all matching solar dates through 2050.

**Live demo:** https://pureugong.github.io/lunar/

## Tech Stack

- Vue 3 + Vite
- [holiday-kr](https://www.npmjs.com/package/holiday-kr) — lunar/solar date conversion
- [ics](https://www.npmjs.com/package/ics) — ICS calendar file generation
- Bootstrap 4 (CDN)

## Quick Start

```bash
npm install
npm run dev      # dev server
npm run build    # production build
```
