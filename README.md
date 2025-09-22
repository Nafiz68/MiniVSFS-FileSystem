# MiniVSFS - Mini Very Simple File System

A lightweight, educational filesystem implementation written in C that demonstrates core filesystem concepts including inodes, directory structures, bitmaps, and data integrity through CRC checksums.

## Overview

MiniVSFS is a simple filesystem designed for educational purposes, implementing fundamental filesystem concepts in a straightforward manner. The project consists of two main utilities:

- **mkfs_builder**: Creates a new MiniVSFS image from scratch
- **mkfs_adder**: Adds files to an existing MiniVSFS image

## Features

- **Block-based storage** with 4096-byte blocks
- **Inode-based file system** with 128-byte inodes
- **Direct block addressing** (up to 12 direct blocks per file)
- **Directory entries** with 64-byte directory entries
- **Bitmap allocation** for both inodes and data blocks
- **Data integrity** through CRC32 checksums for all metadata
- **Root directory** with standard `.` and `..` entries

## File System Structure

```
┌─────────────────┐
│   Superblock    │ Block 0
├─────────────────┤
│  Inode Bitmap   │ Block 1
├─────────────────┤
│  Data Bitmap    │ Block 2
├─────────────────┤
│                 │
│  Inode Table    │ Blocks 3 to 3+N
│                 │
├─────────────────┤
│                 │
│   Data Region   │ Remaining blocks
│                 │
└─────────────────┘
```

## Building

Compile the utilities using GCC:

```bash
gcc -o mkfs_builder mkfs_builder.c
gcc -o mkfs_adder mkfs_adder.c
```

## Usage

### Creating a New Filesystem

```bash
./mkfs_builder --image filesystem.img --size-kib 1024 --inodes 256
```

**Parameters:**

- `--image`: Output image filename
- `--size-kib`: Total size in kilobytes (180-4096, must be multiple of 4)
- `--inodes`: Number of inodes (128-512)

### Adding Files to Filesystem

```bash
./mkfs_adder --input filesystem.img --output filesystem_new.img --file myfile.txt
```

**Parameters:**

- `--input`: Input filesystem image
- `--output`: Output filesystem image
- `--file`: File to add to the filesystem

## Technical Specifications

### Data Structures

#### Superblock (116 bytes)

Contains filesystem metadata including:

- Magic number (0x4D565346)
- Version, block size, total blocks
- Bitmap and inode table locations
- Data region layout
- CRC32 checksum

#### Inode (128 bytes)

File metadata including:

- File mode and permissions
- Size, timestamps (atime, mtime, ctime)
- Direct block pointers (12 blocks max)
- User/group IDs
- CRC32 checksum

#### Directory Entry (64 bytes)

- Inode number
- File type (file/directory)
- Filename (up to 57 characters)
- XOR checksum

### Limitations

- **File size**: Maximum 48KB per file (12 × 4KB blocks)
- **Directory entries**: Maximum entries per directory block
- **No subdirectories**: Only root directory supported
- **No symbolic links**: Regular files only
- **No extended attributes**: Basic metadata only

## Error Handling

The implementation includes comprehensive error checking for:

- Invalid filesystem images
- Insufficient space (inodes/data blocks)
- File size limitations
- Duplicate filenames
- I/O errors

## Example Usage

1. Create a 1MB filesystem with 256 inodes:

```bash
./mkfs_builder --image test.img --size-kib 1024 --inodes 256
```

2. Add a text file to the filesystem:

```bash
./mkfs_adder --input test.img --output test_with_file.img --file document.txt
```

## Project Structure

```
├── mkfs_builder.c    # Filesystem creation utility
├── mkfs_adder.c      # File addition utility
├── file_*.txt        # Sample test files
└── README.md         # This documentation
```

## Educational Context

This project was developed as part of CSE 321 (Operating Systems) coursework to demonstrate:

- Filesystem design principles
- Low-level file I/O operations
- Data structure layout and alignment
- Error handling in system programming
- Memory management in C

## License

This project is for educational purposes. Please refer to your institution's academic integrity policies when using this code.

## Contributing

This is an educational project. While not actively maintained, suggestions for improvements are welcome for learning purposes.
