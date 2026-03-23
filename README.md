# Configuração do JupyterHub com Docker e Suporte a GPU

Este documento detalha os passos para configurar o JupyterHub utilizando Docker, incluindo suporte a GPUs NVIDIA e a configuração inicial de usuários.

## Comandos de Configuração

A seguir, são apresentados os comandos necessários para a implantação e configuração do JupyterHub, acompanhados de suas respectivas descrições para facilitar a compreensão.

### 1. Iniciar o contêiner Docker do JupyterHub

Este comando inicia o contêiner do JupyterHub em modo `detached` (segundo plano), mapeia a porta 8000 do host para o contêiner, configura o reinício automático, ativa o suporte a GPU NVIDIA (dispositivo 0), monta o diretório atual para persistência de dados e atribui o nome `jupyterhub` ao contêiner.

```bash
docker run -p 8000:8000 -d --restart unless-stopped --runtime=nvidia --gpus '"device=0"' -v ./:/home/ --name jupyterhub quay.io/jupyterhub/jupyterhub jupyterhub
```

### 2. Acessar o shell Bash do contêiner JupyterHub

Este comando permite acessar interativamente o shell Bash dentro do contêiner `jupyterhub` em execução, o que é necessário para realizar configurações internas.

```bash
docker exec -it jupyterhub bash
```

### 3. Instalar o JupyterLab

Dentro do contêiner, este comando instala o JupyterLab, que é a interface de usuário avançada para ambientes Jupyter.

```bash
pip install jupyterlab
```

### 4. Criar um novo usuário de sistema

Este comando cria um novo usuário de sistema chamado `admin` dentro do contêiner. Este usuário poderá ser utilizado para login no JupyterHub.

```bash
adduser admin
```

### 5. Configurar o JupyterHub

Este bloco de comandos cria ou sobrescreve o arquivo `jupyterhub_config.py` para definir `admin` como um usuário administrador, permitir que qualquer usuário se autentique e habilitar a criação automática de usuários de sistema no primeiro login.

```bash
cat > jupyterhub_config.py <<'EOF'
c = get_config()
c.Authenticator.admin_users = {"admin"}
c.Authenticator.allow_all = True
c.LocalAuthenticator.create_system_users=True
EOF
```

### 6. Sair do shell do contêiner

Este comando encerra a sessão interativa do shell dentro do contêiner, retornando ao terminal do host.

```bash
exit
```

### 7. Reiniciar o contêiner Docker do JupyterHub

Após as alterações de configuração, este comando reinicia o contêiner `jupyterhub` para que as novas configurações sejam efetivadas.

```bash
docker container restart jupyterhub
```

### 8. Acessar o JupyterHub no navegador

Após a conclusão das etapas de configuração e reinício do contêiner, o JupyterHub estará acessível através deste endereço no seu navegador web.

```
localhost:8000
```
