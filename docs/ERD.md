# Entity Relationship Diagram - ChromaPro

> **Auto-generated** from your idea analysis
> **Entities:** 2

---

## Visual Diagram

```mermaid
erDiagram
    profiles {
        uuid id PK
        text username UK
        text display_name
        text avatar_url
        timestamptz created_at
        timestamptz updated_at
    }

    colorpalettes {
        uuid id PK
        uuid user_id FK
        uuid id
        timestamptz created_at
        timestamptz updated_at
        uuid user_id FK
        text name
        jsonb colors
        timestamptz created_at
        timestamptz updated_at
    }

    moodboards {
        uuid id PK
        uuid user_id FK
        uuid id
        timestamptz created_at
        timestamptz updated_at
        uuid user_id FK
        text name
        uuid color_palette_id FK
        timestamptz created_at
        timestamptz updated_at
    }

    %% Relationships
    profiles ||--o{ colorpalettes : owns
    profiles ||--o{ moodboards : owns
    colorpalettes ||--o{ users : "A user can have multiple color palettes"
    moodboards ||--o{ users : "A user can have multiple mood boards"
    moodboards }o--|| colorpalettes : "A mood board is associated with one color palette"
```

---

## Entity Details

### ColorPalette
> A generated color palette

**Fields:**
  - `id`: uuid (required) - Primary key
  - `created_at`: datetime (required) - Creation timestamp
  - `updated_at`: datetime (required) - Last update timestamp
  - `user_id`: uuid (required) - Owner user ID
  - `name`: string (required) - Name of the color palette
  - `colors`: json (required) - Array of colors in the palette

**Relationships:**
  - one_to_many → **User**: A user can have multiple color palettes

### MoodBoard
> A mood board created by a user

**Fields:**
  - `id`: uuid (required) - Primary key
  - `created_at`: datetime (required) - Creation timestamp
  - `updated_at`: datetime (required) - Last update timestamp
  - `user_id`: uuid (required) - Owner user ID
  - `name`: string (required) - Name of the mood board
  - `color_palette_id`: uuid (required, indexed) - Foreign key referencing the ColorPalette entity

**Relationships:**
  - one_to_many → **User**: A user can have multiple mood boards
  - many_to_one → **ColorPalette**: A mood board is associated with one color palette

---

## Notes

- All entities have standard fields: `id`, `user_id`, `created_at`, `updated_at`
- `PK` = Primary Key, `FK` = Foreign Key, `UK` = Unique Key
- Copy the Mermaid code block to visualize in any Mermaid-compatible tool
- Relationships: `||--o{` = one-to-many, `||--||` = one-to-one, `}o--o{` = many-to-many
