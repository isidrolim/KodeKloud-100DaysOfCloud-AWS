# 100 Days of Cloud AWS - Day 023: Data Migration Between S3 Buckets Using AWS CLI

## Scenario

The Nautilus DevOps team needed to migrate all data from an existing Amazon S3 bucket to a new destination bucket.

The migration had to preserve all objects without data loss, and the destination needed to be validated after synchronization.

## Requirement

Migrate all objects between the following S3 buckets:

- **Source Bucket:** `devops-s3-554714727`
- **Destination Bucket:** `devops-sync-554714727`
- **Method:** AWS CLI
- Ensure all source objects are copied successfully.
- Validate the destination after migration.

## Initial State

The existing S3 buckets were checked using:

```bash
aws s3 ls
```

Initial result:

```text
devops-s3-554714727
```

The source bucket existed, while the destination bucket had not yet been created.

## Verification Before Migration

Before copying any data, a baseline of the source bucket was collected.

```bash
aws s3 ls s3://devops-s3-554714727 \
  --recursive \
  --summarize | tail -2
```

Source baseline:

```text
Total Objects: 3782
Total Size:    110075734
```

This gave two values that could later be compared against the destination:

```text
Object Count
+
Total Data Size
```

## Create Destination Bucket

Create the new destination bucket:

```bash
aws s3 mb s3://devops-sync-554714727 \
  --region us-east-1
```

Verify that both buckets exist:

```bash
aws s3 ls
```

Expected:

```text
devops-s3-554714727
devops-sync-554714727
```

## Data Migration

Synchronize all objects from the source bucket to the destination bucket:

```bash
aws s3 sync \
  s3://devops-s3-554714727 \
  s3://devops-sync-554714727
```

The dependency path for the migration was:

```text
Source Bucket
devops-s3-554714727
        │
        │ aws s3 sync
        ▼
Destination Bucket
devops-sync-554714727
```

## Validation

After the synchronization completed, check the destination bucket:

```bash
aws s3 ls s3://devops-sync-554714727 \
  --recursive \
  --summarize | tail -2
```

Destination result:

```text
Total Objects: 3782
Total Size:    110075734
```

Compare the source and destination:

```text
SOURCE
Total Objects: 3782
Total Size:    110075734

DESTINATION
Total Objects: 3782
Total Size:    110075734
```

Both the object count and total size matched.

## Result

✅ Source bucket `devops-s3-554714727` verified.

✅ Source baseline collected before migration.

✅ Destination bucket `devops-sync-554714727` created.

✅ All objects synchronized using `aws s3 sync`.

✅ Source object count: `3782`.

✅ Destination object count: `3782`.

✅ Source size: `110075734` bytes.

✅ Destination size: `110075734` bytes.

✅ Migration successfully completed and validated.

## Key Takeaway

The `aws s3 sync` command compares the source and destination and copies objects that need to be transferred.

```text
aws s3 sync
     │
     ├── Compare source and destination
     │
     ├── Copy missing objects
     │
     └── Update changed objects
```

A good migration workflow is:

```text
Verify Source
     ↓
Record Baseline
     ↓
Create Destination
     ↓
Run Migration
     ↓
Validate Destination
     ↓
Compare Against Baseline
```

Simply seeing a successful `sync` command is not enough to prove that a migration completed correctly.

For this challenge, comparing:

```text
Total Objects
+
Total Size
```

provided a practical validation that the destination matched the source after migration.

In production, higher-assurance migrations may also include checks such as object inventories, checksums, metadata validation, versioning requirements, encryption settings, permissions, and application-level testing.
