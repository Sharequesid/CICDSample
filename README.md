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

## Steps are used to create yaml file for creating and uploading apk/aab file.For sample code please see AndroidBuild.yml file and steps in comment.
# 1. Checkout
- This action checks-out your repository under $GITHUB_WORKSPACE so your workflow can access it.
```yaml
- name: Checkout
  uses: actions/checkout@v2
```

# 2. Set up JDK
- Here, the goal is simple. To set up a JDK version to Java Version.
```yaml
- name: Setup Java JDK
  uses: actions/setup-java@v4.0.0
  with:
    java-version: '17'
    distribution: 'adopt'
```

# 3. Build with Gradle
- Build your app with Gradle. It is the same command you use in your terminal to build the project.
```yaml
- name: Build with Gradle
  id: build
  run: ./gradlew build
```

# 4. Run Unit tests (Optional)
- It runs unit tests located in the project. You can skip this step if you have not written any tests.
```yaml
- name: Unit tests
  id: tests
  run: ./gradlew test
```

# 5. Build Release Bundle(AAB)
- Time to build your release bundle. You can also build APK with assembleRelease command if you want. But I prefer bundle to optimize app size.
```yaml
- name: Build Release AAB
  id: buildRelease
  run: ./gradlew bundleRelease
```


# 6. Sign Bundle
- To use these keys from the workflow, we’ll add it to GitHub Secrets. So it’ll not be exposed.
- To add Secrets, open your repository and go to Settings.
- Now click on Secrets and variables from left panel-> Actions -> in secret tab - Repository secrets-> create
- Add the following Secrets in your repository

* SIGNING_KEY — base64 text of your Keystore file
* ALIAS — your alias name
* KEY_STORE_PASSWORD — password of your Keystore
* KEY_PASSWORD — your key password

```yaml
- name: Sign AAB
  id: sign
  uses: r0adkll/sign-android-release@v1
  # r0adkll/sign-android-release@v1 used for creating signed build.
  with:
    releaseDirectory: app/build/outputs/bundle/release
    signingKeyBase64: ${{ secrets.SIGNING_KEY }}
    alias: ${{ secrets.ALIAS }}
    keyStorePassword: ${{ secrets.KEY_STORE_PASSWORD }}
    keyPassword: ${{ secrets.KEY_PASSWORD }}
    env:
        # Setting build tool version will reduce error if we comment this line it will through exception for build tool version 29.*
        BUILD_TOOLS_VERSION: "34.0.0"
```

# 7. Upload Bundle(Optional)
- This step is also optional. it is not needed until you need to download that build.
```yaml
- name: Upload AAB
  id: uploadArtifact
  uses: actions/upload-artifact@v4
  with:
      name: app
      path: app/build/outputs/bundle/release/app-release.aab
```


# 8. Prepare Play Store Service Account
- You’ll need a service account to publish the app on the Play Store automatically.
- If you already have a service account, download the JSON file, copy all content, and paste in Secrets.
- Add the Secret in your repository - SERVICE_ACCOUNT_JSON — content of JSON file

- If you don’t have a service account, follow the below steps to create one.

- Enable Google APIs in the Google Cloud Console
1. Go to the Google Cloud Console. 
2. Select your project from the projects list or create a new one.
3. Go to the API & Services tab and click the "Enable API and Services" button.
4. In the API Library tab, search for the Google Play Developer Reporting API.
5. Enable the API for your project.
6. Enable Google Play Android Developer API.
7. Enable Cloud Pub/Sub API(Optional).

- Go to the Google Cloud Console ➡ IAM & Admin ➡ Service Accounts.
1. Click Create Service Account.
2. Enter your service account name, copy its ID, and click Create and Continue.
3. Grant this service account access to the project by enabling:
4. Skip the last step and click Done.

- Create and download the service key
1. Go to the Service Accounts section in the Google Cloud Console, choose your recently created key, click on the three dots to open the Actions dropdown menu, and then choose Manage Keys.
2. Click the Add Key button, then click Create new key.
3. In the pop-up window, ensure that JSON is chosen and click Create to generate and download the JSON Key on your computer. Downloaded JSON required for adding secret in github.

- Go to the Google Play account, 
1. Navigate to the Users and Permissions section in the Google Play Console and click Invite new users.
2. Enter the email of the service account/Copied ID that you created before, select your application, and click Apply.
3. Grant the following account permissions and click Invite user at the bottom of the page. Afterwards, you will be directed to the Users and Permissions section, where you should observe your recently established account listed as active.


# 9. Deploy to Play Store
- This action will help you upload an Android .apk or .aab file to the Google Play Console using the Google Play Developer API v3.
```yaml
- name: Deploy to Play Store (BETA)
  id: deploy
  uses: r0adkll/upload-google-play@v1
  with:
    serviceAccountJson: service_account.json
    packageName: com.package
    releaseFile: app/build/outputs/bundle/release/app-release.aab
    track: beta
    whatsNewDirectory: whatsnew/
```

# Screens

<p>
    <img src="images/ci_cd_sample1.jpg" width="350" title="First screen">
    <img src="images/ci_cd_sample2.jpg" width="350" alt="second screen">
    <img src="images/ci_cd_sample3.jpg" width="350" alt="after all things done">
</p>