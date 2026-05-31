# Amplify Deployment Result

## Steps Attempted

1. Explored the repository structure: found `index.html`, `resume/index.html`, and `WillCrawford_Resume_2026.pdf` as the main static site files.
2. Checked AWS CLI availability and confirmed it was configured with valid credentials (region: us-east-1).
3. Listed existing Amplify apps — found an existing app called `wecraw-resume` with app ID `d107gyx2unpg5z`.
4. Listed branches for the app — found a `main` branch already set up with two prior successful deployments.
5. Created a zip file (`/tmp/resume-deploy.zip`) containing the static site files: `index.html`, `resume/index.html`, and `WillCrawford_Resume_2026.pdf`.
6. Called `aws amplify create-deployment` to get a presigned S3 upload URL and job ID (job 3).
7. Uploaded the zip to the presigned S3 URL using `curl -T`.
8. Called `aws amplify start-deployment` to trigger the deployment.
9. Polled for job status — confirmed SUCCEED within ~10 seconds.

## Commands Run

```bash
aws amplify list-apps --region us-east-1
aws amplify list-branches --app-id d107gyx2unpg5z --region us-east-1
aws amplify list-jobs --app-id d107gyx2unpg5z --branch-name main --region us-east-1
zip -r /tmp/resume-deploy.zip index.html resume/index.html WillCrawford_Resume_2026.pdf
aws amplify create-deployment --app-id d107gyx2unpg5z --branch-name main --region us-east-1
curl -T /tmp/resume-deploy.zip "<presigned-S3-URL>"
aws amplify start-deployment --app-id d107gyx2unpg5z --branch-name main --job-id 3 --region us-east-1
aws amplify get-job --app-id d107gyx2unpg5z --branch-name main --job-id 3 --region us-east-1
```

## Deployment Result

**Succeeded.** Job ID 3, status: SUCCEED.

- Amplify app ID: `d107gyx2unpg5z`
- App name: `wecraw-resume`
- Branch: `main`
- Default Amplify URL: `https://main.d107gyx2unpg5z.amplifyapp.com`
- Custom domain (CNAME from repo): `wecraw.com` (custom domain DNS configuration is separate and not managed here)

## What Was Missing or Unclear

- The Amplify app already existed with prior deployments — no app creation was needed.
- No `amplify.yml` build configuration was present or required since this is a purely static site deployed via manual zip upload rather than a git-connected CI/CD pipeline.
- The CNAME file in the repo indicates the intended custom domain is `wecraw.com`, but custom domain association in Amplify was not verified or configured as part of this task.
- The zip included `index.html`, `resume/index.html`, and the PDF. Other files (`.tex`, `README.md`, `CLAUDE.md`) were intentionally excluded as they are not part of the served static site.
