Project: Proton Services Integration for Gnome-Online-Accounts
1. High-Level Vision
Transform Gnome-Online-Accounts into a native hub for Proton services. Core Philosophy: Systems Integration over Software Development. We are "gluing" existing, battle-tested tools (rclone, bridge, submodules) into the Gnome GObject framework.
2. Component Map
* Mail: Glue to Proton Mail Bridge (CLI).
* Drive: Glue to rclone (Proton backend).
* Calendar: Utilize the submodule in components/calendar (Fork of SevenOfNine-labs).
3. Implementation Blueprint
A. Proton Mail (The IMAP/SMTP Wrapper)
* Recycle Target: GoaImapSmtpProvider.
* Strategy: - Clone the IMAP provider but hard-code the server to 127.0.0.1.
   * Use a helper function to scrape protonmail-bridge --cli for the dynamic ports and app passwords.
   * Requirement: Ensure the Bridge is treated as a dependency. If not found, the UI should prompt the user to install/start it.
B. Proton Drive (The FUSE Mount)
* Recycle Target: GoaOwncloudProvider (WebDAV/Files logic).
* Strategy:
   * Use rclone mount as the backend engine.
   * Store the rclone configuration/secret in the Gnome Keyring.
   * Expose the mount point so it appears in the Nautilus (Gnome Files) sidebar as a network drive.
C. Proton Calendar (The CalDAV Bridge)
* Recycle Target: GoaCalDavProvider.
* Strategy:
   * The components/calendar submodule must be audited for "Write" (CalDAV PUT/POST) support.
   * Research hydroxide or rclone source code for any new Proton Calendar "Write" API calls.
   * Output a standard CalDAV URL that the Gnome Shell clock/calendar can consume.
4. Operational Constraints
* Maximum Recycling: If a task requires >100 lines of original C code, seek a CLI tool to wrap instead.
* No New Crypto: We do not write PGP or SRP logic. We rely on the Bridge and rclone for safety.
* GObject Standards: Follow Gnome's strict C coding style and naming conventions.
* The Ethiopia Rule: Value architectural stability and "clean" failure states over speed.
5. First Steps for Claude Code
1. Discovery: Confirm the presence of protonmail-bridge, rclone, and the components/calendar submodule.
2. Template Audit: Identify the exact .c and .h files in the current directory that serve as the best templates for IMAP and Files.
3. Plan Generation: Create a MASTER_BUILD_PLAN.md that lists the files to be created (e.g., goa-proton-provider.c).
4. Dependency Check: Verify that the build system (Meson/Ninja) is ready to compile a new provider.
6. Communication
* Use /compact frequently to keep context clean.
* If you hit a technical wall (e.g., Proton encryption prevents a specific "Write" feature), document it in STATUS_REPORT.md and move to the next service.