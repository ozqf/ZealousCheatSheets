# Snippets

## Find all files of type

```
const searchPath
const fs = require("fs");
const path = require("path");
let files = fs.readdirSync(searchPath);
files = files.filter(file => (path.extname(file) === ".json"));
```

