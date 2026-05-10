# Showcase To X

Codex skill for turning recently completed work into an X post, with safe credential handling and a confirmation gate before anything is published.

## Install

Clone this repo into your Codex skills directory:

```powershell
git clone https://github.com/SimoneMariaRomeo/showcase-to-x.git "$env:USERPROFILE\.codex\skills\showcase-to-x"
```

Then invoke:

```text
Use $showcase-to-x to draft an X post showcasing the work I just completed.
```

## What It Does

- Uses `local-password-manager` for first-time X credential setup.
- Uses Browser to log in to X and stage the post.
- Drafts a showcase post from the work just completed.
- Asks for explicit confirmation before publishing.
- Posts links as replies/comments instead of placing them in the main post.

The skill does not store credentials itself and should never print passwords.
