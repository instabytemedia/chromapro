# ChromaPro

> Elevate Design With AI

ChromaPro is an advanced version of ChromaChroma, focusing on professional designers and artists. It incorporates AI-driven color theory to generate unique color palettes based on trending design styles and hashtags. This variant includes features like color palette export in various formats and integration with popular design tools.

## Features

- AI-driven color palette generation
- Trending design style integration
- Color palette export

## Tech Stack

- **Framework:** Next.js 14 (App Router)
- **Database:** Supabase (PostgreSQL)
- **Auth:** Supabase Auth
- **Styling:** Tailwind CSS
- **Language:** TypeScript

## Getting Started

1. Clone this repository
2. Copy `.env.example` to `.env.local` and fill in your credentials
3. Run `npm install`
4. Run `npm run dev`

## Project Structure

```
├── app/                  # Next.js App Router pages
├── components/           # React components
├── lib/                  # Utilities and helpers
├── supabase/            # Database schema
└── INSTRUCTIONS.md      # Detailed build guide for AI assistants
```

## Database

This project uses 2 main entities:
- **ColorPalette**: A generated color palette
- **MoodBoard**: A mood board created by a user

## Build Instructions

For detailed step-by-step build instructions, see [`INSTRUCTIONS.md`](./INSTRUCTIONS.md).

This file contains comprehensive guidance for building this project with AI coding assistants like Claude Code, Cursor, or Windsurf.

---

*Generated with [Claudery](https://claudery.io) - AI-powered blueprint generator*
