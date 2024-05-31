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


I apologize for that oversight. Let's add the logic for downloading and installing the APK or app file into the provided script. We'll use `axios` for downloading files and appropriate commands for installation.

### Updated `package.json`

We'll need to add `axios` as a dependency for downloading files:

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
    "inquirer": "^8.0.0",
    "axios": "^0.21.1"
  },
  "author": "Your Name",
  "license": "MIT"
}
```

### Updated `src/index.js`

Now let's add the download and installation logic:

```javascript
#!/usr/bin/env node

const fs = require('fs');
const { execSync } = require('child_process');
const inquirer = require('inquirer');
const axios = require('axios');
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

async function downloadFile(url, outputPath) {
  const writer = fs.createWriteStream(outputPath);
  const response = await axios({
    url,
    method: 'GET',
    responseType: 'stream'
  });

  response.data.pipe(writer);

  return new Promise((resolve, reject) => {
    writer.on('finish', resolve);
    writer.on('error', reject);
  });
}

async function installAndroidApp(device) {
  const apkUrl = `${config.artifactoryUrl}/android/app.apk`;
  const outputPath = path.resolve(__dirname, 'app.apk');

  try {
    console.log(`Downloading APK from ${apkUrl}...`);
    await downloadFile(apkUrl, outputPath);
    console.log('APK downloaded successfully.');

    if (device) {
      console.log(`Installing APK on specified device ${device}...`);
      execSync(`adb -s ${device} install ${outputPath}`);
    } else {
      console.log('Installing APK on default emulator...');
      execSync(`adb install ${outputPath}`);
    }
    console.log('APK installed successfully.');
  } catch (error) {
    throw new Error(`Failed to download or install APK: ${error.message}`);
  }
}

async function installIosApp(device) {
  const appUrl = `${config.artifactoryUrl}/ios/app.app`;
  const outputPath = path.resolve(__dirname, 'app.app');

  try {
    console.log(`Downloading app from ${appUrl}...`);
    await downloadFile(appUrl, outputPath);
    console.log('App downloaded successfully.');

    if (device) {
      console.log(`Installing app on specified device ${device}...`);
      execSync(`xcrun simctl install ${device} ${outputPath}`);
    } else {
      console.log('Installing app on default simulator...');
      const defaultDevice = execSync(`xcrun simctl list devices available | grep 'iPhone' | head -n 1 | awk -F '[()]' '{print $2}'`).toString().trim();
      execSync(`xcrun simctl install ${defaultDevice} ${outputPath}`);
    }
    console.log('App installed successfully.');
  } catch (error) {
    throw new Error(`Failed to download or install app: ${error.message}`);
  }
}
```

This script now includes the logic for downloading the APK or app file using `axios` and installing it on the specified device using `adb` for Android and `xcrun simctl` for iOS. 

### Steps to Test and Publish
1. **Install Dependencies**:
   ```sh
   npm install
   ```

2. **Run Locally**:
   ```sh
   node src/index.js --platform android
   ```

3. **Publish to npm**:
   ```sh
   npm login
   npm publish
   ```

This ensures that the script meets the specified requirements, including user-friendly error messages and handling installation on the specified or default device.
