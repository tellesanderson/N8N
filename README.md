# N8N com PostgreSQL e Cloudflare Tunnel

Deploy completo do N8N com PostgreSQL usando Docker Compose e Cloudflare Tunnel para expor via HTTPS em seu subdomínio personalizado.

**O que é este projeto?**
- 🐳 N8N + PostgreSQL em containers Docker
- 🔒 SSL/HTTPS automático via Cloudflare Tunnel
- 🌐 Expõe seu N8N em `seu-subdominio.seu-dominio.com.br`
- ⚡ Sem gerenciar certificados SSL manualmente

## 📋 Pré-requisitos

- Docker (20.10+)
- Docker Compose (2.0+)
- Domínio com DNS gerenciado pelo Cloudflare
- Conta Cloudflare (gratuita funciona)

## 🔗 Configuração do Cloudflare Tunnel

### 1. Crie uma conta Cloudflare
1. Acesse [Cloudflare](https://dash.cloudflare.com/)
2. Crie uma conta ou faça login
3. Adicione seu domínio ao Cloudflare (siga as instruções)

### 2. Gere o Token do Tunnel
1. No dashboard Cloudflare, vá para: **Zero Trust** → **Networks** → **Tunnels**
2. Clique em **Create a tunnel**
3. Escolha um nome (ex: `n8n-prod`)
4. Selecione **Docker** como ambiente
5. Copie o comando que aparece, procure por `--token=...`
6. Copie apenas o valor do token (a parte depois de `--token=`)

### 3. Cole o token no `.env`
```env
CLOUDFLARE_TUNNEL_TOKEN=seu_token_copiado_aqui
```

### 4. Configure a rota no Cloudflare
1. De volta ao painel do tunnel, vá para **Public Hostname**
2. Clique em **Create public hostname**
3. Configure:
   - **Subdomain**: `n8n` (ou o nome que quiser)
   - **Domain**: seu-dominio.com.br
   - **Type**: HTTPS
   - **URL**: `http://n8n:5678`
4. Salve

### 5. Verifique a conectividade
O tunnel deve aparecer como **Connected** no painel.

> **Documentação oficial**: [Cloudflare Tunnel - Get started](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/tunnel-guide/)

## 📋 Pré-requisitos

- Docker (20.10+)
- Docker Compose (2.0+)

## 🚀 Quick Start

### 1. Clone o repositório
```bash
git clone <seu-repo>
cd n8n
```

### 2. Configure o ambiente
```bash
cp .env.example .env
```

### 3. Edite o `.env` com seus valores
```env
# Banco de dados
POSTGRES_PASSWORD=senha_segura_aqui

# N8N - Atualize com seu domínio
N8N_HOST=seu-dominio.com.br
WEBHOOK_URL=https://seu-dominio.com.br

# Cloudflare Tunnel - Se usar
CLOUDFLARE_TUNNEL_TOKEN=seu_token_docker_aqui
```

### 4. Inicie os containers
```bash
docker compose up -d
```

### 5. Acesse
- **Remoto (com SSL)**: `https://seu-dominio.com.br`
- **Local**: `http://localhost:5678` (após expor a porta no docker-compose.yml)

## 📁 Estrutura

```
.
├── docker-compose.yml    # Orquestração de containers
├── .env.example          # Template de variáveis (commitar no Git)
├── .env                  # Variáveis reais (NÃO commitar)
├── .gitignore           # Arquivos ignorados pelo Git
├── n8n_data/            # Dados e configurações do N8N (volume)
└── postgres_data/       # Dados do PostgreSQL (volume)
```

## 🐳 Serviços

### N8N
- **Imagem**: Configurável via `.env` (`N8N_IMAGE`)
- **Porta interna**: 5678
- **Volumes**: `./n8n_data:/home/node/.n8n`
- **Database**: PostgreSQL

### PostgreSQL
- **Imagem**: Configurável via `.env` (`POSTGRES_IMAGE`)
- **Porta**: 5432
- **Database**: `n8n`
- **Volumes**: `./postgres_data:/var/lib/postgresql`

### Cloudflare Tunnel
- **Imagem**: Configurável via `.env` (`CLOUDFLARED_IMAGE`)
- **Função**: Expor N8N via SSL para domínio remoto
- **Config**: Token via `.env`

## 🛠️ Comandos Úteis

### Iniciar
```bash
docker compose up -d
```

### Parar
```bash
docker compose down
```

### Ver logs
```bash
docker compose logs -f n8n
docker compose logs -f postgres
docker compose logs -f cloudflared
```

### Acessar container
```bash
docker exec -it n8n bash
docker exec -it postgres psql -U n8n -d n8n
```

### Resetar tudo (⚠️ perderá dados)
```bash
docker compose down -v
```

## 🔐 Segurança

- **Senhas**: Não commite o `.env` (está em `.gitignore`)
- **N8N**: Use credenciais fortes no seu usuário
- **PostgreSQL**: Atualize `POSTGRES_PASSWORD` no `.env`
- **Tunnel Token**: Gerado pelo Cloudflare, nunca compartilhe

## 🔄 Atualizações

Para atualizar versões de imagens:
umentation](https://docs.n8n.io/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [Cloudflare Tunnel - Setup Guide](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/tunnel-guide/)
- [Cloudflare Tunnel - Get started](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/)
- [Como adicionar domínio ao Cloudflare](https://developers.cloudflare.com/dns/zone-setup
N8N_IMAGE=n8nio/n8n:2.28.0
POSTGRES_IMAGE=postgres:18.5
CLOUDFLARED_IMAGE=cloudflare/cloudflared:2026.7.0
```

2. Reinicie os containers:
```bash
docker compose down
docker compose up -d
```

## 📝 Variáveis de Ambiente

| Variável | Descrição | Exemplo |
|----------|-----------|---------|
| `POSTGRES_USER` | Usuário do PostgreSQL | `n8n` |
| `POSTGRES_PASSWORD` | Senha do PostgreSQL | `sua_senha_segura` |
| `POSTGRES_DB` | Nome do banco | `n8n` |
| `N8N_HOST` | Host do N8N | `n8n.dominio.com.br` |
| `N8N_PORT` | Porta do N8N | `5678` |
| `WEBHOOK_URL` | URL externa para webhooks | `https://n8n.dominio.com.br` |
| `CLOUDFLARE_TUNNEL_TOKEN` | Token do Cloudflare Tunnel | Obtido do Cloudflare |

## 🐛 Troubleshooting

### N8N não consegue conectar ao PostgreSQL
- Verifique se o container `postgres` está rodando: `docker ps`
- Confirme `DB_HOST` no `.env` (deve ser `postgres`)

### Cloudflare Tunnel não funciona
- Verifique o token em `.env`
- Veja os logs: `docker compose logs cloudflared`

### Erros de permissão
- Volumes podem precisar de permissões ajustadas no host

## 📚 Referências

- [N8N Docs](https://docs.n8n.io/)
- [Docker Compose Docs](https://docs.docker.com/compose/)
- [Cloudflare Tunnel Docs](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/)

## 📄 Licença

[Sua licença aqui]
