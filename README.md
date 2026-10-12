# az-weblogin

Use the `az login` browser flow on a headless box (e.g. a VPS over SSH) instead of device code.

```sh
az-weblogin [any az login args]
```

1. It prints a `login.microsoftonline.com` URL. Open it in any browser and sign in.
2. The browser redirects to `http://localhost:<port>/?code=...` and fails to load. Copy that URL.
3. Paste it into the prompt. The wrapper forwards it to the waiting `az login`, which completes.

Paste within a few minutes; the auth code expires quickly.

## How it works

On Linux, `az login` falls back to device code when Python's `webbrowser.get()` finds no browser.
The wrapper sets `BROWSER` to a script that just records the URL, so `az` uses the normal auth code flow and listens on localhost.
MSAL requests `response_mode=form_post`; the wrapper switches it to `query` so the code is visible in the address bar, then POSTs it to the listener with `curl`.

## Install

```sh
install -m 755 az-weblogin ~/.local/bin/
```
