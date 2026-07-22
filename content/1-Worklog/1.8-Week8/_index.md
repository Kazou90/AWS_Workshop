---
title: "Week 8 Worklog"
date: "2026-06-08"
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Week 8 Objectives:
- Configure **Amazon Cognito User Pools** to handle user registration, secure login, and email/SMS OTP verification workflows.
- Package **React Vite Frontend** applications, uploading build artifacts to **Amazon S3** and distributing endpoints globally via **Amazon CloudFront CDN** with HTTPS encryption.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference/Material |
| --- | --- | --- | --- | --- |
| 1 (Mon) | **Configure Cognito User Pool & OTP Triggers:** Set up user signup verification policies in Cognito User Pools, configuring automated OTP delivery via Email. | 08/06/2026 | 08/06/2026 | <https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-settings-email-phone-verification.html> |
| 2 (Tue) | **Integrate Auth SDK into React Frontend:** Integrate AWS Amplify / Cognito Auth SDK into React Vite, implementing Signup forms, OTP Verification dialogs, and JWT token storage. | 09/06/2026 | 09/06/2026 | AWS Amplify Auth Docs |
| 3 (Wed) | **Build React Vite Frontend Assets:** Bundle the React Vite application into optimized static assets (`dist/` folder containing HTML, JS bundles, CSS) using `npm run build`. | 10/06/2026 | 10/06/2026 | Vite Build Guide |
| 4 (Thu) | **Deploy Frontend to S3 Bucket:** Upload built static assets to the S3 bucket, configuring Static Website Hosting properties and CloudFront read access policies. | 11/06/2026 | 11/06/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html> |
| 5 (Fri) | **CloudFront CDN & Single Page App Routing:** Create CloudFront Distributions targeting S3 origins, configuring Custom Error Responses (redirecting HTTP 403/404 errors to `index.html` for client routing). | 12/06/2026 | 12/06/2026 | <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/DefaultPages.html> |

### Week 8 Achievements:
* **Secure User Authentication via OTP:** Built a complete user onboarding and login experience backed by Amazon Cognito OTP verification.
* **Global Frontend Deployment via CloudFront:** Deployed React Vite E-Commerce frontend builds onto S3 and CloudFront, delivering lightning-fast page loading over encrypted HTTPS connections.
