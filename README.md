# CI/CD Sample app is for creating ci/cd for building artifacts
- This sample code is only for demo purpose i will only create a build not to publish on any store.
- publishing on play store is require some setup in play store account so i will skip this step.

#
- CI- running unit test and creating build 
- CD- Deploy app on maven or play store.


- Checkout
- Set up JDK 
- Build with Gradle
- Run Unit tests(Optional)
- Build Release Bundle(AAB)
- Sign Bundle
- Upload Bundle(Optional)
- Prepare Play Store Service Account
- Deploy to Play Store
- Notify Build status on Slack(Optional)

## Steps are used to create yaml file for creating and uploading apk/aab file.For sample code please see AndroidBuild.yml file and steps in comment.
# 1. Checkout
- This action checks-out your repository under $GITHUB_WORKSPACE so your workflow can access it.

# 2. Set up JDK
- Here, the goal is simple. To set up a JDK version to Java Version.

# 3. Build with Gradle
- Build your app with Gradle. It is the same command you use in your terminal to build the project.

# 4. Run Unit tests
- It runs unit tests located in the project. You can skip this step if you have not written any tests.

# 5. Build Release Bundle(AAB)
- Time to build your release bundle. You can also build APK with assembleRelease command if you want. But I prefer bundle to optimize app size.

# 6. Sign Bundle
- To use these keys from the workflow, we’ll add it to GitHub Secrets. So it’ll not be exposed.
- To add Secrets, open your repository and go to Settings.
- Now click on Secrets and variables from left panel-> Actions -> in secret tab - Repository secrets-> create
- Add the following Secrets in your repository

SIGNING_KEY — base64 text of your Keystore file
ALIAS — your alias name
KEY_STORE_PASSWORD — password of your Keystore
KEY_PASSWORD — your key password