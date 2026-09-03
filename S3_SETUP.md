# Connecting ZNeko to S3 Storage

ZNeko can use any S3-compatible storage as a second place to keep your games and folder backups. The same steps apply to Amazon S3, Cloudflare R2, Wasabi, or a MinIO server you run yourself.

This guide uses **Backblaze B2**, which is the only provider ZNeko has been tested against — its free tier is generous and does not ask for a credit card. Anything else should work, but you would be the first to try it.

The bucket is yours. ZNeko never sees your files or your keys — everything is signed on your device and sent straight to the provider.

---

## 1. Create a Backblaze account

Sign up at [backblaze.com/sign-up/cloud-storage](https://www.backblaze.com/sign-up/cloud-storage).

When asked, pick the region closest to you. Brazilian users should choose **US East**.

> [!IMPORTANT]
> The region cannot be changed later, and a bucket can only be reached from the region it was created in.

## 2. Create a bucket

Go to [Buckets](https://secure.backblaze.com/b2_buckets.htm) and click **Create a Bucket**.

| Setting | Value |
| --- | --- |
| Bucket Unique Name | `zneko-b` |
| Files in Bucket are | **Private** |
| Default Encryption | **Disable** |
| Object Lock | **Disable** |

The name must be at least 6 characters and is unique across all of Backblaze, not just your account — if `zneko-b` is taken, pick another and use it everywhere below.

Keep **Object Lock** off. It enforces a retention period that would stop ZNeko from deleting a game or replacing a backup.

## 3. Create an application key

Go to [Application Keys](https://secure.backblaze.com/app_keys.htm) and click **Add a New Application Key**.

| Setting | Value |
| --- | --- |
| Name of Key | `zneko-key` |
| Allow access to Bucket(s) | `zneko-b` |
| Type of Access | **Read and Write** |

Leave the remaining optional fields empty, then click **Create New Key**.

> [!WARNING]
> The `applicationKey` is shown **once**. Save it now.
>
> The `keyID` can be looked up again later, the key itself cannot. If you lose it, delete the key and create a new one.
>
> Restrict the key to this one bucket. A key with account-wide access can read and delete everything in your Backblaze account, and it will be stored on your handheld.

## 4. Build the connection string

ZNeko takes the whole configuration as a single string:

```
s3://KEYID:APPLICATIONKEY@ENDPOINT/BUCKET
```

| Part | Where to find it |
| --- | --- |
| `KEYID` | [Application Keys](https://secure.backblaze.com/app_keys.htm), the `keyID` column |
| `APPLICATIONKEY` | The value you saved in step 3 |
| `ENDPOINT` | [Buckets](https://secure.backblaze.com/b2_buckets.htm), the **Endpoint** shown on your bucket |
| `BUCKET` | Your bucket name |

With the example values, it looks like this:

```
s3://005a1b2c3d4e5f60000000001:K005AbCdEfGhIjKlMnOpQrStUvWxYz0@s3.us-east-005.backblazeb2.com/zneko-b
```

ZNeko reads the region out of the endpoint, so there is nothing else to fill in.

## 5. Connect

On your handheld, open **Settings → Backups → S3 Storage**, press to enter the connection string, and paste it in.

ZNeko checks the whole configuration with a single request before saving it. If something is wrong it says which part: a rejected key pair, a bucket that is not there, or an endpoint that could not be reached. The provider's own error code is shown underneath, which is usually what tells two similar failures apart.

---

## What ZNeko puts in the bucket

```
roms/<platform>/<game files>
backups/<name>.zip     one folder backup
backups/<name>.json    that backup's size, file count, and folder fingerprint
```

Nothing else is written, and nothing outside these two prefixes is read.

## Recommended: keep only the latest version

On your bucket's **Lifecycle Settings**, choose **Keep only the last version of the file**.

Backblaze keeps every version of every object by default, so a re-uploaded game would be stored twice and a deleted one would still be billed. ZNeko already removes superseded versions itself when it uploads, deletes, or refreshes the backups list — the lifecycle rule is what cleans up anything left behind by an interrupted transfer.

## Costs

Backblaze's free tier covers 10 GB stored and 1 GB of downloads per day, which is comfortable for saves and a modest set of games. Transferring a large library will exceed the download allowance; check [Backblaze's pricing](https://www.backblaze.com/cloud-storage/pricing) before moving many gigabytes.

---

## Once connected

- **Settings → Backups → Games** lists every game with a badge for where its copies are, and lets you upload a local game, transfer a remote one, or delete one from the bucket.
- **Settings → Backups → Backups** pairs a folder on your device with a backup in the bucket, for emulator saves and configuration.
- Games in the bucket appear in your Library with an **S3** badge, next to the ones already on the device.

See the [Privacy Policy](PRIVACY_POLICY.md) for exactly what is sent to your provider and what is kept on your device.
