---
name: brand-guidelines
description: Applies a company's brand colors and typography to any sort of artifact that may benefit from having the company's look-and-feel. Use it when brand colors or style guidelines, visual formatting, or company design standards apply.
license: Complete terms in LICENSE.txt
---

# Brand Styling

## Overview

This skill helps apply any company's official brand identity and style to artifacts. When the user provides brand colors, fonts, or style guidelines, use them consistently. If no brand details are provided, ask the user for their brand colors, fonts, and style preferences before proceeding.

**Keywords**: branding, corporate identity, visual identity, post-processing, styling, brand colors, typography, brand guidelines, visual formatting, visual design

## Workflow

1. **Gather brand details** — If not already provided, ask the user for:
   - Primary and secondary colors (hex or RGB)
   - Accent colors (if any)
   - Heading font and body font preferences
   - Any additional style rules (logo placement, spacing, etc.)

2. **Apply consistently** — Use the provided brand values across all elements: backgrounds, text, shapes, accents, and typography.

3. **Fallback gracefully** — If specific fonts are unavailable, fall back to system fonts (e.g., Arial for headings, Georgia for body text) while preserving the overall design intent.

## Brand Guidelines Template

When the user provides their brand, map it to these slots:

### Colors

**Main Colors:**

- Dark: `<user-provided>` — Primary text and dark backgrounds
- Light: `<user-provided>` — Light backgrounds and text on dark
- Mid Gray: `<user-provided>` — Secondary elements
- Light Gray: `<user-provided>` — Subtle backgrounds

**Accent Colors:**

- Primary Accent: `<user-provided>`
- Secondary Accent: `<user-provided>`
- Tertiary Accent: `<user-provided>` (optional)

### Typography

- **Headings**: `<user-provided>` (with Arial fallback)
- **Body Text**: `<user-provided>` (with Georgia fallback)
- **Note**: Fonts should be pre-installed in the environment for best results

## Features

### Smart Font Application

- Applies the brand heading font to headings (24pt and larger)
- Applies the brand body font to body text
- Automatically falls back to Arial/Georgia if custom fonts are unavailable
- Preserves readability across all systems

### Text Styling

- Headings (24pt+): brand heading font
- Body text: brand body font
- Smart color selection based on background
- Preserves text hierarchy and formatting

### Shape and Accent Colors

- Non-text shapes use brand accent colors
- Cycles through the provided accent palette
- Maintains visual interest while staying on-brand

## Technical Details

### Font Management

- Uses system-installed brand fonts when available
- Provides automatic fallback to Arial (headings) and Georgia (body)
- No font installation required — works with existing system fonts
- For best results, pre-install the brand's fonts in the environment

### Color Application

- Uses RGB color values for precise brand matching
- Applied via python-pptx's RGBColor class (for PPTX/document outputs)
- Maintains color fidelity across different systems
