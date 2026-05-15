# Deploy do site Zambom Network no Cloudflare Pages

Guia passo a passo para colocar `zambomnetwork.com.br` no ar.

## Estrutura do projeto

```
zambom-network/
├── index.html          # Página principal
├── 404.html            # Página de erro 404 (CF Pages serve automaticamente)
├── css/style.css       # Estilos
├── js/main.js          # Scripts
├── sitemap.xml         # Mapa do site para Google
├── robots.txt          # Diretrizes para crawlers
├── _headers            # Cabeçalhos HTTP (segurança + cache) — Cloudflare Pages
├── _redirects          # Redirecionamentos (www → apex) — Cloudflare Pages
└── DEPLOY.md           # Este arquivo
```

> O Cloudflare Pages reconhece automaticamente `404.html`, `_headers` e `_redirects`. Não precisa de configuração adicional.

---

## Passo 1 — Pré-requisito: domínio gerenciado pelo Cloudflare

Você já está apontando o domínio para a Cloudflare. Confirme:

1. Acesse https://dash.cloudflare.com → selecione `zambomnetwork.com.br`
2. Em **Overview**, o status deve estar **Active** (verde)
3. Em **DNS → Records**, deve haver alguns registros (mesmo que ainda não apontem para o site)

Se ainda não terminou a transferência de nameservers, espere o status virar **Active** antes de continuar.

---

## Passo 2 — Escolher o método de deploy

A Cloudflare Pages tem três formas de publicar. Recomendo na ordem abaixo do mais simples para o mais avançado:

### Método A — Upload direto (drag & drop) — **mais simples**
Bom para começar agora, sem precisar de conta no GitHub.

### Método B — Conectar a um repositório GitHub/GitLab — **recomendado para produção**
Cada `git push` republica o site automaticamente.

### Método C — Wrangler CLI
Para desenvolvedores que querem deploy via linha de comando.

---

## Método A — Upload direto (passo a passo)

1. Acesse https://dash.cloudflare.com
2. No menu lateral, clique em **Workers & Pages**
3. Clique em **Create** → aba **Pages** → **Upload assets**
4. Nome do projeto: `zambom-network` (será o subdomínio temporário `zambom-network.pages.dev`)
5. Compacte a pasta `D:\Documentos\zambom-network` em um `.zip` (ou arraste a pasta inteira)
6. Faça o upload → clique em **Deploy site**
7. Em 30s o site estará no ar em `https://zambom-network.pages.dev`
8. Vá para o **Passo 4** para conectar o domínio customizado

---

## Método B — Conectar a um repositório GitHub

### 1. Subir os arquivos para o GitHub

Crie um repositório em https://github.com/new (público ou privado, ambos funcionam com CF Pages).

```bash
cd D:\Documentos\zambom-network
git init
git add .
git commit -m "Initial production deploy"
git branch -M main
git remote add origin https://github.com/SEU-USUARIO/zambom-network.git
git push -u origin main
```

### 2. Conectar no Cloudflare Pages

1. Acesse https://dash.cloudflare.com → **Workers & Pages**
2. **Create** → **Pages** → **Connect to Git**
3. Autorize o Cloudflare a acessar seu GitHub
4. Selecione o repositório `zambom-network`
5. Build settings:
   - **Framework preset**: None
   - **Build command**: (deixe vazio)
   - **Build output directory**: `/` (ou deixe vazio)
6. Clique em **Save and Deploy**

Pronto — em ~1 min o site estará em `https://zambom-network.pages.dev`.

A partir daí, todo `git push` na branch `main` faz deploy automático.

---

## Método C — Wrangler CLI (avançado)

```bash
npm install -g wrangler
wrangler login
cd D:\Documentos\zambom-network
wrangler pages deploy . --project-name=zambom-network
```

---

## Passo 4 — Conectar o domínio customizado

Independente do método escolhido acima:

1. No painel do Pages, abra o projeto `zambom-network`
2. Aba **Custom domains** → **Set up a custom domain**
3. Digite: `zambomnetwork.com.br` → **Continue**
4. A Cloudflare detecta que o domínio já está na conta e adiciona o registro DNS automaticamente
5. Repita para `www.zambomnetwork.com.br` (a Cloudflare adiciona um CNAME apontando para o apex)
6. Em 1–5 minutos, o domínio fica ativo com HTTPS automático

> Como o domínio já está na Cloudflare, **nenhum registro DNS manual é necessário**. A integração é automática.

### Verificar SSL/TLS

1. Em **SSL/TLS → Overview**, garanta que está em **Full** ou **Full (strict)**
2. **NÃO** use "Flexible" — pode causar loops de redirect

---

## Passo 5 — Pós-deploy

### Testes finais
- https://zambomnetwork.com.br → carrega com cadeado verde
- https://www.zambomnetwork.com.br → redireciona para apex (sem www)
- https://zambomnetwork.com.br/sitemap.xml → exibe XML
- https://zambomnetwork.com.br/robots.txt → exibe texto
- https://zambomnetwork.com.br/url-inventada → mostra a página 404 estilizada

### Validadores recomendados (gratuitos)
- **Rich Results Test** (valida JSON-LD): https://search.google.com/test/rich-results
- **Open Graph Debugger** (preview WhatsApp/Facebook): https://developers.facebook.com/tools/debug/
- *