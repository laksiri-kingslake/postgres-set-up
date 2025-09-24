# PostgreSQL Setup and Configuration in WSL

## PostgreSQL Installation and Set-up for External Access
1. Remove any existing installations
```bash
sudo apt-get remove postgresql postgresql-contrib
```

2. Install
```bash
sudo apt-get update
sudo apt-get install postgresql postgresql-contrib
sudo systemctl status postgresql
psql --version
```

3. Set password to postgres user
```bash
sudo passwd postgres
```

4. Start service
```bash
sudo service postgresql start
```

5. Change user password
```bash
sudo -u postgres psql
```
```sql
ALTER USER postgres WITH PASSWORD 'my-new-secure-password';
\password postgres
```

6. Config postgresql.conf
```bash
# to get config file path
sudo -u postgres psql -c "SHOW config_file;"

# usually it is /etc/postgresql/[postgres-version]/main/postgresql.conf
sudo nano /etc/postgresql/[postgres-version]/main/postgresql.conf

# change
# listen_addresses = 'localhost'
# to
listen_addresses = '*'
```

7. Configure pg_hba.conf
```bash
# to get file path
sudo -u postgres psql -c "SHOW hba_file;"

# usually it is /etc/postgresql/[postgres-version]/main/pg_hba.conf
sudo nano /etc/postgresql/[postgres-version]/main/pg_hba.conf

# add the following line in the bottom
host    all             all             0.0.0.0/0            md5

# restart service
sudo service postgresql restart
```

8. Check psql postgres login using password
```bash
psql -U postgres -h localhost -W

# If it doesn't allow login
sudo -u postgres psql
```
```pgsql
ALTER USER postgres WITH PASSWORD 'MyNewSecurePassword';
\password postgres
```

## Create a Database and a User
1. Create the DB user and database (run as postgres)
```bash
# become postgres user
sudo -i -u postgres

# 1) create role (replace password)
psql -c "CREATE ROLE kauser WITH LOGIN PASSWORD 'StrongP@ssw0rd!';"

# 2) create DB owned by kauser
psql -c "CREATE DATABASE ka_db OWNER kauser;"
```

2. Give schema / table privileges (optional but useful)
```bash
# grant usage/privileges on public schema and default privileges for future objects
psql -d ka_db -c "GRANT ALL ON SCHEMA public TO kauser;"
psql -d ka_db -c "GRANT ALL PRIVILEGES ON DATABASE ka_db TO kauser;"
psql -d ka_db -c "ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT, INSERT, UPDATE, DELETE ON TABLES TO kauser;"
```

3. Restart PostgreSQL
```bash
# most systems:
sudo systemctl restart postgresql

# fallback:
sudo service postgresql restart
```

4. Test connection
```bash
psql -h <server-ip> -p 5432 -U kauser -d ka_db
```

## pgAdmin Windows installation and Configuration
1. Download and install pgAdmin 4 latest stable version.

2. Get wsl ubuntu ip address - Only for wsl postgres servers
```bash
ip addr show eth0

# in below case use 172.28.82.113 as ip address
# inet 172.28.82.113/20
```

3. Set configurations </br>
3.1. In the **Explorer** go to **Servers** and in **Quick Links** click **Add New Server** </br>
3.2. In **General** tab give a name to the server </br>
3.3. In **Connection** tab enter the following
- Host name/address: [ip address of wsl]
- Port: 5432
- Maintenance database: postgres
- Username: postgres
- Password: [password of above user]

## psql for Database, Tables and Data

1. List available databases
```bash
psql -U postgres -h localhost -W
```
```sql
\l
```

2. List available tables and show data
```bash
# psql -U your_username -d your_database_name
psql -U postgres -d iceberg_catalog -h localhost -W
```
```sql
\dt

-- show data
select * from guest_queries;

```

## Troubleshooting Tips
1. On the server
```bash
# check postgres service status
sudo systemctl status postgresql

# follow logs
sudo journalctl -u postgresql -f

# or find log files (Debian/Ubuntu)
sudo tail -n 200 /var/log/postgresql/*.log
```

2. On client
```bash
# check TCP reachability
nc -vz <server-ip> 5432
# or
telnet <server-ip> 5432
```

3. More secure (recommended) options
- Use an SSH tunnel instead of opening 5432 to the internet:
```bash
ssh -i /path/to/key.pem -L 5432:localhost:5432 ubuntu@EC2_PUBLIC_IP
# then point pgAdmin/psql to localhost:5432
```

- If using the DB for production, require SSL, restrict access to known CIDR ranges, or place DB in a private subnet and access through a bastion host / VPN.

## Open port 5432 in AWS Security Group (IMPORTANT)
1. In AWS Console → EC2 → Instances → select your instance → Security → click the Security Group.

2. Edit Inbound rules → Add rule:
- Type: PostgreSQL (port 5432)
- Source: Custom → 
<your-ip>/32 (or use "My IP" button)

3. Save

## Reference
1. [postgresql.org](https://www.postgresql.org/)