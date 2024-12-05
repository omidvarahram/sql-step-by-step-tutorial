'''
const fs = require('fs');

// Sample input array
const packages = [
  "@someDomain/somepackage1@4.5.2.alpha.something",
  "@someDomain/somepackage2@3.3.3"
];

// Function to convert packages to JSON object
function parsePackages(packages) {
  const result = {};
  packages.forEach(pkg => {
    const match = pkg.match(/^(@?.+?)@(.+)$/);
    if (match) {
      const [, name, version] = match;
      result[name] = version;
    }
  });
  return result;
}

// Parse the packages
const parsedPackages = parsePackages(packages);

// Convert to JSON format
const jsonOutput = JSON.stringify(parsedPackages, null, 2);

// Write to a file (optional)
fs.writeFileSync('output.json', jsonOutput);

// Output to console
console.log(jsonOutput);


'''