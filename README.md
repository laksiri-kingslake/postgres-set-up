# PostgreSQL Setup and Configuration in WSL

## PostgresSQL Installation and Set-up for External Access
1. Remove any existing installations
```bash
sudo apt-get remove postgresql
```

2. Install
```bash
sudo apt-get update
sudo apt-get install postgresql
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

2. Get wsl ubuntu password
```bash
ip addr show eth0
```

3. Set configurations