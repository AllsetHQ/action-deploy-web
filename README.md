# Allset deploy for web apps

## Prerequisites
1. Your project needs to define a `build:{environment}` script for each environment supported. Example: `build:edge`
2. A Firebase project alias should be present for each environment.
3. The Firebase hosting project should already be created.
4. A Sentry project under the Allset organization is created for this app.

## Usage
Adding the following to your workflow will build and deploy your web app to a given Firebase project.

```yaml
- uses: actions/checkout@v2
- name: Build and deploy
  uses: AllsetHQ/action-deploy-instrumented-web@main
  with:
    environment: edge
    release_version: ${{ github.sha }}
    firebase_token: ${{ secrets.FIREBASE_CI_TOKEN }}
    firebase_project: allset-web-tipping-edge
    sentry_org: ${{ secrets.SENTRY_ORG }}
    sentry_project: ${{ secrets.SENTRY_PROJECT }}
    sentry_auth_token: ${{ secrets.SENTRY_AUTH_TOKEN }}

- name: Send Slack notification of failed build
  if: failure()
  uses: rtCamp/action-slack-notify@v2
  env:
    SLACK_FOOTER: ''
    SLACK_COLOR: ${{ job.status }}
    SLACK_MESSAGE: ':dev::rocket::failed:'
    SLACK_TITLE: ' '
    SLACK_WEBHOOK: ${{ secrets.SLACK_BUILD_URL }}

- name: Send Slack notification of successful build
  if: success()
  uses: rtCamp/action-slack-notify@v2
  env:
    SLACK_FOOTER: ''
    SLACK_COLOR: ${{ job.status }}
    SLACK_MESSAGE: ':dev::rocket::done:'
    SLACK_TITLE: ' '
    SLACK_WEBHOOK: ${{ secrets.SLACK_BUILD_URL }}
```

### Inputs
#### Parameters
|name|description|required|default|
|---|---|---|---|
|`environment`| Set the environment for this release. E.g. "edge", "staging", or "production". |true|-|
|`release_version`| The version string to associate with this release. Useful for connecting instrumentation. |true|-|
|`firebase_project`| The name of the Firebase hosting project being deployed to. |true|-|
|`firebase_token`| The CI Token used to interact with Firebase. |true|-|
|`node_version`| The version of node to use for the build. |false|16.5.0|
|`sentry_org`| The Sentry organization ID to interact with. |true|-|
|`sentry_project`| The Sentry project ID to create a new release in. |true|-|
|`sentry_auth_token`| Authentication token to interact with Sentry. |true|-|
