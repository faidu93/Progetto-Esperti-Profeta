# Design System Document: The Editorial Authority

## 1. Overview & Creative North Star
**Creative North Star: "The Modern Curator"**

This design system is built to bridge the gap between high-end editorial prestige and a welcoming community atmosphere. We are moving away from the "templated" look of traditional SaaS platforms. Instead, we embrace **Organic Minimalism**—a style characterized by intentional asymmetry, vast "breathing room" (white space), and a sophisticated use of typography that commands authority while remaining accessible.

The "e" logo is our geometric anchor. Its circular form should influence our UI through subtle rounding and radial tonal shifts, ensuring the brand identity isn't just "placed" on the page, but woven into the interface.

---

## 2. Colors & Surface Philosophy

Our palette is anchored in deep, ink-like greys with a surgical strike of vibrant orange. This creates a high-contrast, premium feel.

### The "No-Line" Rule
To maintain an editorial feel, **1px solid borders for sectioning are strictly prohibited.** We define boundaries through:
- **Background Color Shifts:** A section using `surface_container_low` (#1c1b1b) sitting against a `surface` background (#131313).
- **Negative Space:** Using the Spacing Scale (specifically 16 and 20) to separate conceptual blocks.

### Surface Hierarchy & Nesting
Treat the UI as physical layers. Use the hierarchy to guide the eye:
1.  **Base Layer:** `surface` (#131313) – The canvas.
2.  **Sectioning:** `surface_container` (#201f1f) – For large content blocks.
3.  **Elevation:** `surface_container_high` (#2a2a2a) – For interactive cards or prominent navigation.
4.  **Interaction:** `surface_bright` (#393939) – For hover states and active indicators.

### Glass & Gradient Implementation
Avoid flat orange boxes for large areas. For Hero backgrounds or main CTAs, use a subtle linear gradient:
- **Primary Gradient:** From `primary_container` (#ff6b00) to `on_secondary_container` (#ffa173) at a 135° angle.
- **Glassmorphism:** For floating navigation or tooltips, use `surface_variant` at 70% opacity with a `20px` backdrop-blur. This softens the UI and makes it feel integrated rather than "pasted."

---

## 3. Typography: The Editorial Voice

We utilize a dual-font strategy to balance "Authority" and "Approachability."

*   **Display & Headlines (Space Grotesk):** This is our "Expert" voice. It’s geometric, slightly brutalist, and highly professional. Use `display-lg` (3.5rem) with tighter letter-spacing (-0.02em) for hero sections to create a bold, editorial impact.
*   **Body & Titles (Plus Jakarta Sans):** This is our "Friendly" voice. It is highly legible and modern. Use `body-lg` (1rem) for long-form content to ensure a comfortable reading experience.

**Hierarchy Note:** Always maintain a significant scale jump between headlines and body text. If a headline is `headline-lg`, the supporting text should be `body-md` to emphasize the editorial hierarchy.

---

## 4. Elevation & Depth

We convey importance through **Tonal Layering**, not structural lines.

### The Layering Principle
Place `surface_container_lowest` cards on a `surface_container_low` section. The slight shift in grey creates a soft, natural "lift" that mimics fine paper stocks.

### Ambient Shadows
Shadows should be felt, not seen.
- **Token:** Use `on_surface` color for shadows at **6% opacity**.
- **Setting:** 0px 24px 48px blur. This creates a soft, ambient glow that feels like natural light hitting a matte surface.

### The "Ghost Border" Fallback
If a border is required for accessibility (e.g., input fields), use the **Ghost Border**:
- **Value:** `outline_variant` at **15% opacity**.
- **Constraint:** Never use 100% opaque borders; they break the minimalist "ink and paper" flow.

---

## 5. Components

### Logo Integration
The "e" logo must be treated as a hallmark. 
- **Header:** Placed at `2.75rem` (Spacing 8) height, left-aligned or centered with `3.5rem` (Spacing 10) of clear space around it.
- **Footer:** Rendered in `on_surface_variant` for a subtle, high-end watermark effect.

### Buttons (High-Contrast)
- **Primary:** Background `primary_container` (#ff6b00), Text `on_primary` (#561f00). Shape: `md` (0.375rem) for a professional edge.
- **Tertiary (Ghost):** No background. Text `primary`. Use a hover state of `surface_container_highest` with `0.25rem` (DEFAULT) rounding.

### Cards & Lists
- **Rule:** Forbid divider lines. 
- **Execution:** Use vertical white space (Spacing 6 or 8) between list items. For cards, use `surface_container_low` with a padding of Spacing 5 (`1.7rem`).

### Expert Chips
- Use `secondary_container` (#7b3409) for background and `on_secondary_container` (#ffa173) for text. Shape: `full` (9999px). This identifies "Expert" tags with a warm, authoritative glow.

### Input Fields
- Background: `surface_container_lowest`.
- Border: "Ghost Border" (15% `outline_variant`).
- Focus State: Transition border to `primary` (#ffb693) at 50% opacity with a `2px` outer glow.

---

## 6. Do's and Don'ts

### Do
- **Do** use asymmetrical layouts (e.g., offsetting a headline to the left while the body text sits in a narrower column to the right).
- **Do** utilize `surface_dim` for the very bottom of the page to "ground" the footer.
- **Do** allow for large gutters (Spacing 16+) between major content sections.

### Don't
- **Don't** use pure black (#000000). Always use `surface` (#131313) to keep the dark mode "soft" and premium.
- **Don't** use standard "Drop Shadows." Stick to the Ambient Shadow values provided in Section 4.
- **Don't** crowd the logo. It is a symbol of authority; it needs room to breathe.
- **Don't** use more than one vibrant orange element in a single viewport. Let the orange be the "Expert's Signature"—precise and intentional.