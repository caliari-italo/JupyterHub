# JupyterHub

`docker run -p 8000:8000 -d --restart unless-stopped --runtime=nvidia --gpus '"device=0"' -v ./:/home/ --name jupyterhub quay.io/jupyterhub/jupyterhub jupyterhub`

`docker exec -it jupyterhub bash`

`pip install jupyterlab`

`adduser admin`

`cat > jupyterhub_config.py <<'EOF'
c = get_config()
c.Authenticator.admin_users = {"admin"}
c.Authenticator.allow_all = True
c.LocalAuthenticator.create_system_users=True
EOF`

`docker container restart jupyterhub`

`userdel -r username`
