---
name: showcase-to-x
description: Draft and optionally publish an X post that showcases work the user just completed, using the in-app Browser and a local password manager for safe X login. Use when the user asks Codex to announce, share, post, tweet, showcase, or launch recent work on X, especially when links should be posted as comments/replies and publishing must wait for explicit user confirmation.
---

# Showcase To X

## Core Rule

Prepare and stage X posts that showcase the user's just-completed work. Do not publish the main post, replies, comments, or links until the content is typed into X and the user explicitly confirms the staged browser content and destination.

Use Browser for X interaction. Use `local-password-manager` for X login when credentials are needed. Never ask the user to paste passwords into chat, never print secrets, and stop at CAPTCHA, 2FA, passkey, recovery-code, or suspicious-login screens.

## First Run

Use this path when `local-password-manager` is not installed, the X credential is missing, or no credential with allowed domain `x.com` is visible.

1. Explain the workflow to the user in plain language:
   - Codex will install or use a local password manager.
   - The user will add the X credential in a local GUI.
   - Codex can later use the approved X credential for `x.com` without seeing the password.
   - Codex will draft posts, type them into X, and ask for confirmation before publishing.
   - Links will be posted as replies/comments, not in the main post, unless the user asks otherwise.
2. Resolve the Codex skills directory:
   - If `CODEX_HOME` is set, use `$CODEX_HOME/skills`.
   - Otherwise use `~/.codex/skills`.
3. If `local-password-manager` is missing, download it into the skills directory:

```powershell
$skillsRoot = if ($env:CODEX_HOME) { Join-Path $env:CODEX_HOME "skills" } else { Join-Path $HOME ".codex\skills" }
New-Item -ItemType Directory -Force -Path $skillsRoot | Out-Null
git clone https://github.com/SimoneMariaRomeo/local-password-manager.git (Join-Path $skillsRoot "local-password-manager")
```

Use an equivalent path for non-Windows systems.

4. Install the local password manager globally for the current user:

```powershell
$skillsRoot = if ($env:CODEX_HOME) { Join-Path $env:CODEX_HOME "skills" } else { Join-Path $HOME ".codex\skills" }
python (Join-Path $skillsRoot "local-password-manager\scripts\install_global_broker.py")
```

5. Launch the GUI:

```powershell
$appHome = if ($env:LOCAL_PASSWORD_MANAGER_HOME) { $env:LOCAL_PASSWORD_MANAGER_HOME } else { Join-Path $env:LOCALAPPDATA "LocalPasswordManager" }
python (Join-Path $appHome "bin\credential_broker_gui.py")
```

6. Ask the user to add the X credential in the GUI:
   - Website name or URL: `X`
   - Allowed domains: `x.com, twitter.com`
   - Fields:
     - `username`: not sensitive
     - `password`: sensitive
7. Wait until the user confirms the credential has been saved. Do not proceed with X login until the user confirms setup is done.
8. Verify metadata only:

```powershell
$appHome = if ($env:LOCAL_PASSWORD_MANAGER_HOME) { $env:LOCAL_PASSWORD_MANAGER_HOME } else { Join-Path $env:LOCALAPPDATA "LocalPasswordManager" }
python (Join-Path $appHome "bin\credential_broker.py") list
```

Confirm there is a credential with `x.com` or `twitter.com` in allowed domains and fields named `username` and `password`. Do not print or request field values.

## Returning Runs

Use this path when the local password manager is installed and an X credential exists.

1. Summarize the work that was just completed:
   - Inspect the current conversation, changed files, commit messages, created artifacts, or user-provided description.
   - Prefer concrete shipped value over generic claims.
   - Avoid private details, unreleased customer information, secrets, internal paths, and unsupported metrics.
2. Draft the main X post:
   - Keep it concise and natural.
   - Showcase what was built, fixed, learned, released, or demonstrated.
   - Include a clear hook or payoff.
   - Do not include links in the main post when links are present; prepare them as replies/comments.
3. Prepare link replies:
   - Put links in the first reply/comment after the main post.
   - If there are multiple links, keep the reply readable and compact.
   - If a link is private, local, or not useful to readers, omit it unless the user asks to include it.
4. Stage and confirm in the browser:
   - Open X and type the drafted main post into the composer before asking for publish confirmation.
   - Let the user edit the staged text directly in the browser if needed.
   - Re-read the staged composer text after any user edit.
   - Ask for explicit confirmation using the exact staged browser text and destination.
   - Do not click `Post` until the user confirms the staged content.
   - After the main post is live, type each reply/comment into X, let the user edit it in the browser if needed, re-read it, and ask for explicit confirmation before clicking `Reply`.

## X Login With Local Password Manager

When X is not already logged in:

1. Open X with Browser and inspect the visible state.
2. Use only a credential whose allowed domains include `x.com` or `twitter.com`.
3. Prefer a credential named `x`; otherwise use the listed credential that matches X allowed domains and has `username` and `password` fields.
4. Use local-password-manager copy/paste operations or in-process calls in a way that does not print secrets.
5. Restore or clear the clipboard after using sensitive fields.
6. Stop and hand control to the user at CAPTCHA, 2FA, passkey, recovery-code, suspicious-login, or account-recovery screens.

Never include username/password values in generated scripts, prompts, logs, screenshots, or final answers.

## Browser Posting Flow

1. Load and follow the current Browser skill before the first browser action. Use the in-app Browser backend for X unless the user approves a fallback.
2. Open `https://x.com/compose/post` in Browser.
3. If redirected to login, complete the login flow using the local password manager rules above.
4. Fill the composer with the drafted main post.
5. Tell the user the post is staged in X and can be edited directly in the browser.
6. Re-read the staged composer text.
7. Ask for explicit confirmation to publish the exact staged text to X.
8. Click `Post` only after explicit confirmation.
9. After the main post is live, open the live post and type the drafted link reply/comment into the reply composer.
10. Tell the user the reply/comment is staged in X and can be edited directly in the browser.
11. Re-read the staged reply/comment text.
12. Ask for explicit confirmation to publish the exact staged reply/comment.
13. Click `Reply` only after explicit confirmation.
14. If X reports `Whoops! You already said that`, check the profile for an already-published matching post before retrying.
15. If X reports `Something went wrong`, inspect the visible toast/dialog and report the state. Do not repeatedly retry the same post.

## Confirmation Wording

Use direct confirmation prompts:

```text
I staged this in X. You can edit it directly in the browser now.

Ready to publish the staged post to X?

Staged post:
...

Please confirm explicitly before I click Post.
```

For replies/comments:

```text
I staged this reply/comment in X. You can edit it directly in the browser now.

Ready to publish the staged reply/comment to X?

Staged reply/comment:
...

Please confirm explicitly before I click Reply.
```

If the user changes the text in chat, update the browser composer, re-read it, and re-confirm before publishing. If the user edits directly in the browser, re-read the staged browser text and re-confirm before publishing.
