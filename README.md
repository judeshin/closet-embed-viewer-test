# CLO-SET Embed Viewer Test Page

This repository contains a simple HTML test page used to verify how **CLO-SET Embed Viewers** behave under various `iframe` permission and `sandbox` configurations.

The primary goal is to ensure that CLO-SET embed viewers function correctly in **enterprise customer environments**, where strict security policies are often applied.

---

## Purpose

Enterprise customer websites frequently enforce restrictive iframe settings, such as:

- Limited `allow` permissions
- Mandatory `sandbox` attributes
- Disabled fullscreen or device-related APIs by default

This test page validates whether CLO-SET embed viewers remain functional under such constraints and helps define the **minimum required iframe configuration**.

Additional tests intentionally include permissions such as `accelerometer`, `gyroscope`, and `picture-in-picture`, under the assumption that these features are **not supported** by the viewer.  
The goal is to confirm that granting unsupported permissions does not introduce unexpected behavior or errors.

---

## What Is Being Tested

### 1. Viewer Contexts

- **3D Interactive Viewer (`/c/` path)**  
  Always loads the interactive 3D viewer, including camera controls and user interaction.

- **Render Viewer (`/r/` path)**  
  Displays rendered outputs (e.g. V-Ray renders) with limited interaction (zoom, pan, frame navigation).

### 2. iframe Permission Policies

- No permissions
- `fullscreen`
- `picture-in-picture`
- Device sensor permissions (`accelerometer`, `gyroscope`)  
  *(Included for validation purposes only. CLO-SET viewers do not rely on these APIs.)*

### 3. iframe Sandbox Behavior (Most Important)

- `sandbox` without permissions
- `sandbox="allow-same-origin allow-scripts"`
- Interaction between `sandbox` and fullscreen / viewer execution

---

## Key Findings / Design Assumptions

### Device Sensors

- `accelerometer` and `gyroscope` permissions are **not required**.
- CLO-SET viewers do not implement or depend on Device Motion or Orientation APIs.
- Granting these permissions has no effect, which is expected and desirable.

### Picture-in-Picture (PiP)

- PiP is primarily designed for video elements.
- CLO-SET viewers are WebGL-based and do not expose a media playback context.
- PiP is expected **not to work**.
- This test verifies that enabling PiP permissions does not cause errors or side effects.

### Sandbox (Critical)

- Many enterprise CMS platforms enforce `iframe sandbox` by default.
- The configuration below is considered the **minimum viable setup**:

```html
sandbox="allow-same-origin allow-scripts"
```

With this configuration, the interactive (3D) viewer is expected to function correctly.

- Additional sandbox permissions (e.g. `allow-popups`, `allow-downloads`) may be required depending on integration needs.

---

## Test Page Structure

The test page includes the following scenarios:

1. **Render Viewer with no iframe permissions**  
   - Expected: Fullscreen does not work

2. **3D Viewer with extended iframe permissions**  
   - Expected: Viewer works normally

3. **Render Viewer with minimal fullscreen permissions**  
   - Expected: Fullscreen works

4. **3D Viewer inside a sandboxed iframe**  
   - Expected: Viewer works correctly with scripts and same-origin enabled

---

## Usage

Due to browser security policies (e.g. CSP), opening the HTML file directly via `file://` may not work.
A proper HTTP origin is required to satisfy iframe security constraints enforced by modern browsers.
Run a local web server instead, then open the page via `http://localhost`.

### Python (recommended)

```bash
# Python 3
python -m http.server 8000
```

This page is intended for:

- Embed compatibility testing
- Enterprise customer validation
- Security / IT review discussions
- Internal documentation and troubleshooting

---

## Disclaimer

This repository is a **technical validation tool**, not a production integration example.  
Actual customer environments may impose additional constraints such as:

- CSP (`Content-Security-Policy`)
- `frame-ancestors` restrictions

---

## License

Internal or evaluation use only.
