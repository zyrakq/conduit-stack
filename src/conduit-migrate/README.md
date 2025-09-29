# Conduit Database Migration Tool

This directory contains tools and instructions for migrating Conduit Matrix homeserver data between different database backends.

## Overview

The `conduit_migrate` tool allows you to migrate your Conduit database from one backend to another (e.g., from RocksDB to SQLite). This is particularly useful when you want to change your database backend without losing your existing Matrix data.

## Prerequisites

### Using DevContainer (Recommended)

The easiest way to use the migration tool is through the provided DevContainer, which includes all necessary dependencies:

- **Ubuntu base image** with pre-installed development tools
- **Rust toolchain** for building and running the migration tool
- **Docker support** for container operations
- **Required system packages**: git, clang, llvm-dev, libclang-dev, build-essential, pkg-config, libssl-dev, ca-certificates

The DevContainer automatically installs `conduit_migrate` on startup, making it the preferred option, especially if your main OS is not Ubuntu.

### Manual Installation

If you prefer to install manually on your system, you'll need:

1. **Rust toolchain** (install via [rustup](https://rustup.rs/))
2. **System dependencies**:

   ```bash
   # Ubuntu/Debian
   sudo apt install git clang llvm-dev libclang-dev build-essential pkg-config libssl-dev ca-certificates
   
   # Other distributions - install equivalent packages
   ```

3. **Set environment variables**:

   ```bash
   export LIBCLANG_PATH="/usr/lib/x86_64-linux-gnu"
   export BINDGEN_EXTRA_CLANG_ARGS="-I/usr/include"
   ```

4. **Install conduit_migrate**:

   ```bash
   cargo install --locked --git https://github.com/shadowjonathan/conduit_toolbox conduit_migrate
   ```

## Migration Process

### Step 1: Backup Your Data

First, create a backup of your existing Conduit database:

```bash
# Create a temporary container with your Conduit data volume
docker run --rm -d \
  --name temp \
  -v conduit-db:/var/lib/matrix-conduit \
  alpine sleep 10000

# Backup the RocksDB database (excluding media files)
docker exec temp tar --exclude='matrix-conduit/media' -czf /tmp/rocksdb.tgz -C /var/lib matrix-conduit

# Copy the backup to your local machine
docker cp temp:/tmp/rocksdb.tgz ./

# Backup media files separately (optional but recommended)
docker exec temp sh -c 'mkdir -p /tmp/media_backup && cp -r /var/lib/matrix-conduit/media /tmp/media_backup/ 2>/dev/null || true'
```

### Step 2: Prepare for Migration

Clear the existing database and prepare for the new backend:

```bash
# Remove existing database files (keep media backup)
docker exec temp rm -rf /var/lib/matrix-conduit/*

# Restore media files
docker exec temp cp -r /tmp/media_backup/media /var/lib/matrix-conduit/
```

### Step 3: Perform the Migration

Extract your backup and run the migration tool:

```bash
# Extract the backup
tar -xzf rocksdb.tgz

# Run the migration (example: RocksDB to SQLite)
conduit_migrate -s ./volumes/rocksdb -f rocks -d ./volumes/sqlite/ -t sqlite
```

### Step 4: Deploy the Migrated Database

Copy the migrated database to your Conduit container:

```bash
# Copy the new database file
docker cp conduit.db temp:/var/lib/matrix-conduit/

# Clean up the temporary container
docker stop temp
```

## Migration Options

The `conduit_migrate` tool supports various source and target database formats:

- **Source formats** (`-f` flag): `rocks` (RocksDB), `sqlite` (SQLite)
- **Target formats** (`-t` flag): `rocks` (RocksDB), `sqlite` (SQLite)
- **Source path** (`-s` flag): Path to the source database directory/file
- **Destination path** (`-d` flag): Path to the destination directory

### Common Migration Scenarios

```bash
# RocksDB to SQLite
conduit_migrate -s /path/to/rocksdb -f rocks -d /path/to/output -t sqlite

# SQLite to RocksDB
conduit_migrate -s /path/to/database.db -f sqlite -d /path/to/output -t rocks

# RocksDB to RocksDB (useful for compaction/reorganization)
conduit_migrate -s /path/to/source/rocksdb -f rocks -d /path/to/output -t rocks
```

## Important Notes

- **Always backup your data** before performing any migration
- **Test the migration process** in a non-production environment first
- **Ensure sufficient disk space** for both source and target databases during migration
- **Media files are not migrated** - they remain in place and should be backed up separately
- **Service downtime** is required during the migration process

## Troubleshooting

### Common Issues

1. **Missing dependencies**: Ensure all required system packages are installed
2. **Permission errors**: Check file permissions and Docker volume access
3. **Memory issues**: Large databases may require additional system memory
4. **Corrupted migration**: Always verify the migrated data before deploying

### Getting Help

- Check the [conduit_toolbox repository](https://github.com/shadowjonathan/conduit_toolbox) for updates and issues
- Ensure you're using the latest version of `conduit_migrate`
- Review Conduit documentation for database-specific requirements

## Environment Variables

The DevContainer sets up the following environment variables for proper compilation:

- `LIBCLANG_PATH=/usr/lib/x86_64-linux-gnu`
- `BINDGEN_EXTRA_CLANG_ARGS=-I/usr/include`

These are automatically configured when using the DevContainer.
