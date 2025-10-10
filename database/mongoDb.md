# 🍃 **Banco de Dados MongoDB**

- [🍃 **Banco de Dados MongoDB**](#-banco-de-dados-mongodb)
	- [⚙️ **Fluxo de Funcionamento**](#️-fluxo-de-funcionamento)
	- [📦 **1. Instalar Dependências**](#-1-instalar-dependências)
	- [🌱 **2. Configurar o arquivo `.env`**](#-2-configurar-o-arquivo-env)
	- [🔧 **3. Criar o arquivo de conexão**](#-3-criar-o-arquivo-de-conexão)
	- [🧩 **4. Criar um Schema e Model**](#-4-criar-um-schema-e-model)
	- [⚙️ **5. Executar Operações CRUD**](#️-5-executar-operações-crud)
		- [🟢 Criar um usuário](#-criar-um-usuário)
		- [🔵 Buscar usuários](#-buscar-usuários)
		- [🟡 Atualizar usuário](#-atualizar-usuário)
		- [🔴 Deletar usuário](#-deletar-usuário)
	- [🧠 **6. Integrando no Servidor**](#-6-integrando-no-servidor)
	- [🧭 **Resumo do Fluxo**](#-resumo-do-fluxo)
	- [💡 **Boas Práticas**](#-boas-práticas)
	- [🧾 **Resumo Geral**](#-resumo-geral)
- [# Schema Types](#-schema-types)
	- [SchemaType Options](#schematype-options)
	- [🧵 Tipo: String](#-tipo-string)
	- [🔢 Tipo: Number](#-tipo-number)
	- [📅 Tipo: Date](#-tipo-date)
	- [🆔 Tipo: ObjectId](#-tipo-objectid)
	- [📌 Indexes (Índices do MongoDB)](#-indexes-índices-do-mongodb)
	- [🧩 Todos os Tipos de Schema](#-todos-os-tipos-de-schema)
- [Relação entre documentos](#relação-entre-documentos)
	- [🔗 Relação "Um para Muitos"](#-relação-um-para-muitos)
		- [🧱 Schema do Usuário](#-schema-do-usuário)
		- [🧱 Schema do Post](#-schema-do-post)
		- [🔍 Buscar todos os posts com dados do autor](#-buscar-todos-os-posts-com-dados-do-autor)
	- [🔗 Relação "Muitos para Muitos"](#-relação-muitos-para-muitos)
		- [🧱 Schema do Aluno](#-schema-do-aluno)
		- [🧱 Schema da Turma](#-schema-da-turma)
		- [🔍 Buscar uma turma com os dados dos alunos](#-buscar-uma-turma-com-os-dados-dos-alunos)

## ⚙️ **Fluxo de Funcionamento**

1. Instalar dependências
2. Criar e configurar o ambiente `.env`
3. Conectar o projeto ao banco com `mongoose`
4. Criar um schema e model
5. Executar operações CRUD
6. Tratar erros e mensagens de conexão

---

## 📦 **1. Instalar Dependências**

O **Mongoose** é a biblioteca oficial para trabalhar com MongoDB em Node.js, fornecendo uma camada de abstração baseada em **schemas e models**.

```bash
npm install mongoose
npm install @types/mongoose --save-dev
```

---

## 🌱 **2. Configurar o arquivo `.env`**

Adicione as credenciais do banco no `.env`:

```env
DB_URI=mongodb+srv://usuario:senha@cluster.exemplo.mongodb.net/meu_banco
```

> ⚠️ **Importante:**
> O `.env` **nunca deve ser versionado**, pois contém credenciais sensíveis.
> Lembre-se de adicionar ao `.gitignore`:
>
> ```
> .env
> ```

---

## 🔧 **3. Criar o arquivo de conexão**

Estrutura recomendada:

```
src/
├── config/
│   └── database.ts
├── model/
│   └── userModel.ts
├── index.ts
└── ...
```

Arquivo de conexão:

```ts
// src/config/database.ts
import mongoose from "mongoose";
import env from "./env.js"; // Arquivo que carrega o .env

export async function connectDB(): Promise<void> {
  try {
    await mongoose.connect(env.DB_URI);
    console.log("🍃 [MongoDB] Conectado com sucesso!");
  } catch (error) {
    console.error("❌ [MongoDB] Erro na conexão:", error);
    process.exit(1);
  }
}
```

> Essa função pode ser importada e chamada no `index.ts` principal do servidor.

---

## 🧩 **4. Criar um Schema e Model**

O **schema** define a estrutura dos documentos (como uma "tabela"), enquanto o **model** representa essa estrutura dentro da aplicação.

```ts
// src/model/userModel.ts
import mongoose, { Schema, Document } from "mongoose";

export interface IUser extends Document {
  name: string;
  email: string;
  password: string;
  createdAt: Date;
}

const userSchema = new Schema<IUser>({
  name: { type: String, required: [true, "O nome é obrigatório"] },
  email: {
    type: String,
    required: [true, "O e-mail é obrigatório"],
    unique: true,
  },
  password: { type: String, required: [true, "A senha é obrigatória"] },
  createdAt: { type: Date, default: Date.now },
});

export default mongoose.model<IUser>("User", userSchema);
```

> Cada campo do schema pode ter **validações, tipos, valores padrão** e **mensagens personalizadas**.

> O Segunda parametro do **Schema** recebe algumas opções dentro de um objeto, e as mais importante são:

```ts
{
timestamps: true, // adiciona createdAt e updatedAt automaticamente
versionKey: false, // remove o campo __v
}
```

---

## ⚙️ **5. Executar Operações CRUD**

### 🟢 Criar um usuário

```ts
import User from "./model/userModel.js";

const novoUsuario = await User.create({
  name: "Edgar",
  email: "edgar@email.com",
  password: "123456",
});

console.log("✅ Usuário criado:", novoUsuario);
```

---

### 🔵 Buscar usuários

```ts
const usuarios = await User.find();
console.log("📦 Lista de usuários:", usuarios);
```

Buscar por ID:

```ts
const usuario = await User.findById("652b12...");
```

---

### 🟡 Atualizar usuário

```ts
await User.findByIdAndUpdate(
  "652b12...",
  { name: "Edgar Atualizado" },
  { new: true }
);
```

> `{ new: true }` retorna o documento atualizado.

---

### 🔴 Deletar usuário

```ts
await User.findByIdAndDelete("652b12...");
console.log("🗑️ Usuário removido!");
```

---

## 🧠 **6. Integrando no Servidor**

```ts
// src/index.ts
import express from "express";
import { connectDB } from "./config/database.js";

const app = express();
app.use(express.json());

// Conecta ao banco
connectDB();

app.get("/", (req, res) => {
  res.send("🚀 API rodando com MongoDB!");
});

app.listen(3000, () => console.log("Servidor ativo na porta 3000"));
```

---

## 🧭 **Resumo do Fluxo**

| Etapa | Descrição                | Arquivo / Comando                            |
| ----- | ------------------------ | -------------------------------------------- |
| 1     | Instalar Mongoose        | `npm install mongoose`                       |
| 2     | Configurar URI no `.env` | `.env`                                       |
| 3     | Criar função de conexão  | `src/config/database.ts`                     |
| 4     | Definir schema e model   | `src/model/userModel.ts`                     |
| 5     | Usar CRUD                | Métodos `create`, `find`, `update`, `delete` |
| 6     | Integrar com servidor    | `src/index.ts`                               |

---

## 💡 **Boas Práticas**

1. ✅ **Usar variáveis de ambiente** para credenciais.
2. 🧱 **Centralizar a conexão** do banco em `config/database.ts`.
3. 🧩 **Separar models por entidade** (`userModel.ts`, `productModel.ts`, etc.).
4. ⚙️ **Criar interfaces TypeScript** para cada model, garantindo tipagem forte.
5. 🚨 **Tratar erros de conexão** com `try/catch` e logs claros.
6. 🔄 **Evitar desconexões forçadas**, usando `mongoose.connect` apenas uma vez na inicialização.

---

## 🧾 **Resumo Geral**

| Conceito     | Explicação                                                         |
| ------------ | ------------------------------------------------------------------ |
| **MongoDB**  | Banco NoSQL baseado em documentos JSON                             |
| **Mongoose** | Abstração para modelar e validar dados                             |
| **Schema**   | Estrutura que define os campos e regras de um documento            |
| **Model**    | Interface entre a aplicação e a coleção no MongoDB                 |
| **CRUD**     | Create, Read, Update, Delete                                       |
| **Conexão**  | Feita uma única vez, preferencialmente antes de iniciar o servidor |

---

# # Schema Types

1. String
2. Number
3. Date
4. Buffer
5. Boolean
6. Mixed
7. ObjectId
8. Array
9. Decimal128
10. Map
11. Schema
12. UUID
13. BigInt
14. Double
15. Int32

---

## SchemaType Options

## 🧵 Tipo: String

```js
lowercase: true // Converte o texto para letras minúsculas automaticamente
uppercase: true // Converte o texto para letras maiúsculas automaticamente
trim: true      // Remove espaços extras no início e fim do texto
match: /abc/    // Verifica se o texto segue um padrão (expressão regular)
enum: ['A', 'B'] // Só aceita valores que estejam na lista fornecida
minLength: 3    // O texto precisa ter no mínimo 3 caracteres
maxLength: 10   // O texto pode ter no máximo 10 caracteres
populate: {...} // Define como preencher dados relacionados automaticamente
```

---

## 🔢 Tipo: Number

```js
min: 1          // O número precisa ser no mínimo 1
max: 100        // O número pode ser no máximo 100
enum: [10, 20]  // Só aceita os números exatos da lista
populate: {...} // Define como preencher dados relacionados automaticamente
```

---

## 📅 Tipo: Date

```js
min: "2023-01-01"; // A data precisa ser igual ou depois dessa
max: "2025-12-31"; // A data precisa ser igual ou antes dessa
expires: 3600; // Expira após 3600 segundos (TTL - tempo de vida)
```

---

## 🆔 Tipo: ObjectId

```js
populate: {...} // Define como preencher dados relacionados automaticamente
```

---

## 📌 Indexes (Índices do MongoDB)

```js
index: true; // Cria um índice para facilitar buscas
unique: true; // Garante que o valor seja único no banco
sparse: true; // Cria índice apenas para documentos que têm esse campo
```

---

## 🧩 Todos os Tipos de Schema

```js
required: true        // Torna o campo obrigatório
default: 'valor'      // Define um valor padrão se nenhum for informado
select: false         // Oculta o campo por padrão nas consultas
validate: fn          // Função personalizada para validar o valor
get: fn               // Função que modifica o valor ao ler
set: fn               // Função que modifica o valor ao salvar
alias: 'apelido'      // Cria um nome alternativo para o campo
immutable: true       // Impede que o valor seja alterado depois de criado
transform: fn         // Modifica o valor ao converter para JSON
```

---

# Relação entre documentos

## 🔗 Relação "Um para Muitos"

Imagine que um **usuário pode ter vários posts**. Cada post pertence a um único usuário.

### 🧱 Schema do Usuário

```js
const UsuarioSchema = new mongoose.Schema({
  nome: String,
});
```

### 🧱 Schema do Post

```js
const PostSchema = new mongoose.Schema({
  titulo: String,
  autor: {
    type: mongoose.Schema.Types.ObjectId, // Guarda o ID do usuário
    ref: "Usuario", // Refere ao modelo 'Usuario'
  },
});
```

### 🔍 Buscar todos os posts com dados do autor

```js
Post.find().populate("autor"); // Traz os dados completos do autor junto com os posts
```

---

## 🔗 Relação "Muitos para Muitos"

Imagine que **alunos podem estar em várias turmas** e **turmas podem ter vários alunos**.

### 🧱 Schema do Aluno

```js
const AlunoSchema = new mongoose.Schema({
  nome: String,
  turmas: [
    {
      type: mongoose.Schema.Types.ObjectId, // Lista de IDs de turmas
      ref: "Turma",
    },
  ],
});
```

### 🧱 Schema da Turma

```js
const TurmaSchema = new mongoose.Schema({
  nome: String,
  alunos: [
    {
      type: mongoose.Schema.Types.ObjectId, // Lista de IDs de alunos
      ref: "Aluno",
    },
  ],
});
```

### 🔍 Buscar uma turma com os dados dos alunos

```js
Turma.find().populate("alunos"); // Traz os dados completos dos alunos da turma
```

---
