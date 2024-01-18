Guia de Instalação do NetBox no Ubuntu 22.04 LTS

Antes de começar, é altamente recomendável realizar um snapshot da máquina para facilitar possíveis rollbacks após atualizações.

Requisitos Mínimos:

Memória: 4GB (mínimo)
Python: >= 3.8
PostgreSQL: >= 10
Redis: >= 4.0
Atualizar a máquina:

apt update - atualizar softwares da maquina

apt install -y postgresql - instalar o postgres

psql -V - verificar a versão do postgres

sudo -u postgres psql - criação da data base
CREATE DATABASE netbox;
CREATE USER netbox WITH PASSWORD 'J5brHrAXFLQSif0K';
ALTER DATABASE netbox OWNER TO netbox;
-- the next two commands are needed on PostgreSQL 15 and later
\connect netbox;
GRANT CREATE ON SCHEMA public TO netbox;

apt install -y redis-server - instalar o redis

redis-cli ping - tem que retornar como "PONG" para ter certeza que serviço esta ok

sudo apt install -y python3 python3-pip python3-venv python3-dev build-essential libxml2-dev libxslt1-dev libffi-dev libpq-dev libssl-dev zlib1g-dev - instalar o python

pip3 install --upgrade pip - rodar após a instalação do python com seus pacotes

wget https://github.com/netbox-community/netbox/archive/refs/tags/vx.x.x.tar.gz
tar -xzf vx.x.x.tar.gz -C /opt
ln -s /opt/netbox
ls -l /opt | grep netbox - Obs: instalar a versão conforme o site da netbox pois pode atualizar

adduser --system -group netbox
chown --recursive netbox /opt/netbox/netbox/media/ - criar usuário netbox

cd /opt/netbox/netbox/netbox/
cp configuration_example.py configuration.py - copiar pasta do arquivo principal

entrar dentro do arquivo configuration.py em "ALLOWED_HOSTS =" informar o IP, nome da VM (para testes '*')

python3 ../generate_secret_key.py - gerar chave nesse comando para colar detro do arquivo configuration.py

echo napalm >> /opt/netbox/local_requirements.txt - cria o ambiente virtual do python

cd /opt/netbox
./upgrade.sh - script para fazer o upgrade

source /opt/netbox/venv/bin/activate - ambiente virtual do python

cd /opt/netbox/netbox
python3 manage.py createsuperuser - criar usuario admin para netbox

python3 manage.py runserver 0.0.0.0:8000 --insecure - verificar se esta funcional dentro do ambiente python (informar o ip da maquina seguido da porta do firewall)

verificar se a porta 8000 ou qualquer outra que informar esta habilitada no firewall

comando "deactivate" sai do interface python

cp /opt/netbox/contrib/gunicorn.py /opt/netbox/gunicorn.py
cp -v /opt/netbox/contrib/*.service /etc/systemd/system/ arquivos de serviço do gunicorn

systemctl daemon-reload
systemctl start netbox netbox-rq
systemctl enable netbox netbox-rq
systemctl status netbox - ativar serviços netbox 

apt install -y nginx
cp /opt/netbox/contrib/nginx.conf /etc/nginx/sites-available/netbox - instalar servidor web

vi /etc/nginx/sites-available/netbox - entrar dentro do arquivo netbox.conf para informar ip da maquina e caso não tiver certificado comentar a 3 linhas do ssl

systemctl restart nginx - para subir as configurações alteradas

Com essas instruções, você terá o NetBox instalado e configurado no seu ambiente Ubuntu 22.04 LTS. Certifique-se de ajustar conforme necessário, especialmente versões e caminhos, e acompanhe possíveis atualizações do NetBox e suas dependências.
