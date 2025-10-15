# 🔒 Criptografar e Comparar Hash com BcryptJS

## 📦 Instalação do módulo

Instale o **bcryptjs**, responsável por gerar e comparar hashes de forma segura:

```bash
npm install bcryptjs
```

---

## 🧩 Importando o módulo

```ts
import bcrypt from "bcryptjs";
```

---

## 🔐 Criptografando uma Senha

Use `bcrypt.hash()` para gerar um **hash criptografado** da senha.
O segundo parâmetro (`10`) representa o **número de rounds de salt**, ou seja, o nível de segurança aplicado ao hash.

```ts
const passwordHash: string = await bcrypt.hash(user.password, 10);
```

> 💡 **Dica:** Quanto maior o número de rounds, mais segura (e mais lenta) será a geração do hash.
> O valor `10` é o mais usado por equilibrar segurança e desempenho.

---

## 🔍 Comparando um Hash

Use `bcrypt.compare()` para verificar se uma senha em texto puro corresponde ao hash armazenado.

```ts
const isValid: boolean = await bcrypt.compare(string, Hash);
```

Se o resultado for `true`, a senha informada é válida.

---

## ✅ Exemplo Completo

```ts
import bcrypt from "bcryptjs";

async function exemplo() {
  const senha = "123456";

  // Criptografando a senha
  const hash = await bcrypt.hash(senha, 10);
  console.log("Hash gerado:", hash);

  // Comparando senha com o hash
  const valida = await bcrypt.compare("123456", hash);
  console.log("Senha válida?", valida);
}

exemplo();
```

---

## 🧠 Resumo Rápido

| Função                        | Descrição                            | Retorno   |
| ----------------------------- | ------------------------------------ | --------- |
| `bcrypt.hash(texto, salt)`    | Gera o hash de uma string            | `string`  |
| `bcrypt.compare(texto, hash)` | Compara texto com hash criptografado | `boolean` |

---
