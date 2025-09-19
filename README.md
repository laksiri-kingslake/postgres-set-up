# PostgreSQL Setup and Configuration in WSL

## PostgresSQL Installation and Set-up for External Access
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

5. Connect to using psql
```bash
sudo -u postgres psql
```

6. Create a user
```bash
sudo -u postgres createuser myuser
```

7. Set user password
```bash
sudo -u postgres psql
```
```pgsql
alter user myuser with encrypted password 'mypassword';
```

8. Creating a database
```bash
sudo -u postgres createdb mydb
```

9. Granting privileges on database
```bash
sudo -u postgres psql
```
```pgsql
grant all privileges on database mydb to myuser ;
```

10. Change user password
```bash
sudo -u postgres psql
```
```pgsql
ALTER USER postgres WITH PASSWORD 'MyNewSecurePassword123!';
\password postgres
```

11. Config postgresql.conf
```bash
sudo nano /etc/postgresql/[postgres-version]/main/postgresql.conf

# change
# listen_addresses = 'localhost'
# to
listen_addresses = '*'
```

13. Configure pg_hba.conf
```bash
sudo nano /etc/postgresql/[postgres-version]/main/pg_hba.conf

# add the following line in the bottom
host    all             all             0.0.0.0/0            md5

# restart
sudo service postgresql restart
```

14. Check psql postgres login using password
```bash
psql -U postgres -h localhost -W

# If it doesn't allow login
sudo -u postgres psql
```
```pgsql
ALTER USER postgres WITH PASSWORD 'MyNewSecurePassword';
\password postgres
```

## pgAdmin Windows installation and Configuration
1. Download and install pgAdmin 4 latest stable version.

2. Get wsl ubuntu ip address
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

## psql for database, tables and data

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

## Reference
1. [postgresql.org](https://www.postgresql.org/)