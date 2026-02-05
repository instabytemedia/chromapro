# 02 - Database Schema

> **Agent:** Database Agent 🗄️
> **Goal:** Set up Supabase database with all tables and policies

---

## ⚠️ CRITICAL: Avoid Duplicate Fields

**NEVER define these fields in your custom schema - they are automatically added:**
- `id` - UUID Primary Key (auto-generated)
- `user_id` - UUID Foreign Key to auth.users (auto-generated)
- `created_at` - TIMESTAMPTZ (auto-generated)
- `updated_at` - TIMESTAMPTZ (auto-generated)

**Only define entity-SPECIFIC fields in your tables.**

Example of WRONG vs RIGHT:
```sql
-- WRONG: Duplicate fields!
CREATE TABLE items (
  id UUID PRIMARY KEY,       -- Already auto-added!
  user_id UUID,              -- Already auto-added!
  created_at TIMESTAMPTZ,    -- Already auto-added!
  name TEXT,
  id UUID,                   -- DUPLICATE! Will cause error!
  created_at TIMESTAMPTZ     -- DUPLICATE! Will cause error!
);

-- RIGHT: Only custom fields
CREATE TABLE items (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  -- Only custom fields below:
  name TEXT NOT NULL,
  description TEXT,
  status TEXT NOT NULL DEFAULT 'active'
);
```

---

## Context

**Product:** ChromaPro
**Entities:** ColorPalette, MoodBoard

### Data Model

**ColorPalette**: A generated color palette
- `id`: UUID (required) - Primary key
- `created_at`: TIMESTAMPTZ (required) - Creation timestamp
- `updated_at`: TIMESTAMPTZ (required) - Last update timestamp
- `user_id`: UUID (required) - Owner user ID
- `name`: TEXT (required) - Name of the color palette
- `colors`: JSONB (required) - Array of colors in the palette
Relationships:
  - one_to_many → User: A user can have multiple color palettes

**MoodBoard**: A mood board created by a user
- `id`: UUID (required) - Primary key
- `created_at`: TIMESTAMPTZ (required) - Creation timestamp
- `updated_at`: TIMESTAMPTZ (required) - Last update timestamp
- `user_id`: UUID (required) - Owner user ID
- `name`: TEXT (required) - Name of the mood board
- `color_palette_id`: UUID (required, indexed) - Foreign key referencing the ColorPalette entity
Relationships:
  - one_to_many → User: A user can have multiple mood boards
  - many_to_one → ColorPalette: A mood board is associated with one color palette

---

## Instructions

### 1. Profiles Table

Create a **profiles** table that syncs with auth.users:

**Requirements:**
- Primary Key: `id` (UUID) - Reference to auth.users(id)
- Fields: username (unique), display_name, avatar_url, bio
- Timestamps: created_at, updated_at
- RLS: Everyone can view profiles, only owner can update
- Auto-Trigger: Automatically create profile on user signup

**Important:**
- ON DELETE CASCADE for foreign key to auth.users
- Trigger Function for auto-create on signup
- Public read access, owner-only write access

### 2. Entity Tables

Create a table for each entity:

**1. ColorPalette Table (`colorpalettes`):**
Purpose: A generated color palette

```sql
CREATE TABLE colorpalettes (
  -- Standard fields
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),

  -- Entity-specific fields
  name TEXT NOT NULL -- Name of the color palette,
  colors JSONB NOT NULL -- Array of colors in the palette

);

-- Indexes for performance
CREATE INDEX colorpalettes_user_id_idx ON colorpalettes(user_id);
CREATE INDEX colorpalettes_created_at_idx ON colorpalettes(created_at DESC);



-- RLS Policies (Granular)
ALTER TABLE colorpalettes ENABLE ROW LEVEL SECURITY;

-- SELECT: Users can only view their own records
CREATE POLICY "colorpalettes_select_own"
  ON colorpalettes FOR SELECT
  USING (auth.uid() = user_id);

-- INSERT: Users can only create records for themselves
CREATE POLICY "colorpalettes_insert_own"
  ON colorpalettes FOR INSERT
  WITH CHECK (auth.uid() = user_id);

-- UPDATE: Users can only update their own records
CREATE POLICY "colorpalettes_update_own"
  ON colorpalettes FOR UPDATE
  USING (auth.uid() = user_id)
  WITH CHECK (auth.uid() = user_id);

-- DELETE: Users can only delete their own records
CREATE POLICY "colorpalettes_delete_own"
  ON colorpalettes FOR DELETE
  USING (auth.uid() = user_id);
```

Relationships:
- one_to_many to User (foreign key: user_id)

**2. MoodBoard Table (`moodboards`):**
Purpose: A mood board created by a user

```sql
CREATE TABLE moodboards (
  -- Standard fields
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),

  -- Entity-specific fields
  name TEXT NOT NULL -- Name of the mood board,
  color_palette_id UUID NOT NULL -- Foreign key referencing the ColorPalette entity

);

-- Indexes for performance
CREATE INDEX moodboards_user_id_idx ON moodboards(user_id);
CREATE INDEX moodboards_created_at_idx ON moodboards(created_at DESC);
CREATE INDEX moodboards_color_palette_id_idx ON moodboards(color_palette_id);


-- RLS Policies (Granular)
ALTER TABLE moodboards ENABLE ROW LEVEL SECURITY;

-- SELECT: Users can only view their own records
CREATE POLICY "moodboards_select_own"
  ON moodboards FOR SELECT
  USING (auth.uid() = user_id);

-- INSERT: Users can only create records for themselves
CREATE POLICY "moodboards_insert_own"
  ON moodboards FOR INSERT
  WITH CHECK (auth.uid() = user_id);

-- UPDATE: Users can only update their own records
CREATE POLICY "moodboards_update_own"
  ON moodboards FOR UPDATE
  USING (auth.uid() = user_id)
  WITH CHECK (auth.uid() = user_id);

-- DELETE: Users can only delete their own records
CREATE POLICY "moodboards_delete_own"
  ON moodboards FOR DELETE
  USING (auth.uid() = user_id);
```

Relationships:
- one_to_many to User (foreign key: user_id)
- many_to_one to ColorPalette (foreign key: colorpalette_id)


### 3. Updated-At Automation

Create a **Trigger Function** that automatically sets `updated_at`:
- Function Name: `update_updated_at()`
- Trigger: BEFORE UPDATE on all tables
- Logic: SET NEW.updated_at = now()

Apply the trigger to all tables (profiles + entities).

### 4. Storage Setup

Create a **Storage Bucket** for file uploads:
- Name: "uploads"
- Visibility: Private
- Folder Structure: `{user_id}/{filename}`

Storage Policies:
- Users can only upload to their own folder
- Users can only read/delete their own files
- No public access

Implement folder-based security with storage.foldername().

### 5. Type Safety

Generate TypeScript types from the schema:
- Use Supabase CLI: `supabase gen types typescript`
- Export to: `lib/database.types.ts`
- Use the types for type-safe queries

---

## Validation Checklist

Verify that:
- [ ] profiles table exists with RLS
- [ ] Auto-create Profile Trigger works
- [ ] ColorPalette table exists with correct fields
- [ ] MoodBoard table exists with correct fields
- [ ] All tables have RLS enabled
- [ ] Foreign Keys correctly set
- [ ] Indexes created for performance
- [ ] updated_at Trigger on all tables
- [ ] Storage Bucket configured
- [ ] TypeScript Types generated

**Test:**
- Signup new user → Profile automatically created
- Create Entity record → Only visible to owner
- Update record → updated_at automatically set
- Upload file → Only possible in own folder

---

## Troubleshooting

**RLS Issues:**
- Check if policies correctly use auth.uid()
- Test with SQL Editor: `SELECT auth.uid()`
- Policies must have USING and WITH CHECK

**Foreign Key Errors:**
- Check CASCADE on user_id
- Entities with relationships: Correct order when creating

**Storage Issues:**
- Folder structure must follow {user_id}/ pattern
- Policies use storage.foldername() for user isolation

---

**Continue to:** `03-auth.md`
