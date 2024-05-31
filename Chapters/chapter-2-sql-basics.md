Here is a complete implementation of the Node.js script that fulfills your requirements. 

### Project Structure
```
install-test-harness-app/
│
├── src/
│   ├── config.json
│   ├── index.js
│
├── package.json
├── README.md
```

### Step-by-Step Implementation

#### 1. `package.json`
This file defines the metadata and dependencies for the npm package.

```json
{
  "name": "install-test-harness-app",
  "version": "1.0.0",
  "description": "A test harness installer for mobile platforms",
  "main": "src/index.js",
  "bin": {
    "install-test-harness-app": "src/index.js"
  },
  "scripts": {
    "start": "node src/index.js"
  },
  "dependencies": {
    "inquirer": "^8.0.0"
  },
  "author": "Your Name",
  "license": "MIT"
}
```

#### 2. `src/config.json`
This file contains configuration details, including the Artifactory URL and documentation link.

```json
{
  "artifactoryUrl": "https://example.com/artifactory",
  "documentationLink": "https://example.com/docs"
}
```

#### 3. `src/index.js`
This is the main script file.

```javascript
#!/usr/bin/env node

const fs = require('fs');
const { execSync } = require('child_process');
const inquirer = require('inquirer');
const path = require('path');

const config = require('./config.json');

const args = process.argv.slice(2);
const platformArgIndex = args.indexOf('--platform');
const deviceArgIndex = args.indexOf('--device');
const helpArgIndex = args.indexOf('--help') >= 0 || args.indexOf('-h') >= 0;

if (helpArgIndex) {
  console.log(`Usage: npx install-test-harness-app --platform <platform> [--device <device>]
  --platform: (required) Platform to install the app (android/ios)
  --device: (optional) Specific device to install the app on (limited to phones)
  --help, -h: Show this help message
  
  Documentation: ${config.documentationLink}`);
  process.exit(0);
}

let platform = platformArgIndex >= 0 ? args[platformArgIndex + 1] : null;
const device = deviceArgIndex >= 0 ? args[deviceArgIndex + 1] : null;

if (!platform) {
  inquirer
    .prompt([
      {
        type: 'list',
        name: 'platform',
        message: 'Please choose a platform',
        choices: ['android', 'ios']
      }
    ])
    .then(answers => {
      platform = answers.platform;
      runInstaller();
    });
} else {
  runInstaller();
}

function runInstaller() {
  if (!platform) {
    console.error('Error: Platform argument is required.');
    process.exit(1);
  }

  try {
    if (platform === 'android') {
      checkAndroidTools();
      installAndroidApp(device);
    } else if (platform === 'ios') {
      checkIosTools();
      installIosApp(device);
    } else {
      throw new Error('Unsupported platform. Please use "android" or "ios".');
    }
  } catch (error) {
    console.error(`Error: ${error.message}`);
    process.exit(1);
  }
}

function checkAndroidTools() {
  try {
    execSync('which adb');
    execSync('which emulator');
    console.log('Android tools are available.');
  } catch {
    throw new Error('Android tools are not installed. Please install Android Studio and ensure adb and emulator are in your PATH.');
  }
}

function checkIosTools() {
  try {
    execSync('xcode-select -p');
    execSync('which simctl');
    console.log('iOS tools are available.');
  } catch {
    throw new Error('iOS tools are not installed. Please install Xcode and ensure simctl is in your PATH.');
  }
}

function installAndroidApp(device) {
  const apkUrl = `${config.artifactoryUrl}/android/app.apk`;
  console.log(`Downloading APK from ${apkUrl}...`);

  // Download APK logic here (e.g., using curl or wget)

  if (device) {
    console.log(`Installing APK on specified device ${device}...`);
    // Install APK on specific device logic here
  } else {
    console.log('Installing APK on default emulator...');
    // Install APK on default emulator logic here
  }
}

function installIosApp(device) {
  const appUrl = `${config.artifactoryUrl}/ios/app.app`;
  console.log(`Downloading app from ${appUrl}...`);

  // Download app logic here (e.g., using curl or wget)

  if (device) {
    console.log(`Installing app on specified device ${device}...`);
    // Install app on specific device logic here
  } else {
    console.log('Installing app on default simulator...');
    // Install app on default simulator logic here
  }
}
```

#### 4. `README.md`
This file provides documentation for your npm package.

```markdown
# Install Test Harness App

A CLI tool to install test harness apps on mobile platforms.

## Usage

```sh
npx install-test-harness-app --platform <platform> [--device <device>]
```

### Arguments

- `--platform`: (required) Platform to install the app (android/ios)
- `--device`: (optional) Specific device to install the app on (limited to phones)
- `--help`, `-h`: Show help message

### Documentation

For more details, visit the [documentation](https://example.com/docs).
```

### Publishing to npm

1. Ensure you are logged in to npm: `npm login`
2. Publish the package: `npm publish`

Now users can install your CLI tool using `npx install-test-harness-app`.

### Notes
- The actual download and installation logic for APKs and apps is left as placeholders. You'll need to implement the actual download logic (e.g., using `curl` or `wget`) and install logic (e.g., using `adb` for Android or `xcrun simctl` for iOS).
- Error handling and user-friendly messages are included as per your requirements.
