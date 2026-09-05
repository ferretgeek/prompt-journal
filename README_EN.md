# Prompt Journal

[中文](./README.md) · English

Save the prompts you write for AI by project and revision, then search, reuse, export, and back them up.

**Requirements:** Windows 10 / 11 with local data storage. Source code only; a Node.js, pnpm, Rust, and Tauri build environment is required.

[Build and verify](#development-and-verification) · [Usage guide](./需求和方案文件夹/README-使用说明.md) · [Backup and restore](./docs/OPERATIONS.md) · [Screenshots](#interface)

## What it does

- **Record prompts by project and revision:** autosave drafts, finish one iteration, and start the next while retaining the history.
- **Edit and preview Markdown:** switch between WYSIWYG and source modes, with code highlighting and sanitized previews.
- **Search all records:** query project names, notes, drafts, completed iterations, and code content.
- **Protect local data:** SQLite WAL, atomic writes, close protection, conflict handling, and crash recovery.
- **Export and back up:** project import/export, full backups, restore, and data-directory migration.
- **Adjust the editor:** six themes, separate UI, body, and code fonts, always-on-top, and keyboard controls.

This is a recording tool. It does not call AI models or generate answers, and has no account system, cloud sync, or telemetry.

## Interface

The screenshot comes from a temporary empty data directory containing only synthetic examples written for this public preview — no real prompts were used or copied.

<p align="center">
  <img src="./docs/images/app-preview.png" alt="Desktop app preview" width="100%" />
</p>

## Development and verification

No ready-made installer is published. Requires Node.js 22, pnpm 10, Rust 1.95+, Windows WebView2, and a Tauri 2 build environment. Choose **Code → Download ZIP** on the repository page, extract it, and open PowerShell in the project root:

```powershell
cd 开发文件夹
pnpm install --frozen-lockfile
pnpm verify
```

Web-only tests and build:

```powershell
pnpm lint
pnpm typecheck
pnpm test
pnpm build:web
```

## Technical details

**Local data protection.** SQLite runs in WAL mode and every write is an atomic replacement. Closing the window with unsaved content is intercepted. Concurrent edits go through conflict handling rather than silently overwriting. After an abnormal exit, restarting recovers the pre-crash state. 

**Markdown previews are sanitized.** Local previews clean user content, and remote images load under an explicit policy only when user content references them — an offline tool shouldn't quietly make a network request because you pasted some Markdown.

**The two editors are lazy-loaded.** The WYSIWYG core and the source core are never resident at the same time; each loads on switch, so an occasionally used mode doesn't hold memory forever.

**Single-machine data boundary.** Product data and SQLite transactions depend on single-machine file locking, Windows permissions, and a local WebView2. So there is **no** shared data directory and no web console ; concurrent sharing could compromise data consistency. The correct way to move between machines is a full directory copy after quitting, or a verified backup.

**Don't put the active data directory on a network location.** UNC paths, mapped network drives, and live-sync folders (OneDrive and friends) break file-locking semantics. This is documented, and the app warns about it.

Full usage instructions are in [`需求和方案文件夹/README-使用说明.md`](./需求和方案文件夹/README-使用说明.md); architecture, recovery semantics, and acceptance facts are in [`需求和方案文件夹/README.md`](./需求和方案文件夹/README.md).

## Layout

```text
开发文件夹/              React, TypeScript, Tauri, and Rust sources
需求和方案文件夹/        Usage guide, architecture decisions, traceability, acceptance reports
docs/images/             Redacted previews and the social preview
成品文件夹/              Local builds and user data; always git-ignored
```

## What it doesn't do

- No AI model integration, no answer generation, no API calls on your behalf.
- No account system, cloud sync, or telemetry.
- No server edition or shared data directory (see above for why).
- The first public version ships source only; local portable builds, user databases, and historical backups are never uploaded.

## More documentation

[Install, upgrade, backup, restore, troubleshooting](./docs/OPERATIONS.md) · [Changelog](./CHANGELOG.md) · [Contributing](./CONTRIBUTING.md) · [Security policy](./SECURITY.md)

## License

MIT License — see [LICENSE](./LICENSE).
