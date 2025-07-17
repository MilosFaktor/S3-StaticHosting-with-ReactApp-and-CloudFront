## Do you want to see all screenshots from this project? 
👉 [All screenshots](screenshots/)

# 📝 Build Journal: React App on AWS S3 with Static Hosting + CloudFront

This journal documents the step-by-step process of hosting a React application on AWS S3 with CloudFront for global distribution and HTTPS support.

**Diagram**

<img src="../screenshots/1-diagram-no cache.png" width="750">

<img src="../screenshots/1.1-diagram-cloudfront.png" width="750">

## 1. Setting up React App on Windows PowerShell

- Attempted to run `npx create-react-app demo-app` but encountered an error because **Node.js** was not installed.
- Installed **Node.js**, which resolved issues with the `npm` command. However, the `npx` command was still not recognized.
- Discovered that `npx` was disabled due to execution policy settings. Fixed this by running:
``` bash
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```
- Successfully created the React app by running:
``` bash
npx create-react-app demo-app
```
- Verified that the React app works by launching it in PowerShell:
``` bash
npm start
```
- Generated a production build for deployment:
``` bash
npm run build
```

**Screenshot:**

<img src="../screenshots/react app created.png" width="750">

## 2. Creating Amazon S3 Buckets

- Created two **S3 buckets**:
    - `www.cloudnecessities.com`: The primary bucket to host the website.
    - `cloudnecessities.com`: A secondary bucket used only to redirect to the `www` domain.
- Both buckets were created in the `eu-north-1` region.
- This setup is ideal for static website hosting.

**Screenshot:**

<img src="../screenshots/S3 buckets overview.png" width="750">

## 3. Configuring S3 Bucket: `www.cloudnecessities.com`

- Uploaded files and folders from the React app’s production build (`build/` folder) to this bucket.
- Disabled **"Block all public access"** to make the bucket content publicly available.
- Created a bucket policy to allow public access.
- Verified that the site is accessible by visiting the `index.html` object URL.
- Enabled static **website hosting** in the bucket's **Properties:**
    - Hosting type: Host a static website.
    - Specified `index.html` as the entry point.

**Screenshot:**

<img src="../screenshots/uploading files into S3 bucker.png" width="750">

<img src="../screenshots/s3 bucket policy.png" width="750">

## 4. Configuring S3 Bucket: `cloudnecessities.com`

- Enabled static **website hosting** in the bucket's **Properties:**
    - Hosting type: Redirect requests for an object.
    - Redirect target: `www.cloudnecessities.com`.
    - Protocol: HTTP (later upgraded to HTTPS via CloudFront).
This configuration ensures users visiting `cloudnecessities.com` are redirected to the `www` domain.

## 5. Connecting Domain via Route 53

- Opened Route 53 and accessed the hosted zone for `cloudnecessities.com`.
- Created two **A records:**
    - One for `www.cloudnecessities.com` pointing to the S3 bucket (alias).
    - One for `cloudnecessities.com` pointing to the S3 bucket for redirection.
- Verified that `cloudnecessities.com` redirects properly to `www.cloudnecessities.com`.

**Screenshot:**

<img src="../screenshots/route53 records.png" width="750">

## 6. Setting Up CloudFront and SSL/TLS

- Requested an SSL/TLS certificate in **AWS Certificate Manager (ACM):**
    - Chose Request a public certificate.
    - Added both domains: `www.cloudnecessities.com` and `cloudnecessities.com`.
    - Selected DNS validation and confirmed.
- Created CNAME records in Route 53 directly from ACM for DNS validation.
- Once validated, ACM showed the certificate as **Issued**.

**Screenshot:**

<img src="../screenshots/N.Virginia certificate.png" width="750">

## 7. Configuring CloudFront Distribution

- Opened CloudFront and created a distribution for `www.cloudnecessities.com`:
    - Set the origin to the S3 bucket website endpoint.
    - Viewer protocol policy: Redirect HTTP to HTTPS.
    - Added `www.cloudnecessities.com` as an alternate domain (CNAME).
    - Selected the SSL/TLS certificate from ACM.
- Repeated the process to create a second CloudFront distribution for `cloudnecessities.com`.
- Updated `cloudnecessities.com` S3 bucket settings to redirect using HTTPS.
- Tested CloudFront domain in the browser to verify the website worked and content was served via CloudFront.

**Screenshot:**

<img src="../screenshots/CloudFront domain.png" width="750">

## 8. Updating DNS Records in Route 53

- Modified the existing **A records** for both `www.cloudnecessities.com` and `cloudnecessities.com`:
    - Changed traffic routing to Alias to CloudFront Distribution.
    - Selected the correct CloudFront domain for each.
- Verified everything was working:
    - Domain points to CloudFront.
    - CloudFront fetches content from S3 buckets.
    - HTTPS is enabled globally.

**Screenshot:**

<img src="../screenshots/route53 records CNAME records.png" width="750">

<img src="../screenshots/CloudFront set up website console.png" width="750">

## ✅ Deployment Complete

Your React app is now served globally with secure HTTPS, automatic redirection from `cloudnecessities.com` to `www.cloudnecessities.com`, and CloudFront CDN caching for optimal performance.

## 🧑‍💻 Author
👋 Milos Faktor 💼 [LinkedIn](https://www.linkedin.com/in/milos-faktor-78b429255/)

#AWS #ReactJS #S3Hosting #CloudFront #WebAppDeployment #StaticWebsite #Serverless #BuildJourney #DevOps #FrontendDeveloper
