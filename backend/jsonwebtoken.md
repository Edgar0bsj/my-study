# ↪︎ Proteção de Rotas com Middleware – Express.js

- [↪︎ Proteção de Rotas com Middleware – Express.js](#︎-proteção-de-rotas-com-middleware--expressjs)
  - [🔄 Fluxo de Funcionamento](#-fluxo-de-funcionamento)
  - [🧩 Exemplo de Implementação](#-exemplo-de-implementação)
    - [1. Criar o middleware (`authMiddleware.js`):](#1-criar-o-middleware-authmiddlewarejs)
    - [2. Usar o middleware em rotas protegidas:](#2-usar-o-middleware-em-rotas-protegidas)
  - [✅ Observações](#-observações)
- [🔐 Criando Token e Extraindo Dados com JWT](#-criando-token-e-extraindo-dados-com-jwt)
  - [📦 Instalação do módulo](#-instalação-do-módulo)
  - [🧩 Importando o módulo](#-importando-o-módulo)
  - [Criando um Token](#criando-um-token)
  - [🔍 Extraindo Dados de um Token](#-extraindo-dados-de-um-token)
  - [✅ Exemplo Completo](#-exemplo-completo)

## 🔄 Fluxo de Funcionamento

1. O cliente envia uma requisição com o token JWT no cabeçalho `Authorization`.
2. O middleware intercepta a requisição antes que ela chegue à rota.
3. O token é extraído e validado com `jwt.verify`.
4. Se o token for válido, o middleware chama `next()` e a requisição continua.
5. Se o token for inválido ou ausente, o middleware retorna erro `401` ou `403`.

---

## 🧩 Exemplo de Implementação

### 1. Criar o middleware (`authMiddleware.js`):

```ts
const jwt = require("jsonwebtoken");
const SECRET = "minha_chave_secreta";

function verificarToken(req, res, next) {
  const authHeader = req.headers.authorization;
  if (!authHeader) return res.status(401).json({ message: "Token ausente" });

  const token = authHeader.split(" ")[1];
  try {
    const decoded = jwt.verify(token, SECRET);
    req.user = decoded; // salva os dados do usuário para uso posterior
    next(); // permite que a requisição continue
  } catch {
    res.status(403).json({ message: "Token inválido ou expirado" });
  }
}

module.exports = verificarToken;
```

### 2. Usar o middleware em rotas protegidas:

```ts
const express = require("express");
const verificarToken = require("./authMiddleware");
const router = express.Router();

// Rota protegida
router.get("/profile", verificarToken, (req, res) => {
  res.json({ message: `Bem-vindo, ${req.user.username}` });
});

// Outra rota protegida
router.post("/posts", verificarToken, (req, res) => {
  res.json({ message: `Post criado por ${req.user.username}` });
});
```

---

## ✅ Observações

- O token deve ser enviado no formato:
  ```
  Authorization: Bearer <seu_token>
  ```
- Você pode aplicar o middleware em **grupos de rotas** com `router.use(verificarToken)` para proteger todas as rotas abaixo.
- Em produção, armazene `SECRET` em variáveis de ambiente (`process.env.JWT_SECRET`).
- Pode adicionar verificação de **papel de usuário** (`admin`, `user`, etc) dentro do middleware usando `req.user.role`.

---

# 🔐 Criando Token e Extraindo Dados com JWT

## 📦 Instalação do módulo

Instale o **jsonwebtoken** e suas tipagens (caso use TypeScript):

```bash
npm install jsonwebtoken
npm install @types/jsonwebtoken -D
```

---

## 🧩 Importando o módulo

```ts
import jwt from "jsonwebtoken";
```

---

## Criando um Token

Use `jwt.sign()` para gerar o token.
O primeiro parâmetro representa os **dados** que serão armazenados dentro do token (payload).
O segundo é a **chave secreta** usada para assinar o token, e o terceiro é um objeto de **opções**, como o tempo de expiração.

```ts
const token = jwt.sign(
  { dados: "qualquer coisa" }, // payload
  JWT_SECRET, // chave secreta
  { expiresIn: "1h" } // expiração
);
```

> 💡 **Dica:** o campo `expiresIn` pode ser configurado com valores como `"1h"`, `"30m"`, `"7d"`, etc.

---

## 🔍 Extraindo Dados de um Token

Para decodificar e validar o token, use `jwt.verify()`.
Se o token for válido, a função retorna os dados originais do payload.

```ts
const decoded = jwt.verify(token, env.JWT_SECRET);
console.log(decoded); // { dados: "qualquer coisa", iat: ..., exp: ... }
```

> ⚠️ **Atenção:** se o token estiver expirado ou for inválido, `jwt.verify()` lançará um erro.
> Use um bloco `try/catch` para tratar esse caso.

---

## ✅ Exemplo Completo

```ts
import jwt from "jsonwebtoken";

const JWT_SECRET = "chave_super_secreta";

// Gerando token
const token = jwt.sign({ userId: 123 }, JWT_SECRET, { expiresIn: "1h" });

// Validando token
try {
  const decoded = jwt.verify(token, JWT_SECRET);
  console.log("Token válido:", decoded);
} catch (error) {
  console.error("Token inválido ou expirado");
}
```
