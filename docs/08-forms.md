# 08 - Forms & Validation

> **Agent:** Forms Agent 📝
> **Goal:** Create forms with react-hook-form + Zod

---

## Context

**Product:** ChromaPro
**Entities with Forms:** ColorPalette, MoodBoard

---

## Instructions

### 1. Check Dependencies

Ensure the following dependencies are installed:
- `react-hook-form` - Form State Management
- `@hookform/resolvers` - Zod Integration

If not present, install them.

**Important:**
- Zod should already be installed from Setup
- Schemas should already exist in lib/schemas/ (from Phase 06)


### 2. ColorPalette Form

Create **ColorPaletteForm Component** (`components/colorpalette/ColorPaletteForm.tsx`):

**Props Interface:**
- `colorpalette?`: ColorPalette Object (if provided = Edit Mode)
- `onSuccess?`: Callback after successful submit

**Functionality:**
- Client Component ("use client")
- react-hook-form with zodResolver
- Use CreateColorPaletteSchema for Validation
- Distinguish Create vs Edit Mode (based on colorpalette prop)
- useCreateColorPalette() Hook for Create
- useUpdateColorPalette(id) Hook for Edit
- Loading State during Submit
- Error Handling with Alert
- After Success: onSuccess Callback + Router Push to List + Router Refresh

**Form Fields:**
- `id` (text) - Required: Primary key
- `created_at` (text) - Required: Creation timestamp
- `updated_at` (text) - Required: Last update timestamp
- `user_id` (text) - Required: Owner user ID
- `name` (text) - Required: Name of the color palette
- `colors` (text) - Required: Array of colors in the palette

**Layout:**
- Card Container
- CardHeader: Title ("New ColorPalette" or "Edit ColorPalette")
- CardContent: Form Fields (space-y-4)
- CardFooter: Actions (justify-end gap-2)
  - Cancel Button (variant="outline", onClick → router.back())
  - Submit Button (type="submit", disabled during Loading)

**Field Layout (per field):**
- Container with space-y-2
- Label with htmlFor
- Input with register()
- Error Message (if errors.fieldName)

**Important:**
- register() with valueAsNumber for Number fields
- defaultValues from colorpalette prop (for Edit Mode)
- Proper Error Display under each field

---

Create **New ColorPalette Page** (`app/(app)/colorpalettes/new/page.tsx`):

**Functionality:**
- Server Component for Auth Check
- Redirect to /login if not authenticated
- Render ColorPaletteForm Component

**Layout:**
- Container with max-w-2xl + py-8
- Centered Form Layout



### 3. MoodBoard Form

Create **MoodBoardForm Component** (`components/moodboard/MoodBoardForm.tsx`):

**Props Interface:**
- `moodboard?`: MoodBoard Object (if provided = Edit Mode)
- `onSuccess?`: Callback after successful submit

**Functionality:**
- Client Component ("use client")
- react-hook-form with zodResolver
- Use CreateMoodBoardSchema for Validation
- Distinguish Create vs Edit Mode (based on moodboard prop)
- useCreateMoodBoard() Hook for Create
- useUpdateMoodBoard(id) Hook for Edit
- Loading State during Submit
- Error Handling with Alert
- After Success: onSuccess Callback + Router Push to List + Router Refresh

**Form Fields:**
- `id` (text) - Required: Primary key
- `created_at` (text) - Required: Creation timestamp
- `updated_at` (text) - Required: Last update timestamp
- `user_id` (text) - Required: Owner user ID
- `name` (text) - Required: Name of the mood board
- `color_palette_id` (text) - Required: Foreign key referencing the ColorPalette entity

**Layout:**
- Card Container
- CardHeader: Title ("New MoodBoard" or "Edit MoodBoard")
- CardContent: Form Fields (space-y-4)
- CardFooter: Actions (justify-end gap-2)
  - Cancel Button (variant="outline", onClick → router.back())
  - Submit Button (type="submit", disabled during Loading)

**Field Layout (per field):**
- Container with space-y-2
- Label with htmlFor
- Input with register()
- Error Message (if errors.fieldName)

**Important:**
- register() with valueAsNumber for Number fields
- defaultValues from moodboard prop (for Edit Mode)
- Proper Error Display under each field

---

Create **New MoodBoard Page** (`app/(app)/moodboards/new/page.tsx`):

**Functionality:**
- Server Component for Auth Check
- Redirect to /login if not authenticated
- Render MoodBoardForm Component

**Layout:**
- Container with max-w-2xl + py-8
- Centered Form Layout


---

## Validation Checklist

Verify for EACH Entity:
- [ ] Form Component created
- [ ] New Page created with Auth Check
- [ ] react-hook-form integrated
- [ ] Zod Validation works
- [ ] Create Mode works
- [ ] Edit Mode works (when colorpalette is passed)
- [ ] Loading State during Submit
- [ ] Error Messages are displayed
- [ ] Redirect after Success

**Test per Entity:**
1. **Create Test:**
   - Go to /[entity]s/new
   - Leave field empty → Validation Error displayed
   - Fill all fields → Submit
   - Redirected to list
   - New entry appears in list

2. **Validation Test:**
   - Required fields empty → Error
   - Number field with text → Error (if applicable)
   - Correct input → No Error

3. **Edit Test (if Detail Page exists):**
   - Open Entity Detail
   - Click Edit
   - Fields are pre-filled
   - Change value → Submit
   - Changes saved

---

## Troubleshooting

**Form submitted but nothing happens:**
- handleSubmit correctly passed to form onSubmit?
- async/await in onSubmit Handler?
- Errors being logged?

**Validation not working:**
- zodResolver correctly imported?
- Schema correctly passed to zodResolver?
- Schema exported from lib/schemas?

**Register not working:**
- Field Name exactly like in Schema?
- Input has {...register("fieldName")} spread?
- For Number: valueAsNumber Option?

**defaultValues being ignored:**
- useForm with defaultValues option?
- colorpalette prop being passed?
- Object spread correct?

**No redirect after Submit:**
- router.push() called?
- router.refresh() for SSR Update?
- No Error in try/catch?

**TypeScript Errors:**
- CreateColorPaletteSchema imported?
- Type Inference from Zod: type CreateColorPalette = z.infer<typeof CreateColorPaletteSchema>
- useForm Generic: useForm<CreateColorPalette>()

---

**Continue to:** `09-landing.md`
