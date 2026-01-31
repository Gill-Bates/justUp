## [1.1.4] - 2026-01-31

- New: Multi-arch support (``amd64`` + ``arm64``). ``armhf`` not planned.
- Fix: Alert recipients can now be selected per target (instead of per user)
- Fix: The page title was missing in the PDF report when the WhatWeb scan failed
- Fix: Typo fixes in the PDF report
- Fix: More robust WhatWeb target analysis with improved error-case logging
- Fix: The “Cert” badge was shown in the target overview even when the HTTP check was disabled
- Fix: When the TCP check was enabled for a target, the TCP badge was not shown in the target details

<details markdown="1">
<summary>Previous versions...</summary>
  
## [1.1.3] - 2026-01-30

- E-Mail alert notifications with configurable SMTP and editable templates (``TinyMCE Editor``)
- About page including changelog, license, and build information
- Per-target metrics reset on dashboard and target detail pages
- IPv6 support for traceroute
- Integration of a dedicated Signal service
- Complete redesign of the internal PTR database and traceroute logic for significantly more accurate location detection
- Signal Messenger: substantially improved device registration workflow
- Signal Messenger: new phone number registration restricted to mobile devices only (captcha requirement)
- Settings: added automatic save functionality for configuration changes

## [1.1.2] - 2026-01-27

- Replaced standard traceroute with MTR for better handling of ICMP drops (e.g. AWS)
- Added intelligent route interpolation to visualize paths even when hops are blocked
- Integrated Signal device registration and linking via QR code in the frontend
- Improved PDF reports with high-resolution charts (600 DPI), refined layout, and better typography
- Unified percentage formatting across dashboard and reports
- Refined UI responsiveness for tables and KPI cards on mobile devices
- Switched to Material Icons for a cleaner, consistent visual style
- Optimized database connection handling and timeout robustness
- Fixed: Permission issues when restoring backups

## [1.1.1] - 2026-01-20

- Fixed: Basic Auth was enabled in target settings even when disabled during save
- PDF report: Traceroute visualization now uses the full page width
- Frontend: Resolved alerts are now clearly visible in dark mode
- Frontend: "URL or Path" field no longer auto-populated for HTTP checks after page refresh when left empty
- Default retention period for targets changed from 30 to 365 days
- Frontend: Signal recipients overview is now collapsible
- Frontend: Improved rendering of measurement points at low zoom levels
- PDF report: More cities shown in traceroute chart for better orientation

## [1.1.0] - 2026-01-19

- Initial release of the project

</details>
