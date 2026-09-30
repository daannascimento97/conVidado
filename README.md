# ConVidado

Banco de tempo para cuidado mutuo entre idosos. Projeto pronto para abrir no
VS Code: backend em Node.js + Express, banco de dados PostgreSQL, e um
frontend simples (HTML/CSS/JS puro, sem build).

## Estrutura de pastas

```
tempojunto/
├── backend/              API em Node.js
│   ├── src/
│   │   ├── server.js     ponto de entrada
│   │   ├── db.js         conexao com o PostgreSQL
│   │   ├── seed.js       cria o primeiro administrador
│   │   ├── routes/       rotas da API (auth, users, offers, transfers, admin)
│   │   ├── middleware/   verificacao de login e de permissao de admin
│   │   └── scripts/
│   │       └── setup-db.js   cria as tabelas e categorias
│   ├── package.json
│   └── .env.example      modelo do arquivo de configuracao
├── database/
│   ├── schema.sql        estrutura das tabelas
│   └── seed.sql          categorias padrao
└── frontend/
    ├── index.html        app principal (login, inicio, buscar, transferir, perfil)
    ├── admin.html         painel de administracao
    ├── css/style.css
    └── js/ (app.js, admin.js)
```

O backend serve o frontend automaticamente, entao no final voce so precisa
rodar um servidor (`npm start`) e abrir o navegador.

---

## Parte 1 - Instalar e configurar o PostgreSQL

Se voce nunca usou PostgreSQL, siga esta parte com calma. Voce so faz isso
uma vez.

### 1.1 Instalar o PostgreSQL

**Windows**
1. Baixe o instalador em https://www.postgresql.org/download/windows/
2. Rode o instalador. Quando ele pedir uma senha para o usuario `postgres`,
   escolha uma e anote em um lugar seguro - voce vai usar em alguns minutos.
3. Deixe a porta padrao (5432).
4. No fim da instalacao, o "Stack Builder" pode abrir - pode fechar, nao e
   necessario para este projeto.

**macOS**
- Opcao mais simples: baixe o **Postgres.app** em https://postgresapp.com,
  arraste para a pasta Aplicativos e abra. Ele ja sobe o banco rodando.
- Ou, se usa Homebrew: `brew install postgresql@16` e depois
  `brew services start postgresql@16`.

**Linux (Ubuntu/Debian)**
```
sudo apt update
sudo apt install postgresql postgresql-contrib
sudo systemctl start postgresql
```

### 1.2 Criar o banco de dados e o usuario do app

Voce vai criar um usuario proprio para o ConVidado (em vez de usar o
usuario `postgres` principal, o que e mais seguro).

Abra o terminal do PostgreSQL (`psql`):

- **Windows**: procure "SQL Shell (psql)" no menu iniciar e abra. Ele vai
  perguntar servidor, banco, porta e usuario - pode apertar Enter em tudo
  ate pedir a senha (a que voce criou na instalacao).
- **macOS/Linux**: no terminal comum, digite `psql postgres` (no Linux pode
  precisar de `sudo -u postgres psql`).

Dentro do `psql`, cole estes comandos um de cada vez (troque `senha_forte`
por uma senha sua):

```sql
CREATE USER convidado_user WITH PASSWORD 'senha_forte';
CREATE DATABASE convidado OWNER convidado_user;
GRANT ALL PRIVILEGES ON DATABASE convidado TO convidado_user;
```

Depois digite `\q` e Enter para sair do psql.

O que voce acabou de fazer: criou um "cofre" vazio chamado `convidado` e
uma chave (`convidado_user` + senha) que so abre esse cofre - o app nunca
vai usar o usuario principal do banco.

### 1.3 Guardar a conexao no arquivo .env

Dentro da pasta `backend`, copie o arquivo de exemplo:

```
cp .env.example .env
```

(no Windows, pode copiar e renomear pelo Explorador de Arquivos mesmo)

Abra o `.env` no VS Code e ajuste a linha `DATABASE_URL` com o usuario e a
senha que voce criou:

```
DATABASE_URL=postgresql://convidado_user:senha_forte@localhost:5432/convidado
```

Tambem troque `JWT_SECRET` por qualquer frase longa e unica, e defina o
email/senha do primeiro administrador em `ADMIN_EMAIL` / `ADMIN_PASSWORD`.

---

## Parte 2 - Rodar o projeto

Com o PostgreSQL instalado e o `.env` preenchido:

```
cd backend
npm install
npm run db:setup
npm run seed:admin
npm start
```

O que cada comando faz:
- `npm install` - baixa as bibliotecas usadas pelo backend.
- `npm run db:setup` - cria as tabelas dentro do banco `convidado` e
  insere as categorias padrao (Transporte, Companhia, Tecnologia...).
- `npm run seed:admin` - cria o primeiro usuario administrador, usando os
  dados que voce colocou em `ADMIN_EMAIL` e `ADMIN_PASSWORD` no `.env`.
  Pode rodar de novo a qualquer momento sem problema.
- `npm start` - liga o servidor.

Agora abra no navegador:
- **App para os usuarios**: http://localhost:8888
- **Painel de administracao**: http://localhost:8888/admin.html (entre com
  o email e senha que voce definiu como administrador)

---

## Como incluir novos administradores

Ha dois jeitos:

1. **Pelo painel** (mais simples): entre em `/admin.html` com uma conta que
   ja seja administradora, encontre a pessoa na tabela de usuarios e clique
   em "Tornar admin".
2. **Pelo terminal**: mude `ADMIN_EMAIL` no `.env` para o email da pessoa e
   rode `npm run seed:admin` de novo - se o email ja existir, ele so marca
   essa conta como administradora.

---

## Principais rotas da API

| Metodo | Rota                        | O que faz                                   |
|--------|-----------------------------|----------------------------------------------|
| POST   | /api/auth/register          | Cria uma conta                                |
| POST   | /api/auth/login             | Entra e recebe o token de sessao              |
| GET    | /api/users/me                | Dados do usuario logado                       |
| GET    | /api/offers                  | Lista ofertas de ajuda (filtra por categoria) |
| POST   | /api/offers                  | Publica uma nova oferta                       |
| POST   | /api/transfers                | Transfere horas para outra pessoa             |
| GET    | /api/transfers/extrato        | Historico de horas da pessoa logada           |
| GET    | /api/admin/users              | (admin) Lista todos os usuarios               |
| PATCH  | /api/admin/users/:id/role     | (admin) Promove ou remove administrador       |

---

## Proximos passos sugeridos

Este projeto e um alicerce funcional, nao uma versao pronta para producao.
Antes de lancar para usuarios de verdade, vale:
- Trocar o `JWT_SECRET` e as senhas do `.env` por valores fortes e unicos.
- Colocar o site atras de HTTPS (por exemplo hospedando em um servico como
  Render, Railway ou uma VPS com Nginx + Certbot).
- Adicionar recuperacao de senha por email.
- Pensar em um processo real de verificacao de identidade antes de liberar
  o selo "verificado" (hoje qualquer administrador pode marcar manualmente).
