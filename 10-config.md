## 🧩 1. What is `tsconfig.json`?

It’s a JSON file that defines how TypeScript should behave for your project.

Example:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "commonjs",
    "strict": true,
    "outDir": "dist",
    "rootDir": "src",
    "esModuleInterop": true
  },
  "include": ["src"],
  "exclude": ["node_modules"]
}
```

You create it with:

```bash
npx tsc --init
```

---

## 🛠️ 2. Core `compilerOptions` Explained

Here are the most useful ones (you’ll see these in almost every project):

| Option                               | Description                                                    | Example                                     |
| ------------------------------------ | -------------------------------------------------------------- | ------------------------------------------- |
| **target**                           | JS version output (e.g. `ES5`, `ES2017`, `ESNext`)             | `"target": "ES2022"`                        |
| **module**                           | Module system (Node.js uses `commonjs`, bundlers use `ESNext`) | `"module": "ESNext"`                        |
| **rootDir**                          | Where TypeScript source files are located                      | `"rootDir": "src"`                          |
| **outDir**                           | Where compiled JS files go                                     | `"outDir": "dist"`                          |
| **strict**                           | Enables *all* strict type-checking rules                       | `"strict": true"`                           |
| **esModuleInterop**                  | Allows default imports from CommonJS modules                   | `"esModuleInterop": true"`                  |
| **skipLibCheck**                     | Skips type checking of declaration files (`.d.ts`)             | `"skipLibCheck": true"`                     |
| **forceConsistentCasingInFileNames** | Avoids file name case mismatches                               | `"forceConsistentCasingInFileNames": true"` |
| **allowJs**                          | Let TypeScript compile JS files too                            | `"allowJs": true"`                          |
| **checkJs**                          | Type-check JS files                                            | `"checkJs": true"`                          |
| **resolveJsonModule**                | Allow importing `.json` files                                  | `"resolveJsonModule": true"`                |
| **moduleResolution**                 | How modules are resolved (`node` or `bundler`)                 | `"moduleResolution": "node"`                |
| **jsx**                              | How JSX should be compiled (for React)                         | `"jsx": "react-jsx"`                        |

---

## 🧱 3. Project Structure Example

```
my-project/
├── src/
│   ├── index.ts
│   ├── utils.ts
├── dist/          ← compiled output
├── tsconfig.json
└── package.json
```

When you run:

```bash
npx tsc
```

TypeScript:

* Reads `tsconfig.json`
* Compiles files from `src/`
* Outputs JS into `dist/`

---

## ⚙️ 4. Common Presets

### For Node.js app

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "outDir": "dist",
    "rootDir": "src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true
  }
}
```

### For React app

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "jsx": "react-jsx",
    "strict": true,
    "moduleResolution": "node",
    "allowSyntheticDefaultImports": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src"]
}
```

---

## 🧠 5. Advanced: Inheritance & Multiple Configs

You can extend another config (useful for monorepos or libraries):

**tsconfig.json**

```json
{
  "extends": "./tsconfig.base.json",
  "compilerOptions": {
    "outDir": "dist"
  },
  "include": ["src"]
}
```

---

## 📦 6. Build Commands

| Command            | Description                       |
| ------------------ | --------------------------------- |
| `npx tsc`          | Compile TypeScript once           |
| `npx tsc -w`       | Watch mode — recompile on save    |
| `npx tsc --noEmit` | Check types only, don’t output JS |

---

## 🧭 7. Bonus Tips

* Add a `"types"` field in `package.json` if you publish your own package.
* Use `"declaration": true` to generate `.d.ts` files (for libraries).
* Combine with **ESLint** for code style enforcement.

---

Would you like me to walk you through **each section of `tsconfig.json` line-by-line** (e.g., what every option inside `"compilerOptions"` means in depth)?
That’s great for mastering it as a senior developer.
