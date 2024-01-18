Guia de Instalação do NetBox no Ubuntu 22.04 LTS

Antes de começar, é altamente recomendável realizar um snapshot da máquina para facilitar possíveis rollbacks após atualizações.

Requisitos Mínimos:

Memória: 4GB (mínimo)
Python: >= 3.8
PostgreSQL: >= 10
Redis: >= 4.0
Atualizar a máquina:

sudo apt update
Instalar PostgreSQL:

sudo apt install -y postgresql
Verificar versão do PostgreSQL:

psql -V
Criar database PostgreSQL:

sudo -u postgres psql
Instalar Redis:

sudo apt install -y redis-server
Verificar status do Redis:

redis-cli ping
Instalar pacotes essenciais do Python:

sudo apt install -y python3 python3-pip python3-venv python3-dev build-essential libxml2-dev libxslt1-dev libffi-dev libpq-dev libssl-dev zlib1g-dev
Atualizar o Pip:

pip3 install --upgrade pip
Baixar e configurar NetBox:

wget https://github.com/netbox-community/netbox/archive/refs/tags/v3.6.7.tar.gz
tar -xzf v3.6.7.tar.gz -C /opt
ln -s /opt/netbox
Criar usuário NetBox:

sudo adduser --system --group netbox
sudo chown --recursive netbox /opt/netbox/netbox/media/
Configurar NetBox:

cd /opt/netbox/netbox/netbox/
cp configuration_example.py configuration.py
Editar configuration.py:

Em "ALLOWED_HOSTS =", informar o IP ou nome da VM.
Gerar chave secreta:

python3 ../generate_secret_key.py
Instalar ambiente virtual do Python:

echo napalm >> /opt/netbox/local_requirements.txt
Executar script de upgrade:

cd /opt/netbox
./upgrade.sh
Ativar ambiente virtual do Python:

source /opt/netbox/venv/bin/activate
Criar superusuário:

cd /opt/netbox/netbox
python3 manage.py createsuperuser
Executar servidor de desenvolvimento:

python3 manage.py runserver 0.0.0.0:8000 --insecure
Configurar firewall:

Verificar se a porta 8000 (ou outra especificada) está habilitada.
Desativar ambiente virtual:

deactivate
Configurar Gunicorn e serviços do sistema:

cp /opt/netbox/contrib/gunicorn.py /opt/netbox/gunicorn.py
cp -v /opt/netbox/contrib/*.service /etc/systemd/system/
systemctl daemon-reload
systemctl start netbox netbox-rq
systemctl enable netbox netbox-rq
systemctl status netbox
Instalar e configurar Nginx:

sudo apt install -y nginx
cp /opt/netbox/contrib/nginx.conf /etc/nginx/sites-available/netbox
vi /etc/nginx/sites-available/netbox
Dentro do arquivo netbox, informar o IP da máquina. Se não houver certificado SSL, comentar as três linhas relacionadas a SSL.
Reiniciar Nginx para aplicar alterações:

bash
Copy code
systemctl restart nginx
Com essas instruções, você terá o NetBox instalado e configurado no seu ambiente Ubuntu 22.04 LTS. Certifique-se de ajustar conforme necessário, especialmente versões e caminhos, e acompanhe possíveis atualizações do NetBox e suas dependências.
