# Discord Cloud Storage Documentation

## Project Overview

Discord Cloud Storage is a Flask-based web application that utilizes Discord as a file storage backend. The system allows users to upload files through a web interface, which are then chunked (if necessary) and stored in a Discord channel as attachments. The application also supports downloading previously uploaded files by reassembling the chunks.

The project includes two versions:
1. **Standard Version** (discord_cloud_no_enc.py) - Basic functionality without encryption
2. **Secure Version** (secure_cloud.py) - Enhanced security with file encryption using Fernet

## System Architecture

### Components

1. **Web Interface** - Flask-based frontend for file upload/download
2. **Discord Bot** - Backend for storing and retrieving files from Discord channels
3. **File Processing Engine** - Handles chunking, metadata management, and file reassembly
4. **Encryption Module** (Secure version only) - Handles file encryption/decryption

### Data Flow

#### Upload Process:
1. User uploads a file via the web interface
2. System checks if the file exceeds Discord's file size limit (25MB)
3. If needed, the file is split into chunks
4. (Secure version) File is encrypted before storage
5. Metadata is created to track file chunks
6. The Discord bot uploads the metadata and file chunks to the specified channel

#### Download Process:
1. User selects a file and channel for download
2. System retrieves the metadata from Discord
3. Each chunk is downloaded and reassembled
4. (Secure version) The file is decrypted
5. The complete file is served to the user

## Setup and Configuration

### Requirements

- Python 3.6+
- Flask
- Discord.py
- Cryptography (for secure version)
- Discord bot token

### Environment Variables

- `bot_token`: Discord bot token for authentication
- `enc_key`: Encryption key (secure version only)

### Directory Structure

- `Data/`: Storage directory for temporary files
- `templates/`: Flask HTML templates (index.html, uploaded.html)

## Technical Details

### File Chunking

Files larger than 25MB (Discord's API limit) are split into smaller chunks:

```python
chunk_size = 25 * 1024 * 1024  # 25 MB
```

Each chunk is named using the pattern: `{filename}_part_{chunk_number}.{extension}`

### Metadata

For each uploaded file, a metadata JSON file is created to track:
- Original filename
- File size (for large files)
- Number of chunks
- List of chunk filenames
- (Secure version) Encryption key

### Security Features (Secure Version)

The secure version implements Fernet symmetric encryption:
- Files are encrypted before uploading to Discord
- Metadata is also encrypted
- The encryption key is stored in the metadata
- Files are automatically decrypted upon download

### Logging

The system uses colorlog for enhanced console output with different colors for:
- DEBUG (bold cyan)
- INFO (bold green)
- WARNING (bold yellow)
- ERROR (red)
- CRITICAL (bold red)

## API Reference

### Web Endpoints

#### GET /
- Returns the main page with upload/download interface

#### POST /upload
- Parameters:
  - `file`: The file to upload
  - `channel`: The Discord channel name for storage
- Returns: Confirmation page or error message

#### POST /download
- Parameters:
  - `channels`: The Discord channel name where the file is stored
  - `files`: The filename to download
- Returns: The downloaded file or error message

### Discord Bot Functions

#### on_ready()
- Triggered when the bot connects to Discord
- Logs the successful connection

#### upload_to_discord(file_path, filename, channel_name)
- Uploads a file to the specified Discord channel
- Handles chunking for large files
- Creates and uploads metadata

#### download_files(channel_name, filename)
- Downloads file chunks from Discord
- Reassembles the original file
- (Secure version) Decrypts the file

## Error Handling

The system includes error handling for common scenarios:
- Channel not found
- File not found
- Metadata not found
- Chunking/reassembly failures
- (Secure version) Encryption/decryption failures

## Limitations and Considerations

1. **Discord Rate Limits**: Be aware of Discord's API rate limits when uploading/downloading large files or numerous chunks.

2. **File Size**: While the system can handle files larger than 25MB through chunking, there are practical limits to file size due to Discord's message history limitations.

3. **Security** (Standard version): The non-encrypted version does not provide security for sensitive files. Use the secure version for confidential data.

4. **Key Management** (Secure version): The encryption key is stored in the metadata file. While this metadata is itself encrypted, a more robust key management system might be desirable for highly sensitive applications.

5. **Channel History Limit**: Discord has limits on message history, which may affect retrieval of older files.

## Future Enhancements

Potential improvements for the system:
- User authentication
- File sharing capabilities 
- Improved error recovery
- Progress indicators for large file transfers
- Enhanced key management
- Compression before chunking
