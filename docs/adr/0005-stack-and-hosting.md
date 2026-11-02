# 0005. Stack and hosting

Status: accepted

Vite + TypeScript + Preact, `vite-plugin-pwa` for offline support, Vitest for unit tests. Deployed as a static site on GitHub Pages (HTTPS is required to install a PWA on iPhone).

Why: Preact is reportedly about 3 KB gzipped (blog sources, unmeasured) with a React-style API; the money math is where bugs live, so types and tests matter more than framework size.
