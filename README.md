# Discord Notifier

A GitHub Action to send messages and rich embeds to Discord via webhooks with support for multiple embeds, custom formatting, and comprehensive validation.

## Features

- Send simple text messages or rich embeds to Discord
- Support for up to 10 embeds per message
- Custom username and avatar for webhook messages
- Embed customization: titles, descriptions, colors, images, thumbnails, fields, footers, and timestamps
- Array or single value inputs for flexible embed configuration
- Comprehensive input validation with detailed error messages
- Markdown formatting support in content and descriptions

## Usage

### Basic Message

```yaml
- uses: chase-roohms/discord-notify@v1
  with:
    webhook_url: ${{ secrets.DISCORD_WEBHOOK_URL }}
    content: 'Build completed successfully'
```

### Message with Custom Sender

```yaml
- uses: chase-roohms/discord-notify@v1
  with:
    webhook_url: ${{ secrets.DISCORD_WEBHOOK_URL }}
    content: 'Deployment finished'
    username: 'GitHub Actions Bot'
    avatar_url: 'https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png'
```

### Single Embed

```yaml
- uses: chase-roohms/discord-notify@v1
  with:
    webhook_url: ${{ secrets.DISCORD_WEBHOOK_URL }}
    embed-titles: 'Build Status'
    embed-descriptions: 'All tests passed successfully'
    embed-colors: '00FF00'
    embed-footers: 'GitHub Actions'
    embed-timestamps: 'now'
```

### Embed with Author and Images

```yaml
- uses: chase-roohms/discord-notify@v1
  with:
    webhook_url: ${{ secrets.DISCORD_WEBHOOK_URL }}
    embed-authors: 'GitHub Actions'
    embed-author-urls: 'https://github.com/features/actions'
    embed-author-icons: 'https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png'
    embed-titles: 'Deployment Status'
    embed-descriptions: 'Application deployed successfully'
    embed-colors: '0099ff'
    embed-thumbnail-urls: 'https://example.com/thumbnail.png'
    embed-image-urls: 'https://example.com/image.png'
```

### Embed with Fields

```yaml
- uses: chase-roohms/discord-notify@v1
  with:
    webhook_url: ${{ secrets.DISCORD_WEBHOOK_URL }}
    embed-titles: 'Workflow Information'
    embed-descriptions: 'Details about this workflow run'
    embed-colors: '9B59B6'
    embed-fields: |
      [
        {"name": "Repository", "value": "${{ github.repository }}", "inline": true},
        {"name": "Branch", "value": "${{ github.ref_name }}", "inline": true},
        {"name": "Triggered by", "value": "${{ github.actor }}", "inline": true}
      ]
    embed-timestamps: 'now'
```

### Multiple Embeds

```yaml
- uses: chase-roohms/discord-notify@v1
  with:
    webhook_url: ${{ secrets.DISCORD_WEBHOOK_URL }}
    content: 'Deployment Report'
    embed-titles: '["Build Summary", "Test Results", "Deployment Info"]'
    embed-descriptions: |
      [
        "All build steps completed successfully",
        "All unit tests and integration tests passed",
        "Application deployed to production"
      ]
    embed-colors: '["2ECC71", "3498DB", "9B59B6"]'
    embed-footers: '["Build completed", "Tests completed", "Deployment completed"]'
    embed-timestamps: 'now'
```

### Multiple Embeds with Individual Fields

```yaml
- uses: chase-roohms/discord-notify@v1
  with:
    webhook_url: ${{ secrets.DISCORD_WEBHOOK_URL }}
    embed-titles: '["Build", "Test", "Deploy"]'
    embed-colors: '["2ECC71", "3498DB", "9B59B6"]'
    embed-fields: |
      [
        [
          {"name": "Duration", "value": "2m 34s", "inline": true},
          {"name": "Status", "value": "Success", "inline": true}
        ],
        [
          {"name": "Tests Run", "value": "142", "inline": true},
          {"name": "Coverage", "value": "94.2%", "inline": true}
        ],
        [
          {"name": "Environment", "value": "Production", "inline": true},
          {"name": "Version", "value": "v1.2.3", "inline": true}
        ]
      ]
```

### Mixed Single and Array Values

```yaml
- uses: chase-roohms/discord-notify@v1
  with:
    webhook_url: ${{ secrets.DISCORD_WEBHOOK_URL }}
    embed-titles: '["Embed 1", "Embed 2"]'
    embed-descriptions: 'This description is shared by both embeds'
    embed-colors: '["FFD700", "C0C0C0"]'
    embed-footers: 'Common footer'
    embed-timestamps: 'now'
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| webhook_url | Discord webhook URL | Yes | - |
| content | Message content (text) | No | "" |
| username | Custom username for the webhook | No | "" |
| avatar_url | Custom avatar URL for the webhook | No | "" |
| embed-authors | Embed author names | No | "" |
| embed-author-urls | Embed author URLs | No | "" |
| embed-author-icons | Embed author icon URLs | No | "" |
| embed-titles | Embed titles | No | "" |
| embed-descriptions | Embed descriptions | No | "" |
| embed-urls | Embed URLs (clickable titles) | No | "" |
| embed-colors | Embed colors (hex values) | No | "" |
| embed-image-urls | Embed main image URLs | No | "" |
| embed-thumbnail-urls | Embed thumbnail URLs | No | "" |
| embed-fields | Embed fields as JSON arrays | No | "" |
| embed-footers | Embed footer text | No | "" |
| embed-footer-icons | Embed footer icon URLs | No | "" |
| embed-timestamps | Embed timestamps (ISO 8601 or "now") | No | "" |

### Embed Input Format

All embed inputs accept either:
- A single value (applied to all embeds if creating multiple)
- A JSON array of values (one per embed)

When using JSON arrays, all arrays must have the same length.

### Color Formats

Colors can be specified in multiple formats:
- Hex without prefix: `FF0000`
- Hex with # prefix: `#FF0000`
- Hex with 0x prefix: `0xFF0000`

### Timestamp Formats

Timestamps accept:
- `now` - Current time
- ISO 8601 format - `2026-01-14T12:00:00Z`

### Fields Format

Fields must be valid JSON arrays of objects with `name` and `value` properties:

Single embed with multiple fields:
```json
[
  {"name": "Field 1", "value": "Value 1", "inline": true},
  {"name": "Field 2", "value": "Value 2", "inline": false}
]
```

Multiple embeds with different fields:
```json
[
  [{"name": "Field A", "value": "Value A"}],
  [{"name": "Field B", "value": "Value B"}]
]
```

## Outputs

| Output | Description | Example |
|--------|-------------|---------|
| status_code | HTTP status code of the Discord request | 200 |
| payload | Full JSON payload sent to Discord | {...} |
| payload_file | Path to the payload file | /tmp/discord_payload.json |

## Requirements

- A valid Discord webhook URL
- Either `content` or at least one embed field must be provided
- Maximum of 10 embeds per message
- All JSON array inputs must have the same length

## Validation

The action performs comprehensive validation:

- Webhook URL format verification
- URL format validation for avatar, images, and links
- Hex color format validation
- Timestamp format validation
- JSON structure validation for fields
- Array length consistency checks
- Embed count limits (maximum 10)

Validation errors will fail the workflow with detailed error messages.

## Example Workflows

### Build Status Notification

```yaml
name: Build Notification
on:
  push:
    branches: [main]

jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Build
        run: npm run build
      
      - name: Notify Discord
        if: always()
        uses: chase-roohms/discord-notify@v1
        with:
          webhook_url: ${{ secrets.DISCORD_WEBHOOK_URL }}
          content: 'Build ${{ job.status }}'
          embed-titles: 'Build Report'
          embed-descriptions: 'Build for ${{ github.repository }}'
          embed-colors: ${{ job.status == 'success' && '00FF00' || 'FF0000' }}
          embed-fields: |
            [
              {"name": "Commit", "value": "${{ github.sha }}", "inline": false},
              {"name": "Author", "value": "${{ github.actor }}", "inline": true},
              {"name": "Branch", "value": "${{ github.ref_name }}", "inline": true}
            ]
          embed-timestamps: 'now'
```

### Multi-Stage Deployment Report

```yaml
name: Deploy
on:
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Build
        run: npm run build
      
      - name: Test
        run: npm test
      
      - name: Deploy
        run: npm run deploy
      
      - name: Send Deployment Report
        uses: chase-roohms/discord-notify@v1
        with:
          webhook_url: ${{ secrets.DISCORD_WEBHOOK_URL }}
          username: 'CI/CD Pipeline'
          content: 'Deployment Complete'
          embed-titles: '["Build", "Test", "Deploy"]'
          embed-descriptions: |
            [
              "Build completed successfully",
              "All tests passed",
              "Deployed to production"
            ]
          embed-colors: '["2ECC71", "3498DB", "9B59B6"]'
          embed-timestamps: 'now'
```

### PR Status with Multiple Embeds

```yaml
name: PR Checks
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  checks:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Run Checks
        run: |
          npm run lint
          npm test
      
      - name: Notify PR Status
        uses: chase-roohms/discord-notify@v1
        with:
          webhook_url: ${{ secrets.DISCORD_WEBHOOK_URL }}
          embed-titles: 'Pull Request #${{ github.event.pull_request.number }}'
          embed-descriptions: '${{ github.event.pull_request.title }}'
          embed-urls: '${{ github.event.pull_request.html_url }}'
          embed-colors: '5865F2'
          embed-fields: |
            [
              {"name": "Author", "value": "${{ github.event.pull_request.user.login }}", "inline": true},
              {"name": "Base", "value": "${{ github.event.pull_request.base.ref }}", "inline": true},
              {"name": "Head", "value": "${{ github.event.pull_request.head.ref }}", "inline": true}
            ]
          embed-timestamps: 'now'
```

## How It Works

1. Input Validation: Validates all inputs including webhook URL format, color formats, URL formats, and JSON structures
2. Payload Building: Constructs the Discord webhook payload with proper JSON formatting
3. Message Sending: Sends the payload to Discord via HTTP POST request
4. Response Handling: Captures and reports the HTTP status code and any errors

## License

This action is provided as-is under the MIT License.

## Contributing

Contributions are welcome. Please open an issue or pull request for bug fixes, improvements, or new features.
