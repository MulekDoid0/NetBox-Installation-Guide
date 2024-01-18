# NetBox Installation Guide for Ubuntu 22.04 LTS

Before starting, it's highly recommended to create a machine snapshot for easy rollbacks after updates.

Minimum Requirements:

Memory: 4GB (minimum)
Python: >= 3.8
PostgreSQL: >= 10
Redis: >= 4.0
Update the machine:

Copy code
sudo apt update
Install PostgreSQL:

Copy code
sudo apt install -y postgresql
Check PostgreSQL version:

Copy code
psql -V
Create PostgreSQL database:

Copy code
sudo -u postgres psql
Install Redis:

Copy code
sudo apt install -y redis-server
Check Redis status:

Copy code
redis-cli ping
Install essential Python packages:

Copy code
sudo apt install -y python3 python3-pip python3-venv python3-dev build-essential libxml2-dev libxslt1-dev libffi-dev libpq-dev libssl-dev zlib1g-dev
Update Pip:

Copy code
pip3 install --upgrade pip
Download and configure NetBox:

Copy code
wget https://github.com/netbox-community/netbox/archive/refs/tags/vx.x.x.tar.gz
tar -xzf vx.x.x.tar.gz -C /opt
ln -s /opt/netbox
Create NetBox user:

Copy code
sudo adduser --system --group netbox
sudo chown --recursive netbox /opt/netbox/netbox/media/
Configure NetBox:

Copy code
cd /opt/netbox/netbox/netbox/
cp configuration_example.py configuration.py
Edit configuration.py:

In "ALLOWED_HOSTS =", provide the IP or VM name.
Generate secret key:

Copy code
python3 ../generate_secret_key.py
Install Python virtual environment:

Copy code
echo napalm >> /opt/netbox/local_requirements.txt
Run upgrade script:

Copy code
cd /opt/netbox
./upgrade.sh
Activate Python virtual environment:

Copy code
source /opt/netbox/venv/bin/activate
Create superuser:

Copy code
cd /opt/netbox/netbox
python3 manage.py createsuperuser
Run development server:

Copy code
python3 manage.py runserver 0.0.0.0:8000 --insecure
Configure firewall:

Ensure that port 8000 (or specified) is enabled.
Deactivate virtual environment:

Copy code
deactivate
Configure Gunicorn and system services:

Copy code
cp /opt/netbox/contrib/gunicorn.py /opt/netbox/gunicorn.py
cp -v /opt/netbox/contrib/*.service /etc/systemd/system/
systemctl daemon-reload
systemctl start netbox netbox-rq
systemctl enable netbox netbox-rq
systemctl status netbox
Install and configure Nginx:

Copy code
sudo apt install -y nginx
cp /opt/netbox/contrib/nginx.conf /etc/nginx/sites-available/netbox
vi /etc/nginx/sites-available/netbox
Inside the netbox file, provide the machine's IP. If no SSL certificate is available, comment the three SSL-related lines.
Restart Nginx to apply changes:

Copy code
systemctl restart nginx
With these instructions, you'll have NetBox installed and configured on your Ubuntu 22.04 LTS environment. Be sure to adjust as necessary, especially versions and paths, and keep an eye on potential updates for NetBox and its dependencies.
