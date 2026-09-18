# MIUI Camera for POCO F9 Ultra (songyuan)

Ported from [lolipuru/device_xiaomi_myron-miuicamera](https://github.com/lolipuru/device_xiaomi_myron-miuicamera)
(Redmi K90 Pro Max / POCO F8 Ultra). Both are sm8850 and both ship MIUI V816,
so the AIDL, shims and sepolicy transfer unchanged.

Inherited automatically by `lineage_songyuan.mk` once the blobs exist:

    device/xiaomi/songyuan-miuicamera/extract-files.py <stock dump>

## APK patches

`patches/` holds what has been ported. `patches-myron-reference/` keeps myron's
originals for reference. They do not apply as-is: songyuan's APK is a different
build with a different R8 mapping.

| patch | myron path | songyuan | state |
| :-- | :-- | :-- | :-- |
| 0001 mod_device is global | `smali_classes4/z7/c.smali` | `smali_classes2/Td/c.smali` | **ported** |
| 0002 obfuscated resources | `smali_classes4/x8/a.smali` | `smali_classes2/Se/a.smali` | **ported** |
| 0003 stub jcodec MP4 | `smali_classes6/vj/e.smali` | not located | not needed so far |
| 0004 NAL length prefix | `smali/vb/e.smali` | not located | not needed so far |
| 0005 icon revert | `res/mipmap-anydpi-v26/ets.xml` | n/a, cosmetic | skipped |

0002 needed one extra change: `ResourcesCompat` is R8-renamed to `W/f$a` here,
so the fallback calls that directly.

0001 remapped cleanly: `Td/c` keeps the same static field `m`, set from the same
`ro.product.mod_device` / `"_global"` `String.contains` check, at the same hunk
offset as myron's. Without it the app calls into HyperOS Security Center for its
permissions dialog.

## Verified working

Photo and 1080p HEVC video both capture cleanly with only 0001 and 0002:

- stills: 5.9 MB JPEG with EXIF, via the full MiAlgo pipeline
- video: well-formed mp42 container, `hvc1` video + audio track, muxer closes clean
- no provider crash, no jcodec/NAL/muxer errors in the recording log

0003 and 0004 are therefore not required for ordinary capture. They likely cover
modes not exercised here -- slow motion, Live Photo, the cinematic modes -- so
check them first if one of those misbehaves.

Capture needs more than this tree: 28 camera blobs under `odm/etc/camera` and
`odm/lib/rfsa` were missing from the main device tree's `proprietary-files.txt`.
Without `xiaomi/quickviewsnapshot.json` in particular, `libmialgoengine` gets a
null pipeline and the camera provider segfaults in `PipelineInfo::operator=` on
every shutter press.
