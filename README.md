# Configuring Apache NiFi JDBC Connection Pool for MySQL

This guide explains how to configure a **DBCPConnectionPool** controller service in Apache NiFi to connect to a MySQL database running in a Docker container.

---

## 🔧 Configuration

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

# Copy the JAR file to current directory for easier mounting
cp mysql-connector-j-9.3.0/mysql-connector-j-9.3.0.jar .