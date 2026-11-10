# Privacy Policy for ZNeko Launcher

**Last updated:** November 10, 2026

ZNeko Launcher is a local-first Android frontend. It does not include advertising or analytics SDKs, and the ZNeko developers do not operate an account system that collects your personal information.

## Information processed on your device

ZNeko processes the following information to provide its features:

- Files and folders you explicitly select through Android's Storage Access Framework, including file names and other metadata needed to organize your game library.
- Artwork, metadata, audio, settings, and other launcher data you add or generate.
- Information about launchable apps installed on your device, used to show compatible emulators and let you assign one to a game folder. This information is processed locally and is not sent to the ZNeko developers.

When you launch a game, ZNeko gives the selected file's Android URI to the emulator you assigned. That emulator is a separate application with its own privacy practices.

## Online services

Scraping and account services are optional. A clean setup also attempts to download an initial folder icon pack automatically when online; the launcher remains usable offline. Connections go directly to the selected service over HTTPS:

- **NeoAssets:** Folder icon pack metadata and images are downloaded from NeoAssets. Pack metadata and discovery history are cached locally, and downloaded icons remain usable offline. Downloading a pack increments its public download count. Requests disclose normal connection information, such as your IP address, to that service.
- **iiDB:** Your API key, game titles or arcade identifiers, resolved game IDs, and asset requests are sent to iiDB to retrieve artwork, screenshots, and soundbites. Bulk scraping groups up to 25 lookups into one request. The key is sent only to its authenticated developer API; media files are downloaded without the key. Resolved game IDs and selected artwork mappings are saved locally.

- **ScreenScraper:** Your ScreenScraper username and password, along with game file names and search information, are sent to ScreenScraper to retrieve artwork and metadata.
- **SteamGridDB:** Your API key, search terms, and asset requests are sent to SteamGridDB to retrieve artwork. When you open the Assets Catalog, ZNeko also asks SteamGridDB for the icons and grids of every supported platform, at most once a week unless you refresh it, and downloads the images of the packs you browse or install. The resulting catalog, which includes the SteamGridDB usernames and profile IDs of the people who uploaded that artwork, is saved in your ZNeko data folder.
- **RetroAchievements:** Your username and API key, together with game and console identifiers, are sent to RetroAchievements to display achievement information.

ZNeko can also download public catalog data and media from sources such as GitHub. Its manual web picker can open services such as iiDB, Google Images, GIPHY, and SteamGridDB. These sites may receive normal web request information, such as your IP address, browser information, and first-party cookies. ZNeko does not add advertising trackers and does not allow third-party cookies in its web picker.

Each external service handles information under its own privacy policy. ZNeko does not control their retention or use of information.

## ZNeko Link

ZNeko Link lets you pair with a companion device and transfer selected files over your local network. During first pairing, the devices establish an encrypted TLS connection and display a verification code. Approve the request in ZNeko Link only after checking that the same code appears on both screens, then confirm the match in the launcher. The launcher remembers ZNeko Link's certificate so later connections can reject an unexpected device.

Discovery, pairing, authenticated requests, and file transfers are encrypted while travelling directly between the paired devices and are not routed through a ZNeko cloud service. ZNeko stores the paired device name, certificate fingerprint, and randomly generated access credential locally so it can recognize and authenticate the companion.

If you choose to receive service credentials from ZNeko Link (such as iiDB, ScreenScraper, SteamGridDB, RetroAchievements, or your S3 storage connection string), the transfer is initiated only on your request and requires explicit confirmation in ZNeko Link on your companion device. Credentials travel directly over the encrypted local TLS connection, are saved in ZNeko's encrypted app-private storage on your device, and are never sent to or routed through ZNeko servers.

If you include the launcher configuration in a backup, that backup contains your launcher settings but not your service credentials, which never leave ZNeko's encrypted storage. ZNeko Link encrypts the transfer in transit, but the received backup remains in the destination folder you selected. Only transfer configuration backups to a device you trust.

## S3-compatible cloud storage

ZNeko can connect to an S3-compatible storage service that you provide, such as Amazon S3, Backblaze B2, Cloudflare R2, Wasabi, or a server you run yourself. The bucket is yours. The ZNeko developers do not operate it, cannot read it, and are not a party to your agreement with the provider.

You supply the endpoint, region, bucket name, access key ID, and secret access key. These are saved locally with your other service credentials and are never sent to the ZNeko developers; they are used only to sign requests to the endpoint you entered. Treat them as more sensitive than the other keys ZNeko stores. Depending on how you scoped them, they may grant full read and write access to the bucket.

When you use this feature, ZNeko sends the following directly to your provider over HTTPS:

- **Game files you choose to upload,** stored under `roms/` with their platform folder and file name.
- **Folder backups you create,** stored under `backups/` as one archive of the folder's contents, alongside a small file recording that archive's size, its number of files, when it was written, and a fingerprint of the folder's shape. The fingerprint is derived from file paths, sizes, and modification times, never from file contents.
- **Requests to list, download, rename, and delete** those objects.

To move a whole file, ZNeko generates a pre-signed URL that authorizes one request for a limited time and hands that URL to the component performing the transfer, so the secret key stays inside the launcher's signing code.

ZNeko keeps a local list of the game objects in your bucket so your library can be shown without contacting the provider every time. It holds object names and sizes only, and it is discarded when you disconnect the account.

Deleting a game or a backup from ZNeko removes the object permanently. On a bucket with versioning enabled it removes every stored version of that object along with its delete markers, rather than hiding the current version behind a marker, so the space is actually reclaimed. ZNeko also removes superseded versions when it uploads and when it refreshes the backup list. Your provider may still hold data beyond ZNeko's reach under your agreement with it, such as its own replication or backups, or a minimum retention period applied by your plan.

## RomM server

ZNeko can connect to a self-hosted RomM server (version 5.4 or newer) that you operate or have access to. The server is yours or your host's; the ZNeko developers do not operate it, cannot read its contents, and are not a party to your hosting agreement.

You connect by providing the server address. Pairing uses RomM's device authorization flow: ZNeko requests a short pairing code, which you approve in RomM from any browser. With that request it sends a random identifier it creates for this device, the device's name as set in Android (or its manufacturer and model when no name is set), the ZNeko version, and the platform (Android); RomM shows these in its list of your devices. RomM then issues an API token for this device with full access to your account. The token and server address are saved locally with your other service credentials. Your account password is never typed on or stored by ZNeko.

When connected, ZNeko communicates directly with your RomM server over HTTP or HTTPS to:

- Retrieve the list of games and platforms available to your account, cached locally so your library can be displayed without querying the server on every launch. This cached list holds game names, file IDs, and sizes only, and is discarded when you disconnect.
- Download games you choose to transfer to your device.
- List, export, and import game save files and folders between your device and RomM when using RomM Saves.

Requests travel directly between your device and your RomM server using your server's configured address. You can revoke the device token at any time in RomM's device settings. Disconnecting in ZNeko deletes the token from this device but does not revoke it on the server.

## Optional diagnostic logs

Log generation is off by default. If you enable it, ZNeko records a small local diagnostic report containing app and Android versions, device manufacturer and model, WebView version, timestamps, app lifecycle and memory signals, standby transitions, screen and surface identification, screen sizes, rotation and power state, window and page sizes, error types, sanitized app stack locations, and the fixed identifier of an emulator integration that was launched.

To investigate unexpected shutdowns, the report may also include process names and IDs (PIDs), Android system UIDs, process exit reasons and times, and sanitized system exit descriptions, including resource usage details such as CPU time. PIDs and UIDs are identifiers assigned by Android to processes and apps on your device.

Diagnostic logs do not include ROM contents, account credentials, network addresses, or unique device identifiers. Exception messages are truncated, and storage URIs, web addresses, host names, and IP addresses are redacted, but messages may include contextual names needed to identify a failure. Logs are never sent automatically. The report is stored on your device so you can review it and choose whether to send it to the ZNeko developers.

## Storage, backup, and deletion

Service credentials, including your S3 keys and your RomM device token, are stored only on your device, in ZNeko's app-private storage. They are encrypted with AES-256-GCM using a key that is created and kept in the Android Keystore. They are never written to the data folder you selected, are not included in backups, and are never sent to the ZNeko developers.

ZNeko disables Android cloud backup for its app-private data and asks Android to exclude that data from supported device-to-device migration. Android notes that some device manufacturers may still migrate app data directly between devices despite an app's backup setting. Any such migration is performed by the operating system; the ZNeko developers cannot access it.

You can remove app-private data through Android's **App info > Storage and cache > Clear storage** option. Clearing storage or uninstalling ZNeko erases your saved service credentials. Files stored in a folder you selected, including artwork, backups, and diagnostic reports, may remain after clearing storage or uninstalling ZNeko and must be deleted from that folder separately. External services retain information according to their own policies.

## Children's privacy

ZNeko Launcher is not directed to children under 13, and the ZNeko developers do not knowingly collect personal information from children.

## Changes to this policy

This policy may be updated when ZNeko's features or data handling change. The date at the top will identify the latest version.

## Contact

For privacy questions, contact the ZNeko project through its official repository:

- [https://github.com/zneko-org/zneko-launcher](https://github.com/zneko-org/zneko-launcher)
