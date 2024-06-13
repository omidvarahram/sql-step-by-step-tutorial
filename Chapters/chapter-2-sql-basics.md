const axios = require('axios');

async function getPackageVersion(repoUrl) {
  try {
    // Extract the repository path from the URL
    const repoPath = repoUrl.replace('github.something.com', 'api.github.com/repos');

    // Fetch the package.json file
    const response = await axios.get(`https://${repoPath}`, {
      headers: {
        'Accept': 'application/vnd.github.v3.raw'
      }
    });

    // Parse the JSON content
    const packageJson = response.data;
    const version = packageJson.version;

    console.log(`Version: ${version}`);
  } catch (error) {
    console.error(`Error fetching package.json: ${error.message}`);
  }
}

// Replace with your actual repository URL
const repoUrl = 'github.something.com/RNS/Package.json';
getPackageVersion(repoUrl);
