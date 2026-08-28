# donputer-ota

The update channel for [DonPuter](https://github.com/devintucker24/DonPuter), a
dancing donkey on an ESP32-S3.

The device polls this repo over HTTPS every 15 minutes and installs whatever
`manifest.json` describes — new messages, new dances, new firmware.

## Why this repo is public

Nothing here is secret, and that is deliberate.

Firmware images and cartoon donkeys are not sensitive. Personal notes are, so
they are encrypted before they are published — with a key that lives on the
device itself and was never compiled into the firmware. Publishing the
firmware and the key in the same public place would have made the encryption
decorative.

The rule that follows: **no credential of any kind is ever committed here, and
none is ever compiled into a DonPuter firmware image.**

## What the device trusts

Not this repo. Being able to write here is not enough to change what the donkey
runs.

- `manifest.json.sig` is an ECDSA P-256 signature over the exact bytes of
  `manifest.json`. The device carries only the public half of that key, so it
  can check a signature but nobody can forge one by taking the device apart.
- Every manifest carries a sequence number that only ever increases. The device
  refuses anything it has already seen or passed, so an old-but-correctly-signed
  update cannot be replayed at it later.

An unsigned or edited manifest is ignored and the donkey carries on as it was.

## Layout

```
manifest.json        what to install, and in what order
manifest.json.sig    the signature over the bytes above
firmware-X.Y.Z.bin   firmware images
content/             messages and dances
```

Published by `tools/publish.py` in the DonPuter repo. Do not hand-edit
`manifest.json` — it will no longer match its signature and the device will
correctly refuse it.
