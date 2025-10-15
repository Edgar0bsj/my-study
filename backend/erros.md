# ⚠️ Criando Erros Específicos com Classe Personalizada

- [⚠️ Criando Erros Específicos com Classe Personalizada](#️-criando-erros-específicos-com-classe-personalizada)
  - [🎯 Objetivo](#-objetivo)
  - [🧩 Criando a Classe de Erro Personalizada](#-criando-a-classe-de-erro-personalizada)
  - [🚀 Lançando um Erro](#-lançando-um-erro)
  - [🪤 Capturando Erros (Genérico)](#-capturando-erros-genérico)
  - [🧠 Capturando Apenas Erros Personalizados](#-capturando-apenas-erros-personalizados)
  - [✅ Exemplo Completo](#-exemplo-completo)
  - [🧾 Resumo Rápido](#-resumo-rápido)
  - [🧠 O que é `Error.captureStackTrace`](#-o-que-é-errorcapturestacktrace)
  - [⚙️ Sintaxe](#️-sintaxe)
  - [📋 Exemplo Didático](#-exemplo-didático)
  - [💡 Resumo Prático](#-resumo-prático)
  - [Capturando qualquer erro](#capturando-qualquer-erro)

## 🎯 Objetivo

Centralizar e padronizar o tratamento de erros na aplicação, permitindo enviar **mensagens personalizadas** junto com **códigos de status HTTP**.

---

## 🧩 Criando a Classe de Erro Personalizada

A classe `AppError` estende a classe nativa `Error`, adicionando uma propriedade `statusCode`.
Isso permite lançar erros com mensagens e códigos HTTP específicos (ex: 400, 404, 500...).

```ts
class AppError extends Error {
  statusCode: number;

  constructor(message: string, statusCode: number = 500) {
    super(message);
    this.statusCode = statusCode;
    Error.captureStackTrace(this, this.constructor);
  }
}

export default AppError;
```

> 💡 **Dica:** `Error.captureStackTrace` ajuda a manter o rastreamento da origem do erro sem poluir o log com detalhes desnecessários.

---

## 🚀 Lançando um Erro

Use `throw new AppError()` para lançar erros personalizados durante a execução do código.

```ts
if (!token) throw new AppError("Token ausente", 400);
```

---

## 🪤 Capturando Erros (Genérico)

Ao capturar erros com `try/catch`, é possível acessar a mensagem e o código definidos na classe.

```ts
catch (err) {
  console.error(`[ERROR]>> ${err.message || err}`);

  const statusCode = err.statusCode || 500;
  const message = err.message || "Erro interno do servidor";
}
```

---

## 🧠 Capturando Apenas Erros Personalizados

Quando quiser **tratar separadamente** os erros lançados pela sua aplicação (via `AppError`), use `instanceof AppError` para verificar a origem.

```ts
try {
  // código que pode lançar erro
} catch (error: any) {
  if (error instanceof AppError) {
    console.error(`[ERROR]>> ${error.message || error}`);

    const statusCode = error.statusCode || 500;
    const message = error.message || "Erro interno do servidor";

    res.status(statusCode).json({
      success: false,
      error: {
        message,
        statusCode,
      },
    });
  } else {
    // Erros genéricos (bibliotecas, sistema, etc.)
    console.error(error);
  }
}
```

---

## ✅ Exemplo Completo

```ts
import AppError from "./AppError";

function autenticarUsuario(token?: string) {
  if (!token) throw new AppError("Token ausente", 400);
  // ... restante da lógica
}

try {
  autenticarUsuario(undefined);
} catch (error: any) {
  if (error instanceof AppError) {
    console.error(`[ERROR]>> ${error.message}`);
  } else {
    console.error("Erro inesperado:", error);
  }
}
```

---

## 🧾 Resumo Rápido

| Método / Conceito      | Função                                              |
| ---------------------- | --------------------------------------------------- |
| `AppError`             | Classe personalizada para tratar erros da aplicação |
| `statusCode`           | Código HTTP do erro (ex: 400, 404, 500)             |
| `throw new AppError()` | Lança um erro com mensagem e status                 |
| `instanceof AppError`  | Verifica se o erro foi lançado pela aplicação       |

---

## 🧠 O que é `Error.captureStackTrace`

O método `Error.captureStackTrace()` é uma função nativa do Node.js que **controla a forma como a pilha de execução (stack trace)** de um erro é registrada.

Em outras palavras:
👉 Ele **remove da pilha** partes desnecessárias (como a própria classe `AppError`) e mantém apenas o ponto exato onde o erro foi lançado no seu código.

---

## ⚙️ Sintaxe

```ts
Error.captureStackTrace(targetObject, constructorFunction);
```

- **`targetObject`** → é o objeto de erro onde a stack será salva (geralmente `this`).
- **`constructorFunction`** → é a função que deve ser **omitida** da stack trace (geralmente o próprio construtor da classe).

---

## 📋 Exemplo Didático

Sem `captureStackTrace`:

```ts
class AppError extends Error {
  constructor(message: string) {
    super(message);
  }
}

new AppError("Algo deu errado");
```

Saída simplificada do erro:

```
Error: Algo deu errado
    at new AppError (AppError.ts:3)
    at Object.<anonymous> (index.ts:2)
    at Module._compile (...)
```

Note que a primeira linha da stack é **dentro da própria classe AppError**, o que **não é muito útil**.

---

Com `captureStackTrace`:

```ts
class AppError extends Error {
  constructor(message: string) {
    super(message);
    Error.captureStackTrace(this, this.constructor);
  }
}

new AppError("Algo deu errado");
```

Saída:

```
Error: Algo deu errado
    at Object.<anonymous> (index.ts:2)
    at Module._compile (...)
```

Agora, a pilha mostra **diretamente onde o erro aconteceu**, ignorando a parte da construção da classe.
👉 Isso torna os logs **mais limpos e informativos**, especialmente em APIs grandes.

---

## 💡 Resumo Prático

| Função                                            | O que faz                                                                                                   |
| ------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| `Error.captureStackTrace(this, this.constructor)` | Remove a referência da própria classe `AppError` da pilha, deixando apenas o ponto onde o erro foi lançado. |
| Sem `captureStackTrace`                           | A stack mostra a criação da instância de erro como se fosse parte da origem do erro.                        |
| Com `captureStackTrace`                           | O log fica mais limpo e útil para debug.                                                                    |

---

## Capturando qualquer erro

```ts
    } catch (error: unknown) {
      if (error instanceof Error) {
        setMsgError(["[Error]>> favor tente mais tarde"]);
        console.error("[Error] >> ", error.message);
      }
      if (typeof error === "object" && error !== null && "response" in error) {
        type ResponseError = {
          response: {
            data: {
              message: string;
            };
          };
        };
        setMsgError([(error as ResponseError).response.data.message]);
      }
    }
```
