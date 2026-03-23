# Configuração do JupyterHub com Docker e Suporte a GPU

Este documento detalha os passos para configurar o JupyterHub utilizando Docker, incluindo suporte a GPUs NVIDIA e a configuração inicial de usuários.

## Comandos de Configuração

A seguir, são apresentados os comandos necessários para a implantação e configuração do JupyterHub, acompanhados de suas respectivas descrições para facilitar a compreensão.

```bash
# 1. Inicia o contêiner Docker do JupyterHub
#    -p 8000:8000: Mapeia a porta 8000 do host para a porta 8000 do contêiner, permitindo acesso externo.
#    -d: Executa o contêiner em modo 'detached' (segundo plano).
#    --restart unless-stopped: Garante que o contêiner será reiniciado automaticamente, a menos que seja explicitamente parado.
#    --runtime=nvidia --gpus '"device=0"': Ativa o suporte a GPU NVIDIA, especificando o uso da GPU de índice 0.
#    -v ./:/home/: Monta o diretório atual do host como /home/ dentro do contêiner, permitindo persistência de dados.
#    --name jupyterhub: Atribui o nome 'jupyterhub' ao contêiner para fácil identificação.
#    quay.io/jupyterhub/jupyterhub: Imagem Docker oficial do JupyterHub a ser utilizada.
#    jupyterhub: Comando para iniciar o serviço JupyterHub dentro do contêiner.
docker run -p 8000:8000 -d --restart unless-stopped --runtime=nvidia --gpus '"device=0"' -v ./:/home/ --name jupyterhub quay.io/jupyterhub/jupyterhub jupyterhub

# 2. Acessa o shell Bash do contêiner JupyterHub em execução
#    -it: Permite uma interação interativa com o terminal do contêiner.
#    jupyterhub: Nome do contêiner alvo.
#    bash: Comando para iniciar o shell Bash dentro do contêiner.
docker exec -it jupyterhub bash

# 3. Instala o JupyterLab dentro do contêiner
#    pip install jupyterlab: Instala a interface de usuário avançada do Jupyter, o JupyterLab.
pip install jupyterlab

# 4. Cria um novo usuário de sistema 'admin' dentro do contêiner
#    adduser admin: Adiciona um novo usuário de sistema chamado 'admin', que poderá ser usado para login no JupyterHub.
adduser admin

# 5. Configura o JupyterHub para definir usuários administradores e permitir criação automática de usuários
#    cat > jupyterhub_config.py <<'EOF': Cria ou sobrescreve o arquivo de configuração do JupyterHub.
#    c = get_config(): Obtém o objeto de configuração do JupyterHub.
#    c.Authenticator.admin_users = {"admin"}: Define 'admin' como um usuário administrador do JupyterHub.
#    c.Authenticator.allow_all = True: Permite que qualquer usuário se autentique no JupyterHub.
#    c.LocalAuthenticator.create_system_users=True: Habilita a criação automática de usuários de sistema no primeiro login.
cat > jupyterhub_config.py <<'EOF'
c = get_config()
c.Authenticator.admin_users = {"admin"}
c.Authenticator.allow_all = True
c.LocalAuthenticator.create_system_users=True
EOF

# 6. Reinicia o contêiner Docker do JupyterHub para aplicar as novas configurações
#    docker container restart jupyterhub: Reinicia o contêiner para que as alterações no arquivo de configuração sejam efetivadas.
docker container restart jupyterhub

# 7. Remove um usuário de sistema e seu diretório home (exemplo de limpeza)
#    userdel -r username: Exclui o usuário 'username' e remove seu diretório home. Este comando é um exemplo de como remover um usuário e deve ser ajustado conforme a necessidade.
userdel -r username
```
