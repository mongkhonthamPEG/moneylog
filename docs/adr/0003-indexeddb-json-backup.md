# 0003. IndexedDB storage with mandatory JSON backup

Status: accepted

Data lives in IndexedDB (no backend, no login). The app requests persistent storage, exports a dated versioned JSON file via the iOS share sheet, warns when the last backup is older than a configurable threshold (default 7 days), and prompts for a backup after each New cycle. Import validates, then replaces all data.

Why: WebKit says installed home-screen apps have their own usage counter and shouldn't lose data, but no official Apple statement covers long periods unopened, so backups are the safety net.
