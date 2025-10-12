# **CONSUMO DE API COM AXIOS**

- [**CONSUMO DE API COM AXIOS**](#consumo-de-api-com-axios)
	- [⚙️ **Fluxo de Funcionamento**](#️-fluxo-de-funcionamento)
	- [⚙️ **Instalação e Configuração**](#️-instalação-e-configuração)
		- [1️⃣ Instalar Axios](#1️⃣-instalar-axios)
		- [2️⃣ Importar e usar](#2️⃣-importar-e-usar)
	- [🧩 **Exemplo Simples**](#-exemplo-simples)
	- [🧱 **Principais Métodos**](#-principais-métodos)
	- [⚙️ **Configuração Global (Instância Axios)**](#️-configuração-global-instância-axios)
		- [📄 `src/services/api.ts`](#-srcservicesapits)
		- [📄 Exemplo de uso:](#-exemplo-de-uso)
	- [🔑 **Autenticação com Token (Bearer)**](#-autenticação-com-token-bearer)
	- [⚙️ **Tratamento de Erros**](#️-tratamento-de-erros)
	- [⚡ **Exemplo Completo de CRUD com Axios**](#-exemplo-completo-de-crud-com-axios)
	- [🧠 **Interceptors (Requisições e Respostas)**](#-interceptors-requisições-e-respostas)
	- [🧠 **Resumo Rápido**](#-resumo-rápido)
	- [💡 **Boas Práticas**](#-boas-práticas)
	- [🧩 **Exemplo Prático com Variáveis de Ambiente**](#-exemplo-prático-com-variáveis-de-ambiente)
	- [⚙️ **Exemplo Final (Responsivo e Reutilizável)**](#️-exemplo-final-responsivo-e-reutilizável)

---

## ⚙️ **Fluxo de Funcionamento**

1. O Axios é usado para **enviar requisições** a um servidor HTTP (como uma API REST).
2. O servidor processa a requisição e **retorna uma resposta** (dados, mensagem, status, etc).
3. O Axios **resolve a promessa (`Promise`)** com o resultado da requisição.
4. É possível **tratar erros**, **configurar interceptadores**, e **usar instâncias globais** para projetos escaláveis.

---

## ⚙️ **Instalação e Configuração**

### 1️⃣ Instalar Axios

```bash
npm install axios
```

Se estiver usando TypeScript:

```bash
npm install -D @types/axios
```

### 2️⃣ Importar e usar

```js
import axios from "axios";
```

---

## 🧩 **Exemplo Simples**

```js
import axios from "axios";

async function getUsers() {
  try {
    const response = await axios.get(
      "https://jsonplaceholder.typicode.com/users"
    );
    console.log(response.data);
  } catch (error) {
    console.error("Erro ao buscar usuários:", error);
  }
}

getUsers();
```

---

## 🧱 **Principais Métodos**

| Método                  | Descrição         | Exemplo                                         |
| ----------------------- | ----------------- | ----------------------------------------------- |
| `axios.get(url)`        | Busca dados       | `axios.get("/users")`                           |
| `axios.post(url, data)` | Envia dados       | `axios.post("/users", { name: "Edgar" })`       |
| `axios.put(url, data)`  | Atualiza dados    | `axios.put("/users/1", { name: "Atualizado" })` |
| `axios.delete(url)`     | Deleta um recurso | `axios.delete("/users/1")`                      |

---

## ⚙️ **Configuração Global (Instância Axios)**

Crie uma **instância configurada** para usar em todo o projeto — ideal para APIs com autenticação e headers fixos.

### 📄 `src/services/api.ts`

```ts
import axios from "axios";

const api = axios.create({
  baseURL: "https://meu-servidor.com/api",
  timeout: 5000, // tempo limite para requisição
  headers: {
    "Content-Type": "application/json",
  },
});

export default api;
```

### 📄 Exemplo de uso:

```ts
import api from "./services/api";

async function getProducts() {
  const { data } = await api.get("/products");
  console.log(data);
}
```

---

## 🔑 **Autenticação com Token (Bearer)**

Para rotas protegidas, é comum incluir o token JWT no cabeçalho.

```ts
import api from "./services/api";

api.defaults.headers.common["Authorization"] = `Bearer ${token}`;
```

Ou dinamicamente com interceptadores:

```ts
api.interceptors.request.use((config) => {
  const token = localStorage.getItem("token");
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});
```

---

## ⚙️ **Tratamento de Erros**

```ts
try {
  const response = await api.get("/users");
  console.log(response.data);
} catch (error: any) {
  if (error.response) {
    console.error(
      "Erro de resposta:",
      error.response.status,
      error.response.data
    );
  } else if (error.request) {
    console.error("Sem resposta do servidor:", error.request);
  } else {
    console.error("Erro inesperado:", error.message);
  }
}
```

---

## ⚡ **Exemplo Completo de CRUD com Axios**

```ts
import api from "./services/api";

// CREATE
export async function createUser(data: any) {
  return await api.post("/users", data);
}

// READ
export async function getUsers() {
  return await api.get("/users");
}

// UPDATE
export async function updateUser(id: string, data: any) {
  return await api.put(`/users/${id}`, data);
}

// DELETE
export async function deleteUser(id: string) {
  return await api.delete(`/users/${id}`);
}
```

---

## 🧠 **Interceptors (Requisições e Respostas)**

Interceptadores permitem **manipular o fluxo de requisições** antes ou depois do envio.

```ts
api.interceptors.request.use(
  (config) => {
    console.log("🔹 Enviando requisição:", config.url);
    return config;
  },
  (error) => Promise.reject(error)
);

api.interceptors.response.use(
  (response) => response,
  (error) => {
    console.error("❌ Erro na resposta:", error.response?.status);
    return Promise.reject(error);
  }
);
```

---

## 🧠 **Resumo Rápido**

| Conceito                 | Descrição                                   |
| ------------------------ | ------------------------------------------- |
| `axios.create()`         | Cria uma instância com configurações padrão |
| `api.get('/url')`        | Busca dados                                 |
| `api.post('/url', data)` | Envia dados                                 |
| `api.interceptors`       | Manipula requisições e respostas            |
| `api.defaults.headers`   | Define cabeçalhos globais                   |
| `try/catch`              | Trata erros de rede ou servidor             |

---

## 💡 **Boas Práticas**

✅ Centralize todas as requisições em um único arquivo (`services/api.ts`).
✅ Use **async/await** com tratamento de erros (`try/catch`).
✅ Utilize **interceptadores** para token e logs automáticos.
✅ Mantenha o `baseURL` dinâmico com **variáveis de ambiente** (`process.env.API_URL`).
✅ Tipifique as respostas no TypeScript para maior segurança:

```ts
interface User {
  id: number;
  name: string;
  email: string;
}

const { data } = await api.get<User[]>("/users");
```

---

## 🧩 **Exemplo Prático com Variáveis de Ambiente**

📄 `.env`

```env
VITE_API_URL=https://api.meusistema.com
```

📄 `api.ts`

```ts
const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL,
});
```

---

## ⚙️ **Exemplo Final (Responsivo e Reutilizável)**

```ts
import api from "./services/api";

export async function getProfile() {
  try {
    const { data } = await api.get("/profile");
    return data;
  } catch (err) {
    console.error("Erro ao carregar perfil:", err);
  }
}
```

---
