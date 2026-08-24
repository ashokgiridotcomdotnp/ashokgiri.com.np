# Project Memory: ashokgiri.com.np

## Overview
Personal portfolio website for Ashok Giri, a Web Developer. Features a Three.js solar system background animation, tabbed content sections (Education, Projects, Certification, Partnership), and a contact form with email integration via Nodemailer/SMTP.

**Tech Stack:** Express.js, Node.js, Tailwind CSS, Three.js (ESM), Nodemailer, Helmet, Morgan, express-rate-limit

## Project Structure
```
ashokgiri.com.np/
├── index.html           # Main HTML with skeleton loader, solar system canvas
├── server.js            # Express server with API routes (/api/contact, /api/health)
├── script.js            # Client-side JS (ESM): SolarSystem, FooterContactForm, TabNavigation, ThemeManager, cert modal
├── index.css            # Complete styling (1500+ lines): themes, layout, components, responsive
├── tailwind.css         # Tailwind entry point (@tailwind base/components/utilities)
├── tailwind.config.js   # Tailwind config (content: ./*.html)
├── postcss.config.js    # PostCSS with tailwindcss + autoprefixer
├── package.json         # Scripts: start, dev, build:css, watch:css
├── .env                 # Environment variables (gitignored)
├── EMAIL_SETUP.md       # SMTP configuration guide
├── .gitignore           # node_modules, dist, .env, logs
├── src/services/
│   └── emailService.js  # Nodemailer transport + sendContactEmail()
├── dist/                # Built CSS output (gitignored)
├── icons/               # sidebar.svg, catblack.svg
├── images/              # Profile gif, project screenshots
├── files/               # Certificate PDFs
└── resume.pdf
```

## Key Features

### 1. Three.js Solar System Background (`script.js:7-400`)
- **Class:** `SolarSystem` - Full solar system with sun, 6 planets (Mercury-Saturn), moon for Earth, Saturn rings
- **Starfield:** Two layers (near/far) with warm/cool colors, additive blending
- **Sun:** Pulsing emissive material + glow layers + lens flare sprite
- **Interaction:** Mouse parallax (camera follows), respects `prefers-reduced-motion`
- **Performance:** FPS capped (30 mobile / 45 desktop), visibility API pauses when tab hidden, `content-visibility: auto` on cards

### 2. Contact Form & Email API
- **Frontend:** `FooterContactForm` class (`script.js:436-509`) - Optimistic UI (shows success instantly, confirms in background)
- **Backend:** `POST /api/contact` (`server.js:53-78`) - Validation (required fields, email format, min 10 chars message)
- **Rate Limit:** 3 requests/day/IP (`server.js:37-43`)
- **Email Service:** `src/services/emailService.js` - Nodemailer with HTML + text templates, dev logging mode (`CONTACT_DEV_LOG_ONLY=true`)
- **Health Check:** `GET /api/health` returns `{ ok: true, emailConfigured: boolean }`

### 3. Tab Navigation (`script.js:514-547`)
- **Tabs:** Education, Projects, Certification, Partnership
- **Class:** `TabNavigation` - switches sections, smooth scroll on mobile

### 4. Theme System (`script.js:597-646`, `index.css:3-54`)
- **Auto:** Light 06:00-17:59, Dark 18:00-05:59 (synced every minute + on visibility change)
- **Manual Toggle:** Floating button, overrides temporarily until next sync
- **CSS Variables:** Comprehensive design tokens for both themes (neo-brutalism: dotted bg, brutal shadows, accent colors)

### 5. Certificate Modal (`script.js:549-590`)
- Opens PDF in new tab or shows image in modal with backdrop blur
- Keyboard accessible (Escape to close)

### 6. Sidebar Toggle (`index.css:731-949`, `index.html:113-129`)
- **Desktop:** Checkbox hack - sidebar collapses/expands, grid switches 1fr/2fr
- **Mobile:** Sidebar becomes full-screen overlay with backdrop
- **Footer Layout:** Adapts when sidebar closed (2-col grid for links + form)

### 7. Skeleton Loader (`index.html:50-88`, `index.css:111-131`)
- Shows immediately, hides when fonts ready + 2 frames (`revealAppShell` in `script.js:414-431`)

## Environment Variables (`.env`)
| Variable | Required | Description |
|----------|----------|-------------|
| `PORT` | No | Server port (default 10000) |
| `NODE_ENV` | No | `production` or `development` |
| `CONTACT_RECEIVER` | **Yes** | Email to receive contact messages |
| `SMTP_HOST` | For email | SMTP server (e.g., `smtp.gmail.com`) |
| `SMTP_PORT` | No | Default 587 |
| `SMTP_SECURE` | No | `true` for port 465 (SSL) |
| `SMTP_USER` | For auth | SMTP username |
| `SMTP_PASS` | For auth | SMTP password / app password |
| `MAIL_FROM` | For email | Sender address (defaults to SMTP_USER) |
| `CONTACT_DEV_LOG_ONLY` | No | `true` to log instead of send |

## Scripts
```bash
npm start        # Production server
npm run dev      # Dev server with --watch
npm run build:css  # Build minified tailwind.css to dist/
npm run watch:css  # Watch and rebuild CSS
```

## Dependencies
**Production:** dotenv, express, express-rate-limit, helmet, morgan, nodemailer
**Dev:** autoprefixer, postcss, tailwindcss

## Security
- Helmet with strict CSP (allows unpkg.com for Three.js, Google Fonts)
- Rate limiting on contact endpoint
- Input validation & sanitization (HTML escaping in emails)
- `.env` in `.gitignore`

## Responsive Breakpoints
- **Desktop:** ≥769px (sidebar visible, 2-col grid)
- **Tablet:** 769-1024px (tighter spacing)
- **Mobile:** ≤768px (sidebar overlay, bottom fixed nav, stacked footer)
- **Extra Small:** ≤480px (smaller nav tabs)

## Assets
- **Images:** `/images/me.png` (favicon/OG), `/images/coding1.gif` (profile), project screenshots
- **Icons:** `/icons/sidebar.svg`, `/icons/catblack.svg`
- **Certificates:** `/files/Scanned_20260717_*.pdf` (3 PDFs)
- **Resume:** `/resume.pdf`

## Notable CSS Patterns (index.css)
- **Neo-brutalism:** `--brutal-shadow` for 8px/4px/3px offsets, `--glass-border` for 2-3px borders
- **Theme-aware Tailwind overrides:** Lines 1475-1554 remap utility classes per theme
- **CSS Grid:** `.grid-layout` (sidebar + main), `.footer-layout` (links + form)
- **Animations:** `skeleton-shimmer`, `fadeIn`, hover transforms with shadow depth changes