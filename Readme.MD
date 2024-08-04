# Combined Json-Server

## Create Dir src/combine/combined.js
Pastikan struktur folder Anda seperti ini:
```
project-root
├── src
│   ├── combine
│   │   └── combine-json.js
│   │   └── combined.json (output file)
├── package.json

```
## Contents of combine-json.js: 
Contents of the combine-json.js file in the src/combine folder:
```javascript
import { promises as fs } from 'fs';
import path from 'path';
import { fileURLToPath } from 'url';

async function combineJSON() {
  const __filename = fileURLToPath(import.meta.url);
  const __dirname = path.dirname(__filename);

  const apiFolder = path.join(__dirname, '..', 'api');
  const jsonFiles = (await fs.readdir(apiFolder)).filter(file => file.endsWith('.json'));

  const combinedData = await jsonFiles.reduce(async (accPromise, file) => {
    const acc = await accPromise;
    const filePath = path.join(apiFolder, file);
    const fileData = JSON.parse(await fs.readFile(filePath, 'utf8'));
    return { ...acc, ...fileData };
  }, Promise.resolve({}));

  await fs.writeFile(path.join(__dirname, 'combined.json'), JSON.stringify(combinedData, null, 2));
}

combineJSON().catch(console.error);


```
