## [1.1.2] - 2026-01-24

- Replaced standard traceroute with MTR for better handling of ICMP drops (e.g. AWS).
- Added intelligent route interpolation to visualize paths even when hops are blocked.
- Integrated Signal device registration and linking via QR code in the frontend.
- Improved PDF reports with high-resolution charts (600 DPI), refined layout, and better typography.
- Unified percentage formatting across dashboard and reports.
- Refined UI responsiveness for tables and KPI cards on mobile devices.
- Switched to Material Icons for a cleaner, consistent visual style.
- Optimized database connection handling and timeout robustness.

## [1.1.1] - 2026-01-20

- In target settings, Basic Auth was enabled even though it was disabled when saving
- PDF report: traceroute visualization now uses the full page width
- Frontend: in dark mode, resolved alerts were not clearly visible
- Frontend: the "URL or Path" field was populated for HTTP checks after a page refresh, even if it was left empty when creating the target
- Default retention period for targets is now 365 days instead of 30 days
- Frontend: signal recipients overview is now collapsible
- Frontend: improved rendering of measurement points at low zoom levels
- PDF report: more cities are now shown in the traceroute chart for better orientation

## [1.1.0] - 2026-01-19

- Initial release of the project
