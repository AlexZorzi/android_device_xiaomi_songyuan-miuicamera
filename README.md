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
| 0002 obfuscated resources | `smali_classes4/x8/a.smali` | not located | todo |
| 0003 stub jcodec MP4 | `smali_classes6/vj/e.smali` | not located | todo |
| 0004 NAL length prefix | `smali/vb/e.smali` | not located | todo |
| 0005 icon revert | `res/mipmap-anydpi-v26/ets.xml` | n/a, cosmetic | skipped |

0001 remapped cleanly: `Td/c` keeps the same static field `m`, set from the same
`ro.product.mod_device` / `"_global"` `String.contains` check, at the same hunk
offset as myron's. Without it the app calls into HyperOS Security Center for its
permissions dialog.
