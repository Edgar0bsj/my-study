## **↪︎ 🌱 Variáveis de Ambiente**

- [**↪︎ 🌱 Variáveis de Ambiente**](#︎--variáveis-de-ambiente)
- [⚙️ **Fluxo de Funcionamento**](#️-fluxo-de-funcionamento)
- [🧩 **1. Instalar o pacote `dotenv`**](#-1-instalar-o-pacote-dotenv)
- [🗂️ **2. Criar o arquivo `.env`**](#️-2-criar-o-arquivo-env)
- [🔧 **3. Carregar as variáveis na aplicação**](#-3-carregar-as-variáveis-na-aplicação)
- [🧠 **4. Usando variáveis de ambiente**](#-4-usando-variáveis-de-ambiente)
- [🧱 **5. Tipagem e Validação (opcional)**](#-5-tipagem-e-validação-opcional)
- [🧩 **6. Exemplo de uso completo**](#-6-exemplo-de-uso-completo)
- [🧭 **Resumo do Fluxo**](#-resumo-do-fluxo)
- [💡 **Dicas Extras**](#-dicas-extras)

## ⚙️ **Fluxo de Funcionamento**

1. Criar o arquivo `.env`
2. Instalar e configurar o pacote `dotenv`
3. Carregar as variáveis de ambiente no início da aplicação
4. Usar as variáveis no código TypeScript
5. (Opcional) Tipar e validar variáveis para maior segurança

---

## 🧩 **1. Instalar o pacote `dotenv`**

O `dotenv` é o pacote responsável por ler o arquivo `.env` e carregar as variáveis no `process.env`.

```bash
npm install dotenv
npm install @types/dotenv --save-dev
```

---

## 🗂️ **2. Criar o arquivo `.env`**

Crie o arquivo na raiz do projeto:

```
meu-projeto-ts/
├── src/
├── .env
├── package.json
└── tsconfig.json
```

Dentro do arquivo `.env`, defina suas variáveis:

```env
# Banco de Dados
DB_URI=mongodb+srv://usuario:senha@cluster/db

# Servidor
PORT=4000

# Token
JWT_SECRET=meusegredo123
```

> ⚠️ **Nunca versionar o `.env`!**
> Sempre adicione ao `.gitignore` para evitar exposição de credenciais:

```
# .gitignore
node_modules/
dist/
.env
```

---

## 🔧 **3. Carregar as variáveis na aplicação**

Crie um arquivo de configuração — recomendado dentro de `src/config/env.ts`:

```ts
// src/config/env.ts
import { config } from "dotenv";

config(); // Carrega as variáveis do arquivo .env

export default {
  DB_URI: process.env.DB_URI as string,
  PORT: process.env.PORT ? Number(process.env.PORT) : 3000,
  JWT_SECRET: process.env.JWT_SECRET as string,
};
```

> Essa estrutura exporta um **objeto centralizado**, o que ajuda a manter a consistência e organização nas configurações do projeto.

---

## 🧠 **4. Usando variáveis de ambiente**

Em qualquer parte do código, basta importar:

```ts
// src/index.ts
import env from "./config/env.js";

console.log("Servidor rodando na porta:", env.PORT);
console.log("Conectando ao banco:", env.DB_URI);
```

---

## 🧱 **5. Tipagem e Validação (opcional)**

Para projetos maiores, você pode garantir que todas as variáveis estejam presentes usando uma biblioteca como `zod`:

```bash
npm install zod
```

E depois:

```ts
// src/config/env.ts
import { config } from "dotenv";
import { z } from "zod";

config();

const envSchema = z.object({
  DB_URI: z.string().url(),
  PORT: z.string().regex(/^\d+$/).default("3000"),
  JWT_SECRET: z.string().min(8),
});

const parsed = envSchema.safeParse(process.env);

if (!parsed.success) {
  console.error("❌ Erro nas variáveis de ambiente:");
  console.error(parsed.error.flatten().fieldErrors);
  process.exit(1);
}

export default {
  DB_URI: parsed.data.DB_URI,
  PORT: Number(parsed.data.PORT),
  JWT_SECRET: parsed.data.JWT_SECRET,
};
```

> 🔒 Assim, se alguma variável estiver ausente ou incorreta, o projeto **não inicia**, evitando falhas em produção.

---

## 🧩 **6. Exemplo de uso completo**

**Estrutura:**

```
meu-projeto-ts/
├── src/
│   ├── config/
│   │   └── env.ts
│   └── index.ts
├── .env
├── .gitignore
├── package.json
└── tsconfig.json
```

**Código:**

```ts
// src/index.ts
import env from "./config/env.js";

function startServer() {
  console.log("🌍 Servidor rodando na porta:", env.PORT);
  console.log("📦 Banco conectado em:", env.DB_URI);
}

startServer();
```

---

## 🧭 **Resumo do Fluxo**

| Etapa | Descrição                     | Arquivo / Comando                   |
| ----- | ----------------------------- | ----------------------------------- |
| 1     | Instalar dotenv               | `npm install dotenv`                |
| 2     | Criar `.env` com as variáveis | `.env`                              |
| 3     | Carregar variáveis no projeto | `src/config/env.ts`                 |
| 4     | Importar e usar as variáveis  | `import env from "./config/env.js"` |
| 5     | Tipar e validar (opcional)    | `zod`                               |

---

## 💡 **Dicas Extras**

- ✅ Sempre versionar um **exemplo de `.env`** para outros desenvolvedores:

  ```
  # .env.example
  DB_URI=
  PORT=
  JWT_SECRET=
  ```

- 🚀 Você pode criar **diferentes `.env`** para ambientes distintos:

  ```
  .env.development
  .env.production
  ```

  E carregar de forma condicional:

  ```ts
  import { config } from "dotenv";

  const envFile =
    process.env.NODE_ENV === "production"
      ? ".env.production"
      : ".env.development";
  config({ path: envFile });
  ```
