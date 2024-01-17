# README.md - Connecting to Oracle Autonomous Database

This README provides a comprehensive guide for connecting to an Oracle Autonomous Database from Visual Studio Code using SQL\*Plus, along with additional instructions for granting quota on a tablespace and alternative installation steps for non-Homebrew users, such as those on Windows Subsystem for Linux (WSL).

## Table of Contents

- [Prerequisites](#prerequisites)
- [Installation and Setup](#installation-and-setup)
  - [Install SQL\*Plus using Homebrew](#1-install-sqlplus-using-homebrew)
  - [Install SQL\*Plus on WSL (Non-Homebrew)](#2-install-sqlplus-on-wsl-non-homebrew)
  - [Verify SQL\*Plus Installation](#3-verify-sqlplus-installation)
  - [Download and Unzip the Oracle Wallet](#4-download-and-unzip-the-oracle-wallet)
  - [Configure Wallet and Environment Variables](#5-configure-wallet-and-environment-variables)
    - [Update sqlnet.ora](#a-update-sqlnetora)
    - [Set TNS_ADMIN Environment Variable](#b-set-tns_admin-environment-variable)
  - [Connect Using SQL\*Plus](#6-connect-using-sqlplus)
- [Granting Quota on Tablespace](#granting-quota-on-tablespace)

## Prerequisites

Before proceeding with the connection setup, ensure you have:

- Visual Studio Code installed on your machine.
- Access credentials for an Oracle Autonomous Database.
- The client credentials (wallet) for the database, downloadable from the Oracle Cloud Console.

## Installation and Setup

### 1. Install SQL\*Plus using Homebrew

SQL\*Plus is an essential command-line interface tool for managing Oracle databases.

#### Commands:

```bash
brew tap InstantClientTap/instantclient
brew install instantclient-sqlplus
```

### 2. Install SQL\*Plus on WSL (Non-Homebrew)

For users on Windows Subsystem for Linux (WSL) or non-Homebrew environments, follow these steps:

#### Steps:

1. Visit the Oracle Instant Client download page.
2. Select the appropriate package for your Linux distribution.
3. Download and extract the files to a directory of your choice.
4. Add the directory containing the extracted files to your PATH environment variable.

#### Example Commands:

```bash
# Replace "instantclient_19_3" with your specific version
export PATH=/path/to/instantclient_19_3:$PATH
```

### 3. Verify SQL\*Plus Installation

To confirm successful installation:

#### Command:

```bash
sqlplus -version
```

### 4. Download and Unzip the Oracle Wallet

The Oracle Wallet contains critical configuration files for database connections.

#### Commands:

```bash
# Navigate to your desired directory
cd path/to/directory

# Unzip the wallet
unzip downloaded_wallet.zip -d Wallet_STVDB
```

### 5. Configure Wallet and Environment Variables

#### a. Update `sqlnet.ora`

Customize the `sqlnet.ora` file to specify the wallet's directory.

#### File Content (`sqlnet.ora`):

```plaintext
WALLET_LOCATION = (SOURCE = (METHOD = file) (METHOD_DATA = (DIRECTORY="/path/to/Wallet_STVDB")))
SSL_SERVER_DN_MATCH=yes
```

#### b. Set `TNS_ADMIN` Environment Variable

This variable should point to the wallet directory.

#### Command:

```bash
export TNS_ADMIN=/path/to/Wallet_STVDB
```

### 6. Connect Using SQL\*Plus

Establish a connection to your Oracle database.

#### Command:

```bash
sqlplus 'username/password@tns_entry'
```

**Note:** Replace `username`, `password`, and `tns_entry` with your actual database credentials and the TNS entry from the `tnsnames.ora` file in the wallet.

## Granting Quota on Tablespace

As an admin on a private network, you may need to grant yourself the necessary quota on the DATA tablespace.

### Command:

```sql
ALTER USER VEGA QUOTA UNLIMITED ON DATA;
```

This command grants the VEGA user unlimited space in the DATA tablespace. Execute this command in SQL\*Plus where you have administrative privileges.

---

Following these steps will set up a secure connection to your Oracle Autonomous Database using SQL\*Plus via Visual Studio Code and enable you to manage quotas on tablespaces.
