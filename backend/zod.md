# **↪︎ 👨‍💻 Validação de Dados com Zod**

- [**↪︎ 👨‍💻 Validação de Dados com Zod**](#︎--validação-de-dados-com-zod)
  - [⚙️ **Fluxo de Funcionamento**](#️-fluxo-de-funcionamento)
  - [📦 **1. Instalar o Zod**](#-1-instalar-o-zod)
  - [🧱 **2. Criar um Schema de Validação**](#-2-criar-um-schema-de-validação)
  - [🧪 **3. Validar Dados**](#-3-validar-dados)
    - [✅ `parse()` → lança erro se inválido](#-parse--lança-erro-se-inválido)
    - [⚠️ `safeParse()` → não lança erro, retorna sucesso/falha](#️-safeparse--não-lança-erro-retorna-sucessofalha)
  - [🧩 **4. Lidando com Erros**](#-4-lidando-com-erros)
  - [🧠 **5. Inferindo Tipos com TypeScript**](#-5-inferindo-tipos-com-typescript)
  - [🚦 **6. Integração com Express (Exemplo Prático)**](#-6-integração-com-express-exemplo-prático)
  - [🧭 **Resumo do Fluxo**](#-resumo-do-fluxo)
  - [💡 **Dicas Extras**](#-dicas-extras)
  - [🧾 **Exemplo Completo**](#-exemplo-completo)

## ⚙️ **Fluxo de Funcionamento**

1. Instalar o Zod
2. Criar schemas de validação
3. Validar dados recebidos
4. Tratar erros de validação
5. (Opcional) Integrar com Express, APIs ou formulários
6. (Opcional) Inferir tipos TypeScript a partir do schema

---

## 📦 **1. Instalar o Zod**

```bash
npm install zod
```

> O Zod é 100% escrito em TypeScript — não requer dependências adicionais.

---

## 🧱 **2. Criar um Schema de Validação**

Um **schema** define a estrutura e as regras que um objeto deve seguir.

```ts
// src/validation/userSchema.ts
import { z } from "zod";

export const userSchema = z.object({
  name: z.string().min(3, "O nome precisa ter no mínimo 3 caracteres"),
  email: z.string().email("E-mail inválido"),
  age: z.number().int().min(18, "Idade mínima é 18 anos"),
  password: z.string().min(6, "A senha precisa ter no mínimo 6 caracteres"),
});
```

> Cada campo possui **validações encadeadas** e **mensagens personalizadas**.
> A estrutura resultante é um objeto fortemente tipado e reutilizável em todo o projeto.

---

## 🧪 **3. Validar Dados**

Existem três formas principais de validação:

### ✅ `parse()` → lança erro se inválido

```ts
import { userSchema } from "./validation/userSchema.js";

const user = userSchema.parse({
  name: "Edgar",
  email: "edgar@email.com",
  age: 25,
  password: "123456",
});

console.log(user); // Dados validados e tipados
```

### ⚠️ `safeParse()` → não lança erro, retorna sucesso/falha

```ts
const result = userSchema.safeParse({
  name: "Ed",
  email: "teste",
  age: 15,
  password: "123",
});

if (!result.success) {
  console.error("❌ Erros de validação:", result.error.format());
} else {
  console.log("✅ Dados válidos:", result.data);
}
```

> Ideal para uso em **APIs**, onde o erro precisa ser tratado e retornado ao cliente.

---

## 🧩 **4. Lidando com Erros**

O Zod fornece detalhes claros sobre cada campo inválido.

```ts
try {
  Player.parse({ username: 42, xp: "100" });
} catch (err) {
  if (err instanceof z.ZodError) {
    err.issues;
    /* [
      {
        expected: 'string',
        code: 'invalid_type',
        path: [ 'username' ],
        message: 'Invalid input: expected string'
      },
      {
        expected: 'number',
        code: 'invalid_type',
        path: [ 'xp' ],
        message: 'Invalid input: expected number'
      }
    ] */
  }
}
```

Saída:

```json
[
  { "path": ["name"], "message": "O nome precisa ter no mínimo 3 caracteres" },
  { "path": ["email"], "message": "E-mail inválido" },
  { "path": ["age"], "message": "Idade mínima é 18 anos" },
  {
    "path": ["password"],
    "message": "A senha precisa ter no mínimo 6 caracteres"
  }
]
```

---

## 🧠 **5. Inferindo Tipos com TypeScript**

Uma das maiores vantagens do Zod é poder **gerar tipos automaticamente** a partir do schema.

```ts
import { z } from "zod";

const productSchema = z.object({
  name: z.string(),
  price: z.number().positive(),
});

type Product = z.infer<typeof productSchema>;

// Uso tipado:
const newProduct: Product = { name: "Teclado", price: 200 };
```

> Assim, se você mudar o schema, o **tipo TypeScript se atualiza automaticamente**, evitando inconsistências entre código e validação.

---

## 🚦 **6. Integração com Express (Exemplo Prático)**

Em projetos de API, a validação geralmente ocorre nos **dados da requisição** (`req.body`).

```ts
// src/middleware/validate.ts
import type { Request, Response, NextFunction } from "express";
import { ZodSchema, ZodError } from "zod";

export function validate(schema: ZodSchema) {
  return (req: Request, res: Response, next: NextFunction) => {
    try {
      schema.parse(req.body);
      next();
    } catch (error) {
      if (error instanceof ZodError) {
        return res.status(400).json({
          error: "Erro de validação",
          details: error.errors.map((err) => ({
            field: err.path.join("."),
            message: err.message,
          })),
        });
      }
      next(error);
    }
  };
}
```

Uso na rota:

```ts
// src/routes/userRoutes.ts
import express from "express";
import { userSchema } from "../validation/userSchema.js";
import { validate } from "../middleware/validate.js";

const router = express.Router();

router.post("/users", validate(userSchema), (req, res) => {
  res.status(201).json({ message: "Usuário criado com sucesso!" });
});

export default router;
```

---

## 🧭 **Resumo do Fluxo**

| Etapa | Ação              | Descrição                                  |
| ----- | ----------------- | ------------------------------------------ |
| 1     | `npm install zod` | Instala a biblioteca                       |
| 2     | Criar schema      | Define as regras de validação              |
| 3     | Validar dados     | Usa `parse()` ou `safeParse()`             |
| 4     | Tratar erros      | Usa `ZodError` para mensagens claras       |
| 5     | Inferir tipos     | Usa `z.infer<typeof schema>`               |
| 6     | Integrar          | Middleware para validação em rotas Express |

---

## 💡 **Dicas Extras**

- 🧱 **Organização sugerida de pastas:**

  ```
  src/
  ├── validation/
  │   ├── userSchema.ts
  │   ├── productSchema.ts
  │   └── ...
  ├── middleware/
  │   └── validate.ts
  ├── routes/
  └── index.ts
  ```

- 🔄 **Validação parcial:**

  ```ts
  userSchema.partial(); // Torna todos os campos opcionais
  ```

- 🧩 **Extensão de schemas:**

  ```ts
  const adminSchema = userSchema.extend({
    role: z.literal("admin"),
  });
  ```

- 🔍 **Combinação de múltiplos schemas:**

  ```ts
  const combined = z.intersection(schemaA, schemaB);
  ```

---

## 🧾 **Exemplo Completo**

```ts
// src/validation/userSchema.ts
import { z } from "zod";

export const userSchema = z.object({
  name: z.string().min(3, "Nome muito curto"),
  email: z.string().email("E-mail inválido"),
  password: z.string().min(6, "Senha muito curta"),
  age: z.number().min(18, "Idade mínima é 18 anos"),
});

export type User = z.infer<typeof userSchema>;
```

```ts
// src/index.ts
import { userSchema } from "./validation/userSchema.js";

const input = {
  name: "Edgar",
  email: "edgar@email.com",
  password: "123456",
  age: 25,
};

const result = userSchema.safeParse(input);

if (result.success) {
  console.log("✅ Dados válidos:", result.data);
} else {
  console.log("❌ Erros:", result.error.flatten().fieldErrors);
}
```
