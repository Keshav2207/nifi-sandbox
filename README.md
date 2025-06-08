# Setup Apache Nifi

## Steps

1.  To use MySQL on Nifi: [Configuring Apache NiFi JDBC Connection Pool for MySQL](#configuring-apache-nifi-jdbc-connection-pool-for-mysql)

2. Update .env file in the root directory (update values as needed).

3. Start the containers: `docker compose up -d --build`


## Configuring Apache NiFi JDBC Connection Pool for MySQL

This guide explains how to configure a **DBCPConnectionPool** controller service in Apache NiFi to connect to a MySQL database running in a Docker container.

---

### 🔧 Configuration

Below is the configuration table for setting up the JDBC connection pool in NiFi:

| Property                      | Value                                                                                  |
|------------------------------|----------------------------------------------------------------------------------------|
| **Database Connection URL**  | `jdbc:mysql://mysql:3306/nifi_begins`                                                 |
| **Database Driver Class**    | `com.mysql.cj.jdbc.Driver`                                                            |
| **Driver Location(s)**       | `/opt/nifi/drivers/mysql-connector-j-9.3.0/mysql-connector-j-9.3.0.jar`               |
| **Database User**            | `root`                                                                                 |
| **Database Password**        | `mysql`                                                                                |

> **Note:** Adjust the `Database User` and `Password` fields based on your MySQL container setup.

### Step-by-step Installation (WSL/Linux/macOS)

```bash
# Create target directory
mkdir -p ~/drivers/mysql-connector-j-9.3.0
cd ~/drivers/mysql-connector-j-9.3.0

# Download the connector
wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-j-9.3.0.tar.gz

# Extract it
tar -xvzf mysql-connector-j-9.3.0.tar.gz
```

## Apache NiFi Backup & Restore Guide

### Real-Time Backup using Alpine & Rsync

A lightweight backup container copies NiFi data from volumes to a bind-mounted host directory every 24 hours.

### 🔁 Restoring from Backup

To restore NiFi volumes from `./nifi_backup`, use this command:

```bash
docker run --rm \
  -v "$(pwd)/nifi_backup/content_repository":/backup/content_repository:ro \
  -v "$(pwd)/nifi_backup/flowfile_repository":/backup/flowfile_repository:ro \
  -v "$(pwd)/nifi_backup/provenance_repository":/backup/provenance_repository:ro \
  -v "$(pwd)/nifi_backup/conf":/backup/conf:ro \
  -v nifi_content:/data/content_repository \
  -v nifi_flowfile:/data/flowfile_repository \
  -v nifi_provenance:/data/provenance_repository \
  -v nifi_conf:/data/conf \
  alpine sh -c "
    apk add --no-cache rsync &&
    echo 'Restoring NiFi backup...' &&
    rsync -av --delete /backup/content_repository/ /data/content_repository/ &&
    rsync -av --delete /backup/flowfile_repository/ /data/flowfile_repository/ &&
    rsync -av --delete /backup/provenance_repository/ /data/provenance_repository/ &&
    rsync -av --delete /backup/conf/ /data/conf/ &&
    echo 'Restore complete!'
  "
```

#### ⚠️ Notes

* Stop the NiFi container before restoring:

  ```bash
  docker stop nifi-${KEY}
  ```

* This command **overwrites** existing data in the volumes.

* Ensure `./nifi_backup` structure is correct:

  ```
  ./nifi_backup/
    ├── content_repository/
    ├── flowfile_repository/
    ├── provenance_repository/
    └── conf/
  ```
