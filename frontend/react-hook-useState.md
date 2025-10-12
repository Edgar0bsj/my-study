# 1. ⚛️ **React Hook – useState**

## 1.1. ⚙️ **Fluxo de Funcionamento**

- [1. ⚛️ **React Hook – useState**](#1-️-react-hook--usestate)
	- [1.1. ⚙️ **Fluxo de Funcionamento**](#11-️-fluxo-de-funcionamento)
	- [1.2. 🧱 **Estrutura Básica**](#12--estrutura-básica)
	- [1.3. 🧩 **Exemplo Prático – Contador**](#13--exemplo-prático--contador)
	- [1.4. 💡 **Dica Importante**](#14--dica-importante)
	- [1.5. ⚙️ **Trabalhando com Tipos de Dados Diferentes**](#15-️-trabalhando-com-tipos-de-dados-diferentes)
		- [1.5.1. 📦 Strings](#151--strings)
		- [1.5.2. 🧮 Números](#152--números)
		- [1.5.3. ✅ Booleanos (para alternância)](#153--booleanos-para-alternância)
		- [1.5.4. 🧩 Arrays](#154--arrays)
		- [1.5.5. 🧱 Objetos](#155--objetos)
	- [1.6. 🧠 **Renderização Condicional**](#16--renderização-condicional)
	- [1.7. 🧮 **Atualizações Complexas**](#17--atualizações-complexas)
	- [1.8. ⚠️ **Erros Comuns**](#18-️-erros-comuns)
	- [1.9. 🚀 **Exemplo Completo – Formulário Simples**](#19--exemplo-completo--formulário-simples)
	- [1.10. 🧭 **Conclusão**](#110--conclusão)

1. **Inicialização**

   - Quando o componente é carregado, `useState` define um valor inicial.
   - Esse valor pode ser número, string, objeto, array ou boolean.

2. **Atualização**

   - Ao chamar a função de atualização (`setState`), o React **re-renderiza** o componente com o novo valor.

3. **Renderização Reativa**

   - Qualquer parte do JSX que dependa desse estado é **automaticamente atualizada**.

---

## 1.2. 🧱 **Estrutura Básica**

```jsx
const [valor, setValor] = useState(valorInicial);
```

- `valor` → variável que armazena o estado atual.
- `setValor` → função usada para atualizar o estado.
- `valorInicial` → valor inicial definido na primeira renderização.

---

## 1.3. 🧩 **Exemplo Prático – Contador**

```jsx
"use client";
import { useState } from "react";

export default function Contador() {
  const [count, setCount] = useState(0);

  function incrementar() {
    setCount(count + 1);
  }

  function zerar() {
    setCount(0);
  }

  return (
    <div className="p-4 text-center">
      <h2 className="text-xl mb-2">Contador: {count}</h2>
      <button
        onClick={incrementar}
        className="bg-blue-500 text-white px-4 py-2 rounded mr-2"
      >
        +1
      </button>
      <button
        onClick={zerar}
        className="bg-gray-600 text-white px-4 py-2 rounded"
      >
        Zerar
      </button>
    </div>
  );
}
```

🧠 **O que acontece aqui:**

- `useState(0)` define o valor inicial como `0`.
- Ao clicar no botão, `setCount` muda o estado.
- O React percebe a mudança e **renderiza novamente** o componente com o novo valor de `count`.

---

## 1.4. 💡 **Dica Importante**

Ao atualizar com base no **estado anterior**, use a forma **funcional**:

```jsx
setCount((prev) => prev + 1);
```

Isso evita problemas quando há múltiplas atualizações seguidas (como dentro de loops ou promessas).

---

## 1.5. ⚙️ **Trabalhando com Tipos de Dados Diferentes**

### 1.5.1. 📦 Strings

```jsx
const [nome, setNome] = useState("Edgar");
setNome("Edgar Barbosa");
```

### 1.5.2. 🧮 Números

```jsx
const [idade, setIdade] = useState(25);
setIdade(idade + 1);
```

### 1.5.3. ✅ Booleanos (para alternância)

```jsx
const [ativo, setAtivo] = useState(false);
setAtivo(!ativo);
```

### 1.5.4. 🧩 Arrays

```jsx
const [itens, setItens] = useState([]);

setItens([...itens, "Novo item"]);
```

### 1.5.5. 🧱 Objetos

```jsx
const [user, setUser] = useState({ nome: "Edgar", idade: 25 });

setUser({ ...user, idade: 26 });
```

---

## 1.6. 🧠 **Renderização Condicional**

O `useState` é muito usado junto com **renderização condicional**:

```jsx
"use client";
import { useState } from "react";

export default function MostrarMensagem() {
  const [mostrar, setMostrar] = useState(false);

  return (
    <div className="text-center mt-4">
      <button
        onClick={() => setMostrar(!mostrar)}
        className="bg-green-600 text-white px-4 py-2 rounded"
      >
        {mostrar ? "Ocultar" : "Mostrar"}
      </button>

      {mostrar && <p className="mt-3 text-gray-700">Olá, Edgar!</p>}
    </div>
  );
}
```

📘 **Explicação:**

- O estado `mostrar` define se o parágrafo é exibido ou não.
- O React atualiza a interface **instantaneamente** conforme o estado muda.

---

## 1.7. 🧮 **Atualizações Complexas**

O `useState` pode receber uma **função inicializadora** útil em cálculos pesados.

```jsx
const [valor, setValor] = useState(() => {
  console.log("Executado apenas uma vez");
  return 100 * 2; // Valor inicial calculado
});
```

Dessa forma, a função é executada **apenas na primeira renderização**.

---

## 1.8. ⚠️ **Erros Comuns**

| Erro                                             | Explicação                                                      |
| ------------------------------------------------ | --------------------------------------------------------------- |
| Chamar `useState` fora do componente             | Hooks só podem ser usados dentro de componentes ou custom hooks |
| Atualizar o estado diretamente (`count = 5`)     | Sempre use `setCount(5)`                                        |
| Criar `useState` dentro de loops ou condicionais | Hooks devem ser declarados no **topo** do componente            |

---

## 1.9. 🚀 **Exemplo Completo – Formulário Simples**

```jsx
"use client";
import { useState } from "react";

export default function Formulario() {
  const [nome, setNome] = useState("");
  const [email, setEmail] = useState("");

  function handleSubmit(e) {
    e.preventDefault();
    console.log({ nome, email });
  }

  return (
    <form onSubmit={handleSubmit} className="p-4 space-y-2">
      <input
        type="text"
        placeholder="Nome"
        value={nome}
        onChange={(e) => setNome(e.target.value)}
        className="border p-2 rounded w-full"
      />

      <input
        type="email"
        placeholder="E-mail"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        className="border p-2 rounded w-full"
      />

      <button type="submit" className="bg-blue-600 text-white p-2 rounded">
        Enviar
      </button>
    </form>
  );
}
```

📍 Aqui, cada campo do formulário está **vinculado ao estado** (`nome`, `email`).
Ao digitar, o `setState` atualiza o valor e o React re-renderiza o campo.

---

## 1.10. 🧭 **Conclusão**

O `useState` é o **ponto de partida da reatividade** no React.
Ele permite que os componentes respondam a ações do usuário, mudem dinamicamente e mantenham controle total sobre os dados exibidos.

🔑 **Resumo rápido:**

- Estado = memória reativa do componente.
- `setState()` = dispara atualização.
- Sempre inicialize com o tipo correto.
- Use o padrão funcional para atualizar com base no estado anterior.
