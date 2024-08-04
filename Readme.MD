# Combined Json-Server

## Create Dir src/combine/combined.js
Make sure your folder structure looks like this:
```
project-root
├── src
│   ├── combine
│   │   └── combined.js
├── package.json

```
## Contents of combined.js: 
Contents of the combined.js file in the src/combine folder:
```javascript
import { promises as fs } from "fs";
import path from "path";
import { fileURLToPath } from "url";

async function combineJSON() {
  const __filename = fileURLToPath(import.meta.url);
  const __dirname = path.dirname(__filename);

  const apiFolder = path.join(__dirname, "..", "api");
  const jsonFiles = (await fs.readdir(apiFolder)).filter((file) =>
    file.endsWith(".json")
  );

  const combinedData = await jsonFiles.reduce(async (accPromise, file) => {
    const acc = await accPromise;
    const filePath = path.join(apiFolder, file);
    const fileData = JSON.parse(await fs.readFile(filePath, "utf8"));
    return { ...acc, ...fileData };
  }, Promise.resolve({}));

  await fs.writeFile(
    path.join(__dirname, "combined.json"),
    JSON.stringify(combinedData, null, 2)
  );
}

combineJSON().catch(console.error);
```

## Update Script in package.json:
Make sure the script in package.json is correct:
```json
 "scripts": {
    "combine-json": "node src/combine/combined.js",
    "server": "npm run combine-json && json-server --watch src/combine/combined.json --port 8000"
  },
//or if use pnpm
 "scripts": {
    "combine-json": "node src/combine/combined.js",
    "server": "pnpm run combine-json && json-server --watch src/combine/combined.json --port 8000"
  },
// or if use bun
 "scripts": {
    "combine-json": "node src/combine/combined.js",
    "server": "bun combine-json && json-server --watch src/combine/combined.json --port 8000"
  },
```

## Run JSON Server:
Now you can run JSON Server with the command:
```javascript
npm run server
//or
pnpm run server
//or
bun server
```

## Output file combined.json:
The output result in the src/combine/combined.json file directory is like this:
```
project-root
├── src
│   ├── combine
│   │   └── combined.js
│   │   └── combined.json (output file)
├── package.json

```

