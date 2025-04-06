# PostgreSQL 17 Cluster with Read Replica

![Docker](https://img.shields.io/badge/Docker-27.2.0-orange)
![Docker Compose](https://img.shields.io/badge/Docker%20Compose-v1.29.2--desktop.2-orange)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-17-green)

## Overview

This Docker Compose setup creates a PostgreSQL 17 cluster with a primary (read-write) instance and a read replica, providing high availability and read scalability for your database needs.

![Docker-compose Stack](docs/docker-compose-stack.png)

## Features

### PostgreSQL 17 Key Features

PostgreSQL 17 introduces several significant improvements:

- **Enhanced Logical Replication**: Improved performance and reliability for logical replication
- **Better Query Performance**: Optimized query planning and execution
- **Improved Partitioning**: Enhanced table partitioning capabilities
- **Security Enhancements**: New security features and improvements
- **Monitoring Improvements**: Better monitoring and observability tools
- **Performance Optimizations**: Various performance improvements across the board

## ⚠️ Important Disclaimer

**This is a Proof of Concept (PoC) setup and should NOT be used in production environments.**

This setup is intended for:
- Learning purposes
- Development environments
- Testing replication concepts
- Understanding PostgreSQL replication basics

**Production Considerations:**
- This setup lacks proper security hardening
- No backup strategy is implemented
- No monitoring or alerting is configured
- No high availability features beyond basic replication
- No disaster recovery procedures
- No proper network isolation
- No SSL/TLS encryption
- No proper user and role management
- No proper logging and auditing

For production environments, please consider:
- Implementing proper security measures
- Setting up monitoring and alerting
- Configuring proper backup and recovery procedures
- Using a managed database service
- Consulting with database experts
- Following PostgreSQL best practices

### Cluster Features

- Primary (Read-Write) and Read Replica setup
- Automatic replication configuration
- Persistent data storage
- Health monitoring
- Isolated network environment

## Architecture

### How Replication Works

This setup implements physical replication between the primary and replica nodes:

1. The primary node (`postgres_primary`) accepts write operations
2. Changes are written to the Write-Ahead Log (WAL)
3. The replica (`postgres_replica`) continuously streams these WAL records
4. The replica applies these changes to maintain an exact copy of the primary
5. The replica operates in hot standby mode, allowing read operations

### Docker Compose Configuration

The `docker-compose.yml` file manages two PostgreSQL containers:

- **Primary Node (postgres_primary)**:
  - Runs on port 5432
  - Handles read/write operations
  - Maintains the primary database

- **Read Replica (postgres_replica)**:
  - Runs on port 5433
  - Automatically connects to the primary
  - Handles read-only operations
  - Maintains an exact copy of the primary data

The configuration includes:
- Health checks for both instances
- Persistent volumes for data storage
- Automatic replication setup
- Dedicated network for container communication

## Quick Start

1. Clone the repository:
```bash
git clone git@github.com:luismr/postgres-docker-compose-cluster.git
cd postgres-docker-compose-cluster
```

2. Start the cluster:
```bash
docker-compose up -d
```

3. Verify the services are running:
```bash
docker-compose ps
```

## Usage

### Checking Replication Status

```bash
# Connect to the primary
docker exec -it postgres_primary psql -U postgres -c "SELECT * FROM pg_stat_replication;"

# Connect to the replica
docker exec -it postgres_replica psql -U postgres -c "SELECT pg_is_in_recovery();"
```

### Testing Replication

1. Create a table on the primary:
```bash
docker exec -it postgres_primary psql -U postgres -c "CREATE TABLE test (id SERIAL PRIMARY KEY, data TEXT);"
```

2. Insert some data on the primary:
```bash
docker exec -it postgres_primary psql -U postgres -c "INSERT INTO test (data) VALUES ('test data');"
```

3. Verify the data is replicated to the read replica:
```bash
docker exec -it postgres_replica psql -U postgres -c "SELECT * FROM test;"
```

### Stopping the Cluster

To stop the cluster:
```bash
docker-compose down
```

To stop and remove volumes (this will delete all data):
```bash
docker-compose down -v
```

## Contributing

We welcome contributions! To contribute:

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details. 