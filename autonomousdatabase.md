# Connecting to Oracle Autonomous Database

This guide outlines the steps for setting up a connection to an Oracle Autonomous Database from Visual Studio Code, using SQL\*Plus as the command-line interface.

## Prerequisites

- Visual Studio Code installed.
- Access to an Oracle Autonomous Database.
- Downloaded client credentials (wallet) for the database.

## Installation and Setup

### 1. Install SQL\*Plus using Homebrew

SQL\*Plus is a command-line tool for running SQL commands against Oracle databases.

#### Commands:

```bash
brew tap InstantClientTap/instantclient
brew install instantclient-sqlplus
```

### 2. Verify SQL\*Plus Installation

Check if SQL\*Plus is installed correctly.

#### Command:

```bash
sqlplus -version
```

### 3. Download and Unzip the Oracle Wallet

Download the wallet from the Oracle Cloud Console and unzip it in a known directory.

#### Commands:

```bash
# Navigate to the desired directory
cd path/to/directory

# Unzip the wallet
unzip downloaded_wallet.zip -d Wallet_STVDB
```

### 4. Configure Wallet and Environment Variables

#### a. Update `sqlnet.ora`

Modify the `sqlnet.ora` file in the wallet directory to correctly reference the wallet location.

#### File Content (`sqlnet.ora`):

```plaintext
WALLET_LOCATION = (SOURCE = (METHOD = file) (METHOD_DATA = (DIRECTORY="/path/to/Wallet_STVDB")))
SSL_SERVER_DN_MATCH=yes
```

#### b. Set `TNS_ADMIN` Environment Variable

Set `TNS_ADMIN` to point to the wallet directory.

#### Command:

```bash
export TNS_ADMIN=/path/to/Wallet_STVDB
```

### 5. Connect Using SQL\*Plus

Connect to the database using SQL\*Plus with the database user credentials.

#### Command:

```bash
sqlplus 'username/password@tns_entry'
```

Replace `username`, `password`, and `tns_entry` with your database user credentials and the TNS entry from the `tnsnames.ora` file in the wallet.

## Using Visual Studio Code for Database Connection

### 1. Install Necessary Extensions in VS Code

Install "Oracle Developer Tools for VS Code" and "SQLTools - Database tools" with the "SQLTools Oracle Driver."

### 2. Configure SQLTools Connection

Set up a new connection in SQLTools with the database details.

#### Connection Details:

- **Connection Name**: Any meaningful name.
- **Connection Type**: OracleDB.
- **Username**: Your database username.
- **Password**: Your database password.
- **Database**: TNS entry from `tnsnames.ora`.
- **Oracle Wallet Location**: Path to the wallet directory.

### 3. Connect and Query the Database

Use SQLTools to connect to the database and execute SQL queries.

## Troubleshooting Steps

1. **Recheck File Paths**: Ensure the wallet path in `sqlnet.ora` and `TNS_ADMIN` environment variable are correct.
2. **Verify Network Accessibility**: Confirm that the network can reach Oracle Cloud services.
3. **Permissions Check**: Ensure that the wallet files have the correct permissions.

---

This README provides a comprehensive overview of the steps taken, including terminal commands, file configurations, and troubleshooting tips. It's tailored for a macOS environment, particularly with the use of Homebrew for installing SQL\*Plus. Make sure to adjust the paths and environment variables as per your specific setup.
