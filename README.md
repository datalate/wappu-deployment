# wappu-deployment

Ansible deployment for the wappu API + frontend.

The frontend role installs Node.js and nginx, builds the frontend, and serves it.
The backend role installs the .NET SDK, builds the API, and runs it (plus irssi and
the log parser) as systemd user services.

Deployed projects:

- Frontend: <https://github.com/datalate/wappu-frontend>
- API: <https://github.com/datalate/wappu-api>

> Only Debian 12+ is currently supported. Could be reworked to support Ubuntu too
> with some small changes.

> The nginx site this deploys serves plain **HTTP** only. For HTTPS, put a separate
> reverse proxy in front that terminates TLS and forwards to this host (nginx is a
> good choice).

## Layout

```
ansible.cfg
site.yml                 # top-level playbook
inventory/hosts          # target hosts
group_vars/
  frontend.yml           # frontend vars (api_base_url, node version, ...)
  backend.yml            # backend vars (wappu_api_key, dotnet version, ...)
roles/
  frontend/
  backend/
```

## Prerequisites

- Ansible installed on your machine (`ansible-playbook` on your PATH).
- SSH access to the target hosts as the user defined in `inventory/hosts`.

Install the required collections:

```sh
ansible-galaxy collection install -r requirements.yml
```

## Configure

1. **Set the target hosts.** Edit `inventory/hosts` and replace the `x.x.x.x`
   placeholders with your server IPs/hostnames (and adjust `ansible_user` if needed).

2. **Set the API key.** Edit `group_vars/backend.yml` and set `wappu_api_key`:

   ```yaml
   wappu_api_key: "your-secret-api-key"
   ```

3. **Set the frontend API base URL.** Edit `group_vars/frontend.yml` and set
   `api_base_url` to the URL the frontend should call:

   ```yaml
   api_base_url: "https://your-domain/api"
   ```

## Run

Deploy everything:

```sh
ansible-playbook site.yml
```

Deploy only one side:

```sh
ansible-playbook site.yml --tags frontend
ansible-playbook site.yml --tags backend
```

Check the playbook without applying changes:

```sh
ansible-playbook site.yml --syntax-check
ansible-playbook site.yml --check
```
