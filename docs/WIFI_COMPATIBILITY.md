Wi-Fi compatibility trial (2026-11-06)
====================================

The current device connects to a hotspot and loads Safari pages, but some home
access points return Apple80211 error -3903 before the kernel receives ASSOCIATE.
Changing the router is not part of this fix.

The kernel advertises capability bytes `4c 46 0a` followed by ten zero bytes.
The pinned wland donor already installs a TKIP group key when the RSN group
cipher is TKIP, while it installs CCMP for the pairwise key. The capability
bitmap omits TKIP. iOS checks cipher capabilities before submitting the join,
so this mismatch can reject a WPA2/CCMP connection with a TKIP group cipher.

Patch 0184 adds only capability bit 1. The first three bytes become `4e 46 0a`.
It does not add WPA1, TKIP-only pairwise support, or modify credentials, firmware,
the radio handshake, GPU behavior, or partitions. Existing calibration copies,
the one-shot delayed wland launcher, Native Metal sync-commit, and wallpaper
configuration must remain unchanged.

Validation: apply the patch to the redwood_apple80211.c added by 0138; verify
only capability bit 1 changes; test the home access point and repeat the known
working hotspot test. Source application and offline checks are insufficient to
claim home-router compatibility. Device trial and full kernel CI are pending.
