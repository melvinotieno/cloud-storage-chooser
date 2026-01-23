# Cloud Storage Picker

A lightweight, TypeScript-first library for integrating file pickers from popular cloud storage providers like Dropbox and Google Drive into your web applications.

## Features

- 🎯 **Type-safe** - Built with TypeScript for full type safety and IntelliSense support
- 🔌 **Pluggable** - Easy-to-use provider architecture for different cloud storage services
- 🪶 **Lightweight** - Minimal bundle size with on-demand script loading
- 🎨 **Framework agnostic** - Works with React, Vue, Svelte, or vanilla JavaScript
- 📦 **Tree-shakeable** - Import only the providers you need

## Supported Providers

- ✅ Dropbox
- ✅ Google Drive
- 🔜 OneDrive (coming soon)

## Installation

```bash
npm install cloud-storage-picker
```

```bash
pnpm add cloud-storage-picker
```

```bash
yarn add cloud-storage-picker
```

## Quick Start

### Dropbox

```typescript
import { createPicker } from "cloud-storage-picker";
import { dropboxProvider } from "cloud-storage-picker/dropbox";

const picker = createPicker({
  provider: dropboxProvider({
    appKey: "your-dropbox-app-key",
  }),
});

// Open the picker
const files = await picker.open({
  multiSelect: true,
  linkType: "preview",
});

console.log(files);
// [{ id: '...', name: 'document.pdf', link: 'https://...', rawData: {...} }]
```

### Google Drive

```typescript
import { createPicker } from "cloud-storage-picker";
import { googleDriveProvider } from "cloud-storage-picker/google-drive";

const picker = createPicker({
  provider: googleDriveProvider({
    clientId: "your-google-client-id",
    apiKey: "your-google-api-key",
  }),
});

// Open the picker
const files = await picker.open({
  maxItems: 5,
});

console.log(files);
// [{ id: '...', name: 'presentation.pptx', link: 'https://...', rawData: {...} }]
```

## API Reference

### `createPicker(params)`

Creates a file picker instance for a given storage provider.

**Parameters:**

- `params.provider` - A storage provider instance (e.g., `dropboxProvider()` or `googleDriveProvider()`)

**Returns:**

- An object with an `open(options?)` method that returns a `Promise<FileData[]>`

### `FileData<T>`

The normalized file data structure returned by all providers:

```typescript
interface FileData<RawFileData> {
  id: string; // Unique identifier for the file
  name: string; // Name of the file
  link: string; // URL to access the file
  rawData: RawFileData; // Provider-specific raw file data
}
```

## Provider-Specific Documentation

### Dropbox Provider

#### Configuration

```typescript
dropboxProvider(config: DropboxConfig, options?: DropboxOptions)
```

**DropboxConfig:**

- `appKey` (required): Your Dropbox App Key. Get one from the [Dropbox App Console](https://www.dropbox.com/developers/apps).

**DropboxOptions:**

- `linkType`: `"preview"` | `"direct"` - Type of link to return (default: `"preview"`)
- `multiSelect`: `boolean` - Enable multiple file selection (default: `false`)
- `extensions`: `string[]` - Filter by file extensions or types (e.g., `[".pdf", "images"]`)
- `folderSelect`: `boolean` - Allow folder selection (default: `false`)
- `sizeLimit`: `number` - Maximum file size in bytes

**DropboxFileData:**

```typescript
interface DropboxFileData {
  id: string;
  name: string;
  bytes: number;
  isDir: boolean;
  link: string;
  linkType: "preview" | "direct";
  icon: string;
  thumbnailLink?: string;
}
```

#### Example

```typescript
const picker = createPicker({
  provider: dropboxProvider(
    { appKey: "your-app-key" },
    {
      multiSelect: true,
      extensions: [".pdf", ".docx", "images"],
      sizeLimit: 10 * 1024 * 1024, // 10MB
    },
  ),
});

const files = await picker.open();
```

### Google Drive Provider

#### Configuration

```typescript
googleDriveProvider(config: GoogleDriveConfig, options?: GoogleDriveOptions)
```

**GoogleDriveConfig:**

- `clientId` (required): Your Google OAuth 2.0 Client ID
- `apiKey` (required): Your Google API Key

Get credentials from the [Google Cloud Console](https://console.cloud.google.com/).

**GoogleDriveOptions:**

- `maxItems`: `number` - Maximum number of items a user can select

**GoogleDriveFileData:**

```typescript
interface GoogleDriveFileData {
  id: string;
  name: string;
  mimeType: string;
  url: string;
}
```

#### Example

```typescript
const picker = createPicker({
  provider: googleDriveProvider(
    {
      clientId: "your-client-id.apps.googleusercontent.com",
      apiKey: "your-api-key",
    },
    { maxItems: 3 },
  ),
});

const files = await picker.open();
```

## Advanced Usage

### Default Options

You can set default options when creating a provider and override them per call:

```typescript
const picker = createPicker({
  provider: dropboxProvider(
    { appKey: "your-app-key" },
    { multiSelect: false }, // Default
  ),
});

// Override defaults for specific calls
const singleFile = await picker.open(); // Uses multiSelect: false
const multipleFiles = await picker.open({ multiSelect: true }); // Override
```

### Error Handling

```typescript
try {
  const files = await picker.open();
  console.log("Selected files:", files);
} catch (error) {
  if (error.message.includes("cancelled")) {
    console.log("User cancelled the picker");
  } else {
    console.error("Picker error:", error);
  }
}
```

### Accessing Raw Provider Data

Each `FileData` object includes a `rawData` property with the complete, unmodified response from the provider:

```typescript
const files = await picker.open();

files.forEach((file) => {
  console.log("Normalized:", file.id, file.name);
  console.log("Raw Dropbox data:", file.rawData);
  // Access provider-specific fields like bytes, icon, thumbnailLink, etc.
});
```

## Setup Requirements

### Dropbox

1. Create an app at the [Dropbox App Console](https://www.dropbox.com/developers/apps)
2. Enable "Chooser" permissions
3. Add your domain to the allowed domains list
4. Copy your App Key

### Google Drive

1. Go to the [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select an existing one
3. Enable the Google Picker API and Google Drive API
4. Create OAuth 2.0 credentials:
   - Add authorized JavaScript origins (e.g., `http://localhost:3000`)
5. Create an API Key
6. Copy your Client ID and API Key

## License

MIT © [Melvin Otieno](https://melvinotieno.com)

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Links

- [GitHub Repository](https://github.com/melvinotieno/cloud-storage-picker)
- [Issue Tracker](https://github.com/melvinotieno/cloud-storage-picker/issues)
- [NPM Package](https://www.npmjs.com/package/cloud-storage-picker)

---

Made with ❤️ by [Melvin Otieno](https://melvinotieno.com)
