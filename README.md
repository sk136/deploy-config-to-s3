# Deploy config to S3 with GitHub Actions

This repo contains a minimal GitHub Actions workflow that syncs the `config/` folder to an S3 bucket whenever config files change.

Setup
1. Create an AWS IAM user for CI with programmatic access and attach a policy similar to `iam-policy.json` (replace `your-bucket-name`).
2. Create an S3 bucket (or use an existing one).
3. In your GitHub repository go to Settings → Secrets and variables → Actions → New repository secret and add:
   - AWS_ACCESS_KEY_ID — the access key id for the IAM user
   - AWS_SECRET_ACCESS_KEY — the secret access key for the IAM user
   - AWS_REGION — e.g. us-east-1
   - S3_BUCKET — your-bucket-name
4. Commit/push changes to `config/` (or `settings.json` if you use that path). The workflow will run automatically and sync files to `s3://<S3_BUCKET>/config/`.

Notes
- The workflow uses `aws s3 sync ... --delete` which deletes remote objects that are not present locally. Remove `--delete` if you don't want that behavior.
- If you need public objects, adjust ACLs or bucket policy; for private objects, ensure your app authenticates when downloading.
- For CloudFront invalidation after update, add a step calling `aws cloudfront create-invalidation ...` (you'll need CloudFront permissions).