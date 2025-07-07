### Install dependencies:

```bash
сd hivetrace-docs
npm install
```

### Local Development

```bash
npm start
```

### Building for Production

Generate static files for production:

```bash
npm run build
```

The build artifacts will be stored in the `build/` directory.

### Testing the Build Locally

To test the production build locally:

```bash
npm run serve
```
### Deploy to GitHub Pages

Deploy the application to GitHub Pages:

```bash
GIT_USER=your_github_username GIT_PASS=your_personal_access_token npm run deploy
```

## Available Scripts

| Script | Description |
|--------|-------------|
| `npm start` | Starts the development server |
| `npm run build` | Builds the app for production |
| `npm run serve` | Serves the production build locally |
| `npm run deploy` | Deploys to GitHub Pages |
