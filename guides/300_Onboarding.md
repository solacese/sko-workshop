# Stage 2: Onboarding — Provision Access

<img src="img/onboarding.png" alt="Onboarding" width="400"/>

Once the role is defined, connect the agent to the systems and data sources it needs to perform it. This is the direct analog to provisioning a new employee with tools, credentials, and access rights on day one. For AI agents, onboarding covers connectivity to enterprise databases via SQL, enterprise applications via APIs, MCP servers, and third-party agents via A2A protocols, as well as defining how the agent is triggered (chat, event, API call, or from another agent). Getting onboarding right means providing the right access: role-based access controls, SSO, and delegated identity ensure each agent operates only within the permissions appropriate to its defined function.

---

## Solace Agent Mesh Features used in the onboarding stage

- **SQL Connector** — Provides agents with query access to relational databases (PostgreSQL, MySQL, MariaDB).
- **MCP Connector** — Connects agents to any Model Context Protocol server via streamable-HTTP or SSE transport, exposing its tools natively to the agent.
- **API Connector (OpenAPI)** — Connects agents to any REST API described by an OpenAPI spec, with auto-detected authentication.
- **Knowledge Base Connector** — Wires agents to a managed vector knowledge base (Amazon Bedrock KB) for retrieval-augmented generation.
- **MCP Tools** — Tools exposed by external MCP servers (stdio, SSE, or streamable-HTTP) attached to an agent and discoverable at runtime.
- **HTTP SSE Gateway** — The platform-managed chat-and-streaming gateway that exposes agents to users via Server-Sent Events; handles session management, auth, and response streaming.
- **Event Mesh Gateway** — An event-driven connector that subscribes to Solace broker topics and routes real-world events (webhooks, IoT, queue messages) into agent tasks.
- **Proxy (`kind: proxy`)** — Connects external A2A-over-HTTPS agents into the mesh; handles agent card fetching, auth (static bearer / API key / OAuth 2.0), and artifact format translation.
- **RBAC (`authorization_service: default_rbac`)** — Role-based access control using YAML-defined roles and users; enforced at tool dispatch, peer delegation, and control-plane operations.
- **IdP config (`idp_claims_config`)** — Maps identity-provider group claims (e.g., OIDC `groups`) to SAM roles at authentication time, enabling SSO-driven access provisioning.
- **Session Memory (`session_service`)** — Configurable persistence backend (in-memory, SQLite, PostgreSQL) that stores conversation history and task checkpoints per agent.

## Hands-on

Now that your agents have defined roles, it is time to give them access to the data they need. In this exercise you will provision a PostgreSQL connector backed by a Supabase database and wire it into your manifest. This is the onboarding equivalent of handing a new employee their system credentials on day one.

---

## Step 1 — Add the database password to your .env

The connector config will reference the password as an environment variable, not as a plain-text value. 


1. Create a .env file with the following basic vars
    ```
    # LLM — LiteLLM proxy at mymaas.net
    LLM_SERVICE_ENDPOINT="https://lite-llm.mymaas.net/"
    MODEL_GENERAL_API_KEY="sk-<>"
    MODEL_PLANNING_API_KEY="sk-<>"
    ```
    > Note: You can just copy the example env if you cloned this repo `cp .env_example .env`
1. Update the necessary vars

Open your `.env` file and add the following line:

```
DB_PASSWORD=thisisaverysecurepassword
```

---

## Step 2 — Create the PostgreSQL connector

Copy and paste the following prompt into Claude Code:

```
Create a PostgreSQL connector for my SAM project. Use the following connection string:

  postgresql://postgres.hllufzsytafdylpscgif:${DB_PASSWORD}@aws-1-ap-northeast-1.pooler.supabase.com:5432/postgres

The password should come from the environment variable DB_PASSWORD. Add the connector to my manifest and apply.
```

---

## What just happened

Claude Code generated a `kind: connector` YAML of subtype `sql/postgres` and wired it into your manifest. A few things worth noting:

- **The password is never in the YAML.** The connector references `${DB_PASSWORD}`, which SAM resolves from the environment at runtime. This pattern keeps credentials out of version control and is the correct pattern for all secrets in SAM.
- **The connector is now a named resource on the platform.** Your agents can be given access to it by referencing it in their toolset configuration in the next steps.
- **Least privilege starts here.** The connector exposes a SQL query interface — agents that are wired to it can only read what the database user permits. Nothing broader.

---

## Step 3 — Create an agent that uses the connector

With the connector live, wire up a dedicated database agent. Copy and paste the following prompt into Claude Code:

```
Create an agent called "store-data" that uses the postgres connector we just created. The agent should be a data specialist focused on querying and retrieving store data from the database. Add it to the manifest and apply.
```

---

## What just happened

A new agent was created with the PostgreSQL connector attached to its toolset. This agent can now issue SQL queries against the database as part of its reasoning loop — no separate integration code required. The connector is the only bridge between the agent and the database; the agent never holds credentials directly.

---

When the apply completes, the Onboarding stage is done. Your agents now have a live data connection they can query. The next stage — [Coaching](./400_Coaching.md) — is where you validate that they use it correctly before promoting to production.

