# GodotNFC

GodotNFC is a **Godot Android Plugin v2** for NFC on Android.

It allows you to:

- detect NFC tags
- read basic NDEF text / URI data
- inspect tag technologies
- read tag UID
- send raw hex commands with multiple NFC techs
- write NDEF text to writable tags
- receive debug logs inside Godot

This plugin is made for **Godot 4** and Android export.

---

## Features

- Godot Android Plugin v2
- Works with `Engine.has_singleton("godotnfc")`
- Reader mode support
- NDEF text reading
- NDEF text writing
- Tag info as JSON
- Raw transceive support
- Debug signal logs
- Supports multiple NFC technologies:
  - NfcA
  - NfcB
  - NfcF
  - NfcV
  - IsoDep
  - MifareUltralight
  - MifareClassic
  - Ndef
  - NdefFormatable

---

## Important note

This plugin supports **many NFC technologies**, but it does **not automatically decode every secure smart card** such as:

- national ID cards
- passports
- payment cards
- proprietary government cards

Those cards often require:

- APDU commands
- authentication
- secure session logic
- proprietary protocols

So this plugin gives you the **base NFC layer**, not full automatic smartcard decoding.

---

## Requirements

- Godot 4.x
- Android export
- Android phone with NFC
- Custom Android build enabled

---

## Project structure

Example addon structure:

addons/
└── godot_nfc/
    ├── plugin.cfg
    ├── plugin.gd
    ├── godot_nfc.gd
    └── bin/
        ├── GodotNFC-debug.aar
        └── GodotNFC-release.aar
Installation
1. Copy the addon

Copy the godot_nfc folder into:

res://addons/
2. Enable the plugin

In Godot:

go to Project > Project Settings > Plugins

enable GodotNFC

3. Enable custom Android build

In Godot Android export settings:

enable Use Custom Build

4. Export to Android device

This plugin works only on Android devices with NFC hardware.

Included files
plugin.cfg

Registers the addon in Godot.

plugin.gd

Registers the Android export plugin and loads the AAR.

godot_nfc.gd

Godot wrapper singleton for clean GDScript access.

GodotNFC-debug.aar / GodotNFC-release.aar

Android native NFC plugin builds.

Android plugin name

The Android singleton name is:

godotnfc

The Godot wrapper autoload name is:

GodotNFC
Basic usage
extends Control

func _ready():
	print("Available: ", GodotNFC.is_available())
	print("Supported: ", GodotNFC.is_nfc_supported())
	print("Enabled: ", GodotNFC.is_nfc_enabled())
	print("Plugin version: ", GodotNFC.plugin_version())
	print("Status: ", GodotNFC.get_nfc_status())

	GodotNFC.nfc_reader_started.connect(_on_reader_started)
	GodotNFC.nfc_reader_stopped.connect(_on_reader_stopped)
	GodotNFC.nfc_error.connect(_on_nfc_error)
	GodotNFC.nfc_tag_detected.connect(_on_tag_detected)
	GodotNFC.nfc_tag_info.connect(_on_tag_info)

func start_scan():
	GodotNFC.start_reader_mode()

func stop_scan():
	GodotNFC.stop_reader_mode()

func _on_reader_started():
	print("reader started")

func _on_reader_stopped():
	print("reader stopped")

func _on_nfc_error(message):
	print("NFC error: ", message)

func _on_tag_detected(id_hex, tech_list_json, payload_text, payload_json):
	print("Tag ID: ", id_hex)
	print("Tech list: ", tech_list_json)
	print("Payload text: ", payload_text)
	print("Payload json: ", payload_json)

func _on_tag_info(id_hex, tag_info_json):
	print("Tag info: ", tag_info_json)
Writing text to a tag
GodotNFC.write_text_to_tag("Hello from GodotNFC")
Signals
nfc_reader_started

Emitted when reader mode starts.

nfc_reader_stopped

Emitted when reader mode stops.

nfc_error(message)

Emitted when an NFC error happens.

nfc_debug_log(level, message)

Emitted for internal debug messages.

nfc_tag_detected(id_hex, tech_list_json, payload_text, payload_json)

Emitted when a tag is detected.

nfc_text_written(id_hex, text)

Emitted after writing text to a tag.

nfc_tag_info(id_hex, tag_info_json)

Emitted with detailed JSON info about the scanned tag.

nfc_transceive_result(tech, req_hex, resp_hex)

Emitted after raw transceive operations.

Methods
Availability / state
GodotNFC.is_available()
GodotNFC.is_nfc_supported()
GodotNFC.is_nfc_enabled()
GodotNFC.is_reader_mode_active()
GodotNFC.get_nfc_status()
GodotNFC.plugin_version()
Reader mode
GodotNFC.start_reader_mode()
GodotNFC.stop_reader_mode()
Reader options
GodotNFC.set_auto_stop_after_scan(true)
GodotNFC.get_auto_stop_after_scan()

GodotNFC.set_vibrate_on_success(true)
GodotNFC.get_vibrate_on_success()
NDEF writing
GodotNFC.write_text_to_tag("Hello NFC")
Last scanned tag
GodotNFC.has_last_tag()
GodotNFC.get_last_tag_id()
GodotNFC.get_last_tag_info_json()
GodotNFC.read_ndef_text_from_last_tag()
Raw transceive
GodotNFC.transceive_last_tag_hex("IsoDep", "00A4040000")
GodotNFC.transceive_last_tag_hex("NfcA", "3004")

Supported tech names:

NfcA

NfcB

NfcF

NfcV

IsoDep

MifareUltralight

MifareClassic

Example: read tag info JSON
func _on_tag_info(id_hex, tag_info_json):
	print("Tag ID: ", id_hex)
	print(tag_info_json)

The JSON may include:

tag UID

technology list

NDEF info

Mifare info

IsoDep info

NfcA / NfcB / NfcF / NfcV details

Example: raw APDU / raw hex
var result = GodotNFC.transceive_last_tag_hex("IsoDep", "00A4040000")
print(result)

Example with NfcA:

var result = GodotNFC.transceive_last_tag_hex("NfcA", "3004")
print(result)
Debugging

You can connect to the debug signal:

GodotNFC.nfc_debug_log.connect(_on_nfc_debug_log)

func _on_nfc_debug_log(level, message):
	print("[NFC ", level, "] ", message)

This helps you see:

when reader mode starts

when reader mode stops

if onTagDiscovered() fires

if Android activity pauses/resumes

internal plugin flow

Common issues
1. Available: false

The Android plugin was not loaded.

Check:

plugin enabled in Godot

AAR files in correct addon folder

Android export uses custom build

2. Supported: false

Usually one of these:

device has no NFC

plugin not loaded correctly

testing on unsupported device

3. Enabled: false

NFC exists but is disabled in Android settings.

4. Android system toast says:

No supported app for this NFC

This usually means your app is not currently capturing the scan in reader mode, or the card uses a secure/proprietary format not automatically handled as plain NDEF.

5. ID card or passport does not read as text

That is normal for many secure cards. They may require:

IsoDep APDU commands

authentication

custom parsing logic

Tested with

Godot 4.x

Android NFC device

Godot Android Plugin v2 structure

Limitations

Android only

No iOS support

No automatic passport/eID decoding

No built-in APDU parser

No payment-card support logic

Roadmap ideas

Possible future improvements:

write URI tags

read/write Mifare Ultralight pages

Mifare Classic authentication helpers

IsoDep APDU helper methods

foreground scan UI

better smartcard helper APIs

License
MIT License

Author

ABDELHAKIM MAHHA

Support

If you find a bug or want a feature, open an issue on GitHub.
