# Docker Hub README MCP Server

[![npm version](https://img.shields.io/npm/v/docker-hub-readme-mcp-server)](https://www.npmjs.com/package/docker-hub-readme-mcp-server)
[![npm downloads](https://img.shields.io/npm/dm/docker-hub-readme-mcp-server)](https://www.npmjs.com/package/docker-hub-readme-mcp-server)
[![GitHub stars](https://img.shields.io/github/stars/naoto24kawa/package-readme-mcp-servers)](https://github.com/naoto24kawa/package-readme-mcp-servers)
[![GitHub issues](https://img.shields.io/github/issues/naoto24kawa/package-readme-mcp-servers)](https://github.com/naoto24kawa/package-readme-mcp-servers/issues)
[![license](https://img.shields.io/npm/l/docker-hub-readme-mcp-server)](https://github.com/naoto24kawa/package-readme-mcp-servers/blob/main/LICENSE)

A Model Context Protocol (MCP) server that provides tools to fetch Docker image information, README content, and usage examples from Docker Hub. This server enables seamless integration with Docker Hub to retrieve detailed information about container images.

## Features

- 🐳 **Get Docker Image README**: Fetch comprehensive README content and usage examples from Docker Hub
- 📊 **Get Docker Image Info**: Retrieve detailed information, available tags, and download statistics for Docker images
- 🔍 **Search Docker Images**: Search for Docker images on Docker Hub with advanced filtering options
- ⚡ **Intelligent Caching**: Built-in LRU memory caching with configurable TTL for optimal performance
- 🛡️ **Robust Error Handling**: Comprehensive error handling with exponential backoff retry logic
- 🔄 **GitHub Fallback**: Automatically attempts to fetch README from linked GitHub repositories when Docker Hub content is unavailable
- 📈 **Rate Limit Handling**: Smart handling of Docker Hub API rate limits

## Installation

### From npm

```bash
npm install docker-hub-readme-mcp-server
```

### From source

```bash
git clone https://github.com/your-username/package-readme-mcp-servers.git
cd package-readme-mcp-servers/docker-hub-readme-mcp-server
npm install
npm run build
```

## Usage

### MCP Client Configuration

Add the server to your MCP client configuration:

#### Claude Desktop

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "docker-hub-readme": {
      "command": "npx",
      "args": ["docker-hub-readme-mcp-server"],
      "env": {
        "GITHUB_TOKEN": "your-github-token-here"
      }
    }
  }
}
```

#### Other MCP Clients

```json
{
  "name": "docker-hub-readme",
  "command": "npx",
  "args": ["docker-hub-readme-mcp-server"],
  "env": {
    "GITHUB_TOKEN": "your-github-token-here",
    "LOG_LEVEL": "info"
  }
}
```

### Available Tools

#### 1. get_package_readme

Fetches README content and usage examples for a Docker image.

**Parameters:**
- `package_name` (required): Docker image name (e.g., "nginx", "library/nginx", "microsoft/dotnet")
- `tag` (optional): Image tag (default: "latest")
- `include_examples` (optional): Whether to include usage examples (default: true)

**Example:**
```json
{
  "tool": "get_package_readme",
  "arguments": {
    "package_name": "nginx",
    "tag": "alpine"
  }
}
```

#### 2. get_package_info

Retrieves basic information and metadata for a Docker image.

**Parameters:**
- `package_name` (required): Docker image name
- `include_tags` (optional): Whether to include available tags (default: true)
- `include_stats` (optional): Whether to include download statistics (default: true)

**Example:**
```json
{
  "tool": "get_package_info",
  "arguments": {
    "package_name": "postgres",
    "include_tags": true
  }
}
```

#### 3. search_packages

Searches for Docker images on Docker Hub.

**Parameters:**
- `query` (required): Search query
- `limit` (optional): Maximum number of results (default: 20, max: 100)
- `is_official` (optional): Filter for official images only
- `is_automated` (optional): Filter for automated builds only

**Example:**
```json
{
  "tool": "search_packages",
  "arguments": {
    "query": "web server",
    "limit": 10,
    "is_official": true
  }
}
```

## Docker Image Name Formats

The server supports various Docker image name formats:

- **Official images**: `nginx`, `postgres`, `ubuntu`
- **User/organization images**: `microsoft/dotnet`, `bitnami/nginx`
- **Fully qualified**: `namespace/name:tag`

## Response Format

### get_package_readme Response

```json
{
  "package_name": "nginx",
  "namespace": "library",
  "full_name": "library/nginx",
  "tag": "latest",
  "description": "Official build of Nginx.",
  "readme_content": "# Nginx Docker Image\n...",
  "usage_examples": [
    {
      "title": "Run Container",
      "code": "docker run -d -p 80:80 nginx",
      "language": "bash"
    }
  ],
  "installation": {
    "pull": "docker pull nginx:latest",
    "run": "docker run nginx:latest"
  },
  "basic_info": {
    "name": "nginx",
    "version": "latest",
    "description": "Official build of Nginx.",
    "namespace": "library",
    "full_name": "library/nginx",
    "author": "library",
    "keywords": ["web", "server"],
    "architecture": ["amd64", "arm64"],
    "os": ["linux"]
  },
  "stats": {
    "pull_count": 1000000000,
    "star_count": 15000,
    "last_updated": "2024-01-15T10:30:00Z"
  }
}
```

### get_package_info Response

```json
{
  "package_name": "postgres",
  "namespace": "library",
  "full_name": "library/postgres",
  "latest_tag": "latest",
  "description": "The PostgreSQL object-relational database system",
  "author": "library",
  "keywords": ["database", "sql"],
  "available_tags": ["latest", "16", "15", "14", "13"],
  "stats": {
    "pull_count": 500000000,
    "star_count": 8000,
    "last_updated": "2024-01-10T08:45:00Z"
  },
  "last_updated": "2024-01-10T08:45:00Z"
}
```

### search_packages Response

```json
{
  "query": "web server",
  "total": 1250,
  "packages": [
    {
      "name": "nginx",
      "namespace": "library",
      "full_name": "library/nginx",
      "description": "Official build of Nginx.",
      "star_count": 15000,
      "pull_count": 1000000000,
      "repo_type": "official",
      "is_official": true,
      "is_automated": false,
      "last_updated": "2024-01-15T10:30:00Z"
    }
  ]
}
```

## Development

### Requirements

- Node.js 18+
- TypeScript 5+

### Setup

```bash
# Clone the repository
git clone <repository-url>
cd docker-hub-readme-mcp-server

# Install dependencies
npm install

# Build the project
npm run build

# Run in development mode
npm run dev
```

### Scripts

- `npm run build`: Compile TypeScript to JavaScript
- `npm run dev`: Run in development mode with hot reload
- `npm start`: Start the production server
- `npm test`: Run tests
- `npm run lint`: Lint the code
- `npm run typecheck`: Check TypeScript types

## Environment Variables

- `GITHUB_TOKEN`: GitHub personal access token for enhanced README fetching (optional)
- `LOG_LEVEL`: Logging level (error, warn, info, debug) - default: warn
- `CACHE_TTL`: Cache time-to-live in milliseconds - default: 3600000 (1 hour)
- `CACHE_MAX_SIZE`: Maximum cache size in bytes - default: 104857600 (100MB)

## Caching

The server implements intelligent caching to improve performance:

- **Memory Cache**: LRU cache with configurable TTL and size limits
- **Cache Keys**: Hierarchical cache keys for different data types
- **Automatic Cleanup**: Periodic cleanup of expired cache entries

## Error Handling

The server includes comprehensive error handling:

- **Validation**: Input parameter validation
- **Retry Logic**: Automatic retry with exponential backoff
- **Rate Limiting**: Handles Docker Hub API rate limits
- **Fallback**: GitHub API fallback for README content

## API Limitations

- **Rate Limits**: Docker Hub API has rate limits for anonymous requests (100 requests per hour)
- **Metadata Availability**: Some metadata (like repository links) may not be available through Docker Hub API
- **Official Images**: Official images use the `library` namespace internally
- **Private Repositories**: This server only supports public Docker images
- **Image Layers**: Detailed layer information is not provided by the Docker Hub API

## Contributing

We welcome contributions! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes
4. Add tests if applicable (`npm test`)
5. Ensure code quality (`npm run lint && npm run typecheck`)
6. Commit your changes (`git commit -m 'Add amazing feature'`)
7. Push to the branch (`git push origin feature/amazing-feature`)
8. Submit a pull request

### Development Guidelines

- Follow TypeScript best practices
- Add comprehensive error handling
- Include unit tests for new features
- Update documentation as needed
- Follow the existing code style

## License

MIT License - see LICENSE file for details.

## Related Projects

- [npm-package-readme-mcp-server](../npm-package-readme-mcp-server) - MCP server for npm packages
- [pip-package-readme-mcp-server](../pip-package-readme-mcp-server) - MCP server for PyPI packages
- [gem-package-readme-mcp-server](../gem-package-readme-mcp-server) - MCP server for RubyGems