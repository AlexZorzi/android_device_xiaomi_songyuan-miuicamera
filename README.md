# MIUI Camera for POCO F9 Ultra (songyuan)

Ported from [lolipuru/device_xiaomi_myron-miuicamera](https://github.com/lolipuru/device_xiaomi_myron-miuicamera)
(Redmi K90 Pro Max / POCO F8 Ultra). Both are sm8850 and both ship MIUI V816,
so the AIDL, shims and sepolicy transfer unchanged.

Inherited automatically by `lineage_songyuan.mk` once the blobs exist:

    device/xiaomi/songyuan-miuicamera/extract-files.py <stock dump>

## Unfinished: the APK patches

`patches-myron-reference/` holds myron's five apktool patches. They do **not**
apply here — songyuan's APK is a different build with a different R8 mapping,
and every path they touch is absent:

| patch | myron path | songyuan |
| :-- | :-- | :-- |
| 0001 mod_device is global | `smali_classes4/z7/c.smali` | absent; equivalent logic is in `smali/Jf/C.smali` |
| 0002 obfuscated resources | `smali_classes4/x8/a.smali` | absent |
| 0003 stub jcodec MP4 | `smali_classes6/vj/e.smali` | absent |
| 0004 NAL length prefix | `smali/vb/e.smali` | absent |
| 0005 icon revert | `res/mipmap-anydpi-v26/ets.xml` | absent |

Until they are re-derived, `extract-files.py` ships the APK unmodified and the
`apktool_patch` fixup stays commented out in `extract-files.py`. Expect the app
to misbehave without at least 0001, which stops it calling into the HyperOS
Security Center for its permissions dialog.
