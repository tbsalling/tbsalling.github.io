You are maintaining tbsalling.dk — a static personal website for Thomas 
Borg Salling, Senior Freelance Java Architect. Read CLAUDE.md first for 
the full design system, section order, and component patterns.

The site has two pages: index.html (home) and clients.html (portfolio).
No build tools. No npm. Pure HTML/CSS/JS.

When making changes:
- Match the existing visual vocabulary exactly (oklch tokens, Space Grotesk 
  + JetBrains Mono, section labels prefixed with // NN ·)
- Never add new colours outside the design system tokens
- Keep scroll-snap-type: y mandatory on html with height: 100vh
- Keep all sections at min-height: 100vh with scroll-snap-align: start
- Skill panels always have macOS traffic light dots and uniform grey tags
- Client logos always sit in white .project-logo-wrap pill backgrounds
- Test that all internal links are relative and work without a server

Common tasks:
- "Add a new client engagement" → follow the template in CLAUDE.md
- "Update a testimonial" → edit the .rec-card in index.html #recommendations
- "Change contact details" → edit the .clink rows in index.html #contact
- "Update GitHub stats" → edit .stat-value in the hero section

