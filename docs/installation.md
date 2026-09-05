# Installation

Docsify can be used directly from a CDN for a documentation website, or installed locally when you want to develop Docsify itself.

## Requirements

For local development, use **Node.js 20.11.0 or later**.

You can verify your Node.js version with:

```bash
node --version
```

## Install Docsify CLI

The recommended way to create and preview a Docsify website is to install `docsify-cli` globally:

```bash
npm install --global docsify-cli
```

Verify the installation:

```bash
docsify --version
```

You can then initialize a new documentation site:

```bash
docsify init ./docs
```

Start the local preview server with:

```bash
docsify serve docs
```

Open `http://localhost:3000` in your browser.

## Install Docsify from npm

If you want to include Docsify as a dependency in an existing project, install the npm package:

```bash
npm install docsify
```

You can also pin a specific version:

```bash
npm install docsify@5
```

For browser-based sites, Docsify can also be loaded from a CDN without installing it through npm.

## Set up the Docsify repository

If you are contributing to Docsify or working on its source code, clone the repository and install its dependencies:

```bash
git clone https://github.com/docsifyjs/docsify.git
cd docsify
npm install
```

The repository requires Node.js `>=20.11.0` and uses npm scripts for development, building, linting, and testing.

### Start development mode

Run:

```bash
npm run dev
```

This starts the development server and watches the JavaScript and CSS sources for changes.

### Build the project

Create a production build with:

```bash
npm run build
```

### Run tests

Run the complete test suite with:

```bash
npm test
```

For more information about contributing to the project, see `CONTRIBUTING.md` in the repository root.

## Troubleshooting

### Node.js version is too old

Check your version:

```bash
node --version
```

Upgrade Node.js to version 20.11.0 or later, then reinstall dependencies:

```bash
rm -rf node_modules
npm install
```

### `docsify` command is not found

If `docsify` is not available after installing the CLI, verify that the global npm binary directory is on your `PATH`:

```bash
npm prefix --global
```

Alternatively, run the CLI without a global installation:

```bash
npx docsify-cli serve docs
```
