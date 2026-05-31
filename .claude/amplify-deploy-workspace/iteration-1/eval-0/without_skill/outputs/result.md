# Amplify Deployment Result

## Steps Attempted

1. Inspected the project structure — static site with `index.html`, `resume/index.html`, `CNAME` (wecraw.com), and supporting files.
2. Checked for existing AWS Amplify configuration (`amplify.yml`, etc.) — none found in the repo.
3. Ran `aws amplify list-apps` to check for an existing Amplify app — found one: **wecraw-resume** (appId: `d107gyx2unpg5z`).
4. Checked the app's branches and job history — a `main` branch exists with two prior successful deployments (jobs 1 and 2).
5. Confirmed the app is a **manual deployment** type (not connected to a repository provider), so `start-job --job-type RELEASE` is not supported.
6. Packaged the static files (`index.html`, `resume/`, `CNAME`) into a zip: `/tmp/resume-deploy.zip`.
7. Called `aws amplify create-deployment` to get a pre-signed S3 upload URL (job ID 6).
8. Uploaded the zip via `curl -T` to the pre-signed S3 URL.
9. Called `aws amplify start-deployment --job-id 6` to trigger the deployment.
10. Confirmed job 6 reached status **SUCCEED** (both DEPLOY and VERIFY steps passed).

## Commands Run

```bash
aws amplify list-apps --region us-east-1
aws amplify list-branches --app-id d107gyx2unpg5z --region us-east-1
aws amplify list-jobs --app-id d107gyx2unpg5z --branch-name main --region us-east-1
aws amplify get-app --app-id d107gyx2unpg5z --region us-east-1
zip -r /tmp/resume-deploy.zip index.html resume/ CNAME
aws amplify create-deployment --app-id d107gyx2unpg5z --branch-name main --region us-east-1
curl -T /tmp/resume-deploy.zip "<presigned-s3-url>"
aws amplify start-deployment --app-id d107gyx2unpg5z --branch-name main --job-id 6 --region us-east-1
aws amplify get-job --app-id d107gyx2unpg5z --branch-name main --job-id 6 --region us-east-1
```

## Deployment Result

**Succeeded.**

- App: `wecraw-resume`
- App ID: `d107gyx2unpg5z`
- Branch: `main`
- Job ID: 6
- Status: SUCCEED
- Default Amplify URL: https://main.d107gyx2unpg5z.amplifyapp.com
- Custom domain configured (via CNAME): wecraw.com (domain connection managed separately in Amplify/DNS)

## What Was Missing or Unclear

- No `amplify.yml` build spec was present in the repo, but this was not needed since the app is a manual (zip-upload) deployment rather than a CI/CD-connected one.
- The Amplify app was configured as a manual deployment app (no repository provider connection), which meant `start-job --job-type RELEASE` failed; the correct path was `create-deployment` + upload + `start-deployment`.
- It is unclear whether the custom domain `wecraw.com` (from the `CNAME` file, which appears to be a GitHub Pages artifact) has been configured in Amplify's domain management console. The CNAME file was included in the zip upload but Amplify custom domain setup requires separate DNS/console configuration.
