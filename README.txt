**UniversalSerialBoss User Manual

This manual provides detailed instructions on how to install, configure, and use the UniversalSerialBoss application.
1. Installation

The install command is a one-time process that prepares your system by setting up the necessary directories and the MySQL database.
Prerequisites

    MySQL Server: A running MySQL or MariaDB server is required.

    Bash: The script is written in Bash and requires a compatible shell.

    Standard Utilities: The script relies on standard Linux utilities like jq, mysql, inotifywait, and stat.

Installation Steps

    Move the script: Copy the usb_boss.sh script to a system path (e.g., /usr/local/bin) and make it executable.

    sudo cp usb_boss.sh /usr/local/bin/usb_boss
    sudo chmod +x /usr/local/bin/usb_boss

    Run the installer: Execute the install command. It will prompt you for the MySQL root password to create a secure usb_boss database and user.

    sudo usb_boss.sh install

    Upon completion, the application will have:

        Created /etc/usb_boss for configuration files.

        Created /var/log/usb_boss for system logs.

        Set up a usb_boss database and a dedicated user.

        Initialized all necessary database tables and views.

2. Core Commands
sync <profile_name>

This command performs a single, one-time synchronization for a specified profile. The profiles are defined in the usb_boss_sync_profiles table in the database.

Usage:

sudo usb_boss.sh sync Pictures

Functionality:

    Scans both the source and target directories.

    Compares files based on hash, size, and modification time.

    Performs copy, update, or deletion operations to make the directories consistent.

    Handles conflicts based on the configured conflict_resolution rule (e.g., newer_wins).

sync_all

Runs a sync operation on all currently enabled sync profiles.

Usage:

sudo usb_boss.sh sync_all

health

Displays a high-level overview of the service's health, including the daemon status, database connection, and log file size.

Usage:

sudo usb_boss.sh health

3. Daemon Management

The daemon is a persistent background process that enables real-time monitoring and safety features.
daemon start

Starts the daemon. This process runs in the background and continuously monitors for real-time sync events and drive removal.

Usage:

sudo usb_boss.sh daemon start

daemon stop

Gracefully stops the running daemon.

Usage:

sudo usb_boss.sh daemon stop

daemon status

Checks if the daemon is currently running and provides its process ID (PID) if it is.

Usage:

sudo usb_boss.sh daemon status

4. Configuration and Features
File Synchronization

Synchronization profiles are stored in the usb_boss_sync_profiles database table. Each profile defines the source_base_path and target_base_path, along with the sync_mode (bidirectional, source-to-target, etc.) and conflict_resolution method.
Real-time Sync

To enable real-time synchronization for a specific USB drive, you must add its device ID to the usb_boss_daemon_profiles table and set realtime_sync_enabled to TRUE. The daemon will then monitor the directories linked to this device ID for changes.
Removal Protection

The removal protection feature uses the daemon to watch for the sudden disappearance of a device. To enable it for a specific USB drive, add its device ID to the usb_boss_daemon_profiles table and set shutdown_on_removal_enabled to TRUE. You can also specify the removal_action (shutdown or unmount_only).

Important Note: The daemon relies on the device ID, which you can typically find in /dev/disk/by-id/.
