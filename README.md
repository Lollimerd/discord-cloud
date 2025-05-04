# Discord Storage System

A cloud storage solution that leverages Discord channels as a storage backend, allowing users to upload, store, and download files through a simple web interface.

## Features

- **Web-based Interface**: Simple Flask application for file upload and download
- **Discord Storage Backend**: Uses Discord channels to store files
- **Chunking System**: Automatically splits large files into smaller chunks to comply with Discord's file size limitations
- **Encryption Option**: Secure version with file encryption using Fernet symmetric encryption
- **Logging System**: Comprehensive colored logging for easy debugging
- **Asynchronous Processing**: Threaded operations to handle Discord API interactions

## File Structure

```
Root Directory
│
├── discord cloud no enc.py  # Main script for the unencrypted file storage solution
├── secure cloud.py          # Main script for the encrypted file storage solution
├── dis_commands.py          # Discord bot commands module
│
├── Data/                    # Directory for storing temporary files
│   └── (Created dynamically during execution)
│
└── templates/               # Flask templates
    ├── index.html           # Main page template
    └── uploaded.html        # Upload confirmation template
```

## Prerequisites

- Python 3.7+
- Discord bot token with proper permissions
- The following Python packages:
  - Flask
  - discord.py
  - cryptography (for the secure version)
  - colorlog

## Installation

1. Clone the repository
2. Install the dependencies:
```bash
  pip install flask discord.py cryptography colorlog
```
3. Create a Discord bot and obtain a token
4. Set up environment variables:
```bash
# For the unencrypted version
export bot_token="YOUR_DISCORD_BOT_TOKEN"

# For the encrypted version
export bot_token="YOUR_DISCORD_BOT_TOKEN"
export enc_key="YOUR_ENCRYPTION_KEY"  # Or generate it within the app
```
5. Create the necessary templates directory and HTML files

## How It Works

### Unencrypted Version (discord cloud no enc.py)

This script provides basic file storage functionality:

```
Core functionality flow:
1. User uploads a file through the web interface
2. File is saved temporarily to the Data directory
3. File size is checked:
- If smaller than 25MB, it's uploaded directly
- If larger than 25MB, it's split into chunks
4. Metadata JSON file is created (containing filename, chunks info)
5. Metadata and file/chunks are uploaded to the specified Discord channel
6. Temporary files are deleted after successful upload
```

The download process works in reverse:

```
# Download process:
1. User requests a file by name and channel
2. System searches for the metadata file in the channel
3. Using the metadata, it finds all associated chunks
4. Chunks are downloaded and reassembled in the correct order
5. The complete file is saved to the Data directory
6. File is served to the user for download
```

### Secure Version (secure cloud.py)

The secure version adds encryption:

```
Security enhancements:
1. Files are encrypted with Fernet symmetric encryption before storage
2. The encryption key is stored in the metadata
3. Metadata itself is encrypted before upload
4. On download, metadata is decrypted to obtain the file structure and key
5. File chunks are downloaded and reassembled
6. The complete file is decrypted before being served to the user
```

### Discord Bot Commands (dis_commands.py)

The bot provides utility commands for managing the storage:

```
Discord slash commands:
/channel_info <channel_id> - Get detailed information about a channel
/get_members - List all members in the current guild
/check_attachments - List all attachments in the current channel
/ping - Simple connectivity test
```

## Usage Example

1. Start the application:
```bash
# For unencrypted version
python "discord cloud no enc.py"

# For encrypted version
python "secure cloud.py"
```

2. Open a web browser and navigate to `http://localhost:5000`

3. To upload a file:
   - Select a file
   - Enter the name of a Discord channel
   - Click Upload

4. To download a file:
   - Enter the channel name where the file is stored
   - Enter the exact filename
   - Click Download

## Web Interface

![Discord Storage System Interface](https://placeholder-for-interface-screenshot.png)

## Console Output

### Unencrypted Version

When running the unencrypted version, you'll see log output like this:

```
█ Logged in as DiscordBot#1234
█ File example.pdf has been saved to Data/example.pdf
█ Channel general found

█ Uploading file: Data/example.pdf
█ File size: 2.34 MB
█ File does not need chunking, preparing uploading...
█ Creating metadata for single chunk
█ Sent metadata example_pdf_metadata.json and deleted in Data/example.pdf
█ File has been sent and deleted from Data/example.pdf

# When downloading:
█ Obtaining general & example.pdf...
█ Channel general found
█ Retrieving contents from example_pdf_metadata.json
█ Checking attachment: example_pdf_metadata.json
█ Creating supporting documents
█ Checking attachments...
█ Chunk valid
█ Writing chunk 1
█ Assembling chunks...
█ File has been reassembled and saved to uploads directory
```

### Encrypted Version

The encrypted version provides more detailed security-related information:

```
█ Logged in as DiscordBot#1234
█ File example.pdf has been saved to Data/example.pdf
█ Channel general found
█ Encrypting data
█ Reading file
█ Encrypting data
█ Written back <_io.BufferedWriter name='Data/example.pdf'>
█ Initiating send sequence

█ Uploading file: Data/example.pdf
█ File size: 2.34 MB
█ File does not need chunking, preparing uploading...
█ Sent metadata example_pdf_metadata.json and deleted in Data/example.pdf
█ File has been sent and deleted from Data/example.pdf
█ Done

# When downloading:
█ Found channel: general
█ Retrieving contents from example_pdf_metadata.json
█ Check for metadata: example_pdf_metadata.json
█ Metadata example_pdf_metadata.json found
█ Reading encrypted metadata...
█ Metadata {metadata_content} loaded
█ Creating supporting documents
█ Checking attachments: example_pdf
█ 1 chunk valid
█ Chunk valid
█ File has been reassembled and saved to uploads directory
```

The console output uses enhanced formatting for better visibility, with each log entry preceded by a colored block character █ that would be appropriately colored in the actual terminal (green for INFO, yellow for WARNING, red for ERROR).

## Security Considerations

- The encryption key is stored in the environment variables for the secure version
- While the files are encrypted, the Discord channel must be kept private
- Admin access to the Discord server should be carefully controlled

## Limitations

- Maximum file size depends on your Discord server's limits
- Upload and download speeds are limited by Discord's API
- The Flask server runs locally by default without authentication

## License

This project is licensed under the MIT License - see the LICENSE file for details.
