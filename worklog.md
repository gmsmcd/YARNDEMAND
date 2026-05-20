---
Task ID: 1
Agent: Main Agent
Task: Add image paste/upload feature to extract yarn demand data from screenshots

Work Log:
- Created API route at `/api/extract-image/route.ts` using z-ai-web-dev-sdk VLM (Vision Language Model)
- The API accepts image uploads (PNG/JPG/WEBP up to 20MB) via FormData
- VLM extracts demand data from screenshots: Demand No, Demand Date, Count, Brand, Lot, Demand Qnty
- System prompt carefully instructs VLM to distinguish between "Brand" (short name, col 15) and "Brand Name" (long name)
- API combines duplicate Count+Brand+Lot items by summing quantities
- Updated `page.tsx` to add image extraction UI in the Paste & Parse tab
- Added image paste support via Ctrl+V in the textarea (auto-detects image vs text paste)
- Added drag-and-drop style upload area with Camera icon
- Added image preview with remove/change buttons
- "Extract from Image" button sends image to API and displays results
- Godown auto-detection applied to image-extracted items
- Results flow into existing parseResult table and save workflow
- Added `Camera` and `Upload` icons from lucide-react
- Increased Next.js body size limit to 20MB for image uploads
- Fixed TypeScript compilation - used `createVision` method instead of `create` for multimodal input

Stage Summary:
- Feature: Users can now paste screenshots (Ctrl+V) or upload images of GMS demand pages
- AI (VLM) extracts all yarn items from the image automatically
- Extracted data flows into the same parse → save workflow as text paste
- Godown auto-detection works for image-extracted items too
- Files modified: `src/app/page.tsx`, `src/app/api/extract-image/route.ts`, `next.config.ts`
---
Task ID: 1
Agent: main
Task: Fix godown number auto-detection for pasted demands

Work Log:
- Investigated why godown numbers weren't auto-detecting when pasting demand data
- Found that `parseYarnDemandData()` accepted `godownLookup` parameter but NEVER used it
- `handleExtractFromImage` correctly applied godown lookup after parsing
- `handleParse` passed lookup to parser but parser ignored it — no post-parse application
- Fixed `buildGodownLookup` to normalize lot keys (uppercase, trimmed) for case-insensitive matching
- Created new `applyGodownLookup()` helper function in parser.ts
- Updated `handleParse` to apply godown lookup AFTER parsing (before yarn spec enrichment)
- Updated `handleExtractFromImage` to use the same `applyGodownLookup` helper
- Removed unused `godownLookup` parameter from `parseYarnDemandData` signature
- Build compiles successfully

Stage Summary:
- Godown auto-detection now works for BOTH paste-parse and image-extract flows
- Lot matching is case-insensitive and trimmed
- Files modified: `src/lib/parser.ts`, `src/app/page.tsx`

---
Task ID: 2
Agent: main
Task: Add godown filter feature in View dialog for selective image download

Work Log:
- Added `viewGodownFilter` state (Set<string>) to track selected godowns
- Created helper functions: `getUniqueGodowns()`, `getFilteredDemandItems()`, `handleToggleGodownFilter()`, `handleSelectAllGodowns()`, `handleClearGodownFilter()`
- Added godown filter bar UI in View dialog with clickable godown chips showing item count & qty
- Each godown chip shows: godown name, item count, total qty
- Added "Select All" and "Clear" buttons for quick filter control
- Updated table to show only filtered items when filter is active
- Added empty state when no items match the filter
- Updated Total row to show filtered total
- Modified `handleDownloadAsImage` to accept filtered items and label
- Image now shows filtered godown label in meta info and filename
- Reset filter when opening a new View dialog
- Build compiles successfully

Stage Summary:
- Users can now filter by godown in the View dialog and download filtered images
- Each godown chip shows item count and total qty for easy selection
- Image filename includes filter info (e.g., demand_DM-XXX_Godown_G1_G3.png)
- Files modified: `src/app/page.tsx`
