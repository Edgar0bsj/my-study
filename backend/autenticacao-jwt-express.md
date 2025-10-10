# ↪︎ Proteção de Rotas com Middleware – Express.js

- [↪︎ Proteção de Rotas com Middleware – Express.js](#︎-proteção-de-rotas-com-middleware--expressjs)
	- [🔄 Fluxo de Funcionamento](#-fluxo-de-funcionamento)
	- [🧩 Exemplo de Implementação](#-exemplo-de-implementação)
		- [1. Criar o middleware (`authMiddleware.js`):](#1-criar-o-middleware-authmiddlewarejs)
		- [2. Usar o middleware em rotas protegidas:](#2-usar-o-middleware-em-rotas-protegidas)
	- [✅ Observações](#-observações)

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
