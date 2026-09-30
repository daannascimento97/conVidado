# conVidado

**Banco de tempo para pessoas 50+ em Sergipe.** Quem ajuda ganha horas; quem precisa usa essas horas. Cada 1 hora de ajuda dada vale 1 hora de ajuda recebida, sem dinheiro.

Projeto do grupo **conVidado** para o **1º MVP Tópicos Integradores – Silver Economy** (Edital nº 02/2026), curso de Análise e Desenvolvimento de Sistemas da **UNINASSAU Aracaju**.

🌐 **Landing page:** https://daannascimento97.github.io/conVidado/

---

## Equipe

| Nome | Papel |
| --- | --- |
| Daniel Nascimento Santos | Líder e Product Owner, back-end, front-end e IA |
| Lucas Silva Santos | Desenvolvimento e revisão de código |

## O problema

Muitas pessoas 50+ têm habilidades para oferecer (costura, consertos, companhia, ensino) e também precisam de ajuda no dia a dia, mas não têm uma rede de apoio organizada. Pedir ajuda por aplicativo costuma ser difícil para quem tem pouca prática com o celular.

## A solução

- **Carteira de horas:** cada troca registra 1 hora de crédito para quem ajudou e 1 hora de débito para quem recebeu.
- **Pedido por voz:** a pessoa fala o que precisa, sem preencher formulários.
- **IA de match:** um LLM organiza o pedido (categoria, urgência e duração) e sugere os 3 voluntários do bairro que mais combinam com ele.
- **Segurança:** cadastro conferido pela moderação, aviso a um familiar antes de cada encontro e avaliação após cada troca.

## Stack (item 7.2 do edital)

| Camada | Tecnologia |
| --- | --- |
| Front-end Web | React + Tailwind CSS |
| Front-end Mobile | React Native |
| Back-end | Java + Spring Boot + Hibernate (JPA), padrão MVC |
| Banco de dados | PostgreSQL |
| Inteligência Artificial | LLM (IA generativa) via API, chamada apenas pelo back-end |

## Arquitetura

```
Web (React) ──┐
              ├──> API REST (Spring Boot) ──> PostgreSQL
Mobile (RN) ──┘          │
                         └──> API do LLM
```

O back-end segue o padrão **MVC**, com os pacotes `model`, `repository`, `service`, `controller` e `dto`.

**Entidades principais:** `Usuario`, `Habilidade`, `UsuarioHabilidade`, `Bairro`, `Pedido`, `Servico`, `TransacaoHora`, `Avaliacao`.

**Endpoints da IA:**
- `POST /api/pedidos/interpretar`: transforma o texto do pedido em um JSON organizado.
- `GET /api/pedidos/{id}/sugestoes`: retorna os 3 voluntários sugeridos e o motivo de cada um.

## Roadmap

| Release | Período | Entregas |
| --- | --- | --- |
| R1 – Base da troca | Outubro de 2026 | Cadastro, perfil com habilidades, carteira de horas, pedir e oferecer ajuda, IA para organizar o pedido, telas Web |
| R2 – IA e acessibilidade | Até 18/11/2026 | Pedido por voz, IA de sugestão de voluntários, avaliação, aviso a familiar, painel admin, app Mobile |
| R3 – Escala comunitária | 2027 | Grupos por bairro, relatórios para parceiros, lembretes por WhatsApp |

## Como rodar (em construção)

### Pré-requisitos
- Java 17 ou superior
- Maven
- Node.js 18 ou superior (para o front-end React e React Native)
- PostgreSQL 15 ou superior

### Back-end
```bash
cd backend
cp .env.example .env      # preencha a conexão do banco e a chave da API do LLM
./mvnw spring-boot:run
```

### Front-end Web
```bash
cd web
npm install
npm run dev
```

> Senhas e chaves de API nunca vão para o repositório. Use sempre o arquivo `.env`, que já está no `.gitignore`.

## Governança do código

- **Branches:** `main` (estável e protegida), `develop` (integração) e `feature/<issue>-<nome>` para cada tarefa.
- **Commits:** padrão [Conventional Commits](https://www.conventionalcommits.org/pt-br/), em português. Exemplo: `feat: cria endpoint de pedidos`.
- **Pull requests:** todo código entra por pull request, com revisão de outro integrante e testes automáticos (GitHub Actions).
- **Dados:** coleta mínima e respeito à LGPD (Lei 13.709/2018). Dados enviados à IA são anonimizados.

## Documentação do projeto

- Lean Inception, Canvas do MVP, roadmap e Kanban: quadro do Miro do grupo.
- Documento de governança e planejamento da funcionalidade de IA: entregues aos professores na Avaliação Inicial.

---

Projeto acadêmico – UNINASSAU Aracaju, 2026.
