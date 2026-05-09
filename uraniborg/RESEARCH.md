## Integrity Check

device_properties.txt: Look for properties like ro.build.display.id or ro.product.model. Malware often hardcodes these to mimic popular brands (Samsung, Pixel) while the hardwareName in hardware.txt reveals the true, cheap chipset (e.g., MT6580).

## Hunting the "Ghost" Apps (packages.txt)

filter for isPreinstalled: true and look for these red flags:

isHidden: true: potential Pre-installed "droppers" (malware that downloads other malware) often have no launcher icon or UI

Broad Permissions: Look for apps where permissionsGranted includes sensitive ones like READ_SMS, RECORD_AUDIO, or INSTALL_PACKAGES without a clear reason.

    sharedUserId: If you see a suspicious package sharing a UID with android.uid.system, that app has full system-level control.

    usesCleartextTraffic: true: Malware could communicate with its C2 server via unencrypted HTTP

## The Forensic Trail (Hashes & Certificates)

For a suspicious package, use the hash field:

    Take the SHA256 hash from packages.txt or binaries.txt.

    Plug it into VirusTotal. Even if the app name looks legitimate (like com.android.settings), the hash will reveal if the actual code has been tampered with.

    certificates.txt: Check the encodedCert. Genuine system apps are signed by Google or the legitimate OEM (like Samsung). Rogue devices often use "test-keys" (generic developer keys) or certificates from known ad-ware companies.

## Analyzing Entry Points

Malware needs to stay alive. In packages.txt, look at the receivers and services for pre-installed apps:

    Boot Persistence: Look for receivers that listen for android.intent.action.BOOT_COMPLETED. This is how the malware starts itself the moment the phone is turned on.

    Exported Services: If a service is isExported: true and has no permission protecting it, any other app on the phone can "command" that service to perform actions.

## "Golden Image" Comparison

loose diff analysis:

    Run Hubble on a known clean device of the same model (if possible).

    Run Hubble on your suspect device.

    Compare the binaries.txt and packages.txt for rogue apps
