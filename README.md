# Dating App

## Work demo - https://dimakolbasin.github.io/dating-app/

## Prerequisites

Before running this project, ensure you have the following installed:

- **Node.js** (version 18.0.0 or higher)
- **Yarn** package manager

## Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd dating-app
```

2. Install dependencies:
```bash
yarn install
```

## Development

To start the development server:

```bash
yarn dev
```

This will start the Vite development server. Open your browser and navigate to the URL displayed in the terminal (typically `http://localhost:5173`).

The development server includes:
- Hot Module Replacement (HMR)
- TypeScript compilation
- SCSS preprocessing
- ESLint integration

## Building for Production

To build the application for production:

```bash
yarn build
```

The built files will be generated in the `dist` directory.

## Preview Production Build

To preview the production build locally:

```bash
yarn preview
```

This will serve the built application from the `dist` directory.