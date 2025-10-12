# 1. ⚛️ **React Hook – useEffect**

- [1. ⚛️ **React Hook – useEffect**](#1-️-react-hook--useeffect)
	- [1.1. ⚙️ **Fluxo de Funcionamento**](#11-️-fluxo-de-funcionamento)
	- [1.2. 🧱 **Estrutura Básica**](#12--estrutura-básica)
	- [1.3. 🧩 **Exemplo 1 – Executar uma vez (montagem)**](#13--exemplo-1--executar-uma-vez-montagem)
	- [1.4. 🧩 **Exemplo 2 – Executar quando o estado muda**](#14--exemplo-2--executar-quando-o-estado-muda)
	- [1.5. 🧩 **Exemplo 3 – Buscar Dados de uma API**](#15--exemplo-3--buscar-dados-de-uma-api)
	- [1.6. 🧹 **Função de Limpeza (Cleanup)**](#16--função-de-limpeza-cleanup)
	- [1.7. ⚙️ **Controle Fino com Dependências**](#17-️-controle-fino-com-dependências)
		- [1.7.1. ❗ Exemplo sem dependências](#171--exemplo-sem-dependências)
	- [1.8. 🧠 **Combinação com useState**](#18--combinação-com-usestate)
	- [1.9. 🧩 **Exemplo – Efeito Condicional (busca dinâmica)**](#19--exemplo--efeito-condicional-busca-dinâmica)
	- [1.10. ⚠️ **Erros Comuns**](#110-️-erros-comuns)
	- [1.11. 🔍 **Resumo de Uso Correto**](#111--resumo-de-uso-correto)
	- [1.12. 🚀 **Exemplo Completo – Requisição + Atualização Dinâmica**](#112--exemplo-completo--requisição--atualização-dinâmica)
	- [1.13. 🧭 **Conclusão**](#113--conclusão)

---

## 1.1. ⚙️ **Fluxo de Funcionamento**

1. **Renderização Inicial (montagem)**
   → O componente é exibido pela primeira vez.
   → O `useEffect` é executado após o render.

2. **Atualização**
   → Se alguma variável monitorada mudar, o efeito é reexecutado.

3. **Desmontagem (limpeza)**
   → Quando o componente é removido, o `useEffect` pode limpar eventos, timers ou conexões.

---

## 1.2. 🧱 **Estrutura Básica**

```jsx
useEffect(() => {
  // Código a ser executado (efeito colateral)

  return () => {
    // (Opcional) Limpeza do efeito
  };
}, [dependencias]);
```

- **Função principal**: executada após renderização.
- **Função de retorno**: executada **antes da próxima execução** ou na desmontagem.
- **Array de dependências**: define _quando_ o efeito será reexecutado.

---

## 1.3. 🧩 **Exemplo 1 – Executar uma vez (montagem)**

```jsx
"use client";
import { useEffect } from "react";

export default function Saudacao() {
  useEffect(() => {
    console.log("Componente montado!");
  }, []); // Executa apenas uma vez

  return <h1>Bem-vindo, Edgar!</h1>;
}
```

📘 **Explicação:**
O array vazio `[]` faz com que o `useEffect` rode **apenas na montagem** (como `componentDidMount` em classes).

---

## 1.4. 🧩 **Exemplo 2 – Executar quando o estado muda**

```jsx
"use client";
import { useState, useEffect } from "react";

export default function Contador() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log(`Contador atualizado: ${count}`);
  }, [count]); // Executa sempre que "count" mudar

  return (
    <div className="text-center p-4">
      <h2>Contador: {count}</h2>
      <button
        onClick={() => setCount(count + 1)}
        className="bg-blue-600 text-white px-3 py-2 rounded"
      >
        +1
      </button>
    </div>
  );
}
```

📘 **Explicação:**
Toda vez que `count` muda, o `useEffect` é executado novamente.

---

## 1.5. 🧩 **Exemplo 3 – Buscar Dados de uma API**

```jsx
"use client";
import { useState, useEffect } from "react";
import axios from "axios";

export default function Usuarios() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    async function carregarUsuarios() {
      const response = await axios.get(
        "https://jsonplaceholder.typicode.com/users"
      );
      setUsers(response.data);
    }

    carregarUsuarios();
  }, []); // Executa apenas uma vez na montagem

  return (
    <ul className="p-4">
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

📘 **Explicação:**

- `useEffect` é usado para **fazer requisições assíncronas**.
- O React renderiza o componente, depois o efeito busca os dados.

---

## 1.6. 🧹 **Função de Limpeza (Cleanup)**

Usada para **evitar vazamento de memória** — essencial ao trabalhar com _event listeners_, _timers_ e _subscrições_.

```jsx
"use client";
import { useEffect } from "react";

export default function Timer() {
  useEffect(() => {
    const interval = setInterval(() => {
      console.log("Executando...");
    }, 1000);

    // Limpeza ao desmontar
    return () => clearInterval(interval);
  }, []);

  return <p>Verifique o console...</p>;
}
```

📘 **Explicação:**
Quando o componente é removido, o intervalo é **limpo automaticamente**.

---

## 1.7. ⚙️ **Controle Fino com Dependências**

| Dependência   | Comportamento                      |
| ------------- | ---------------------------------- |
| `[]`          | Executa **uma vez** (montagem)     |
| `[variavel]`  | Executa **quando a variável muda** |
| _(sem array)_ | Executa **a cada renderização**    |

---

### 1.7.1. ❗ Exemplo sem dependências

```jsx
useEffect(() => {
  console.log("Executa toda renderização");
});
```

⚠️ Evite esse formato em componentes que re-renderizam muito — pode causar **loop infinito**.

---

## 1.8. 🧠 **Combinação com useState**

O `useEffect` e o `useState` geralmente trabalham juntos.
Exemplo prático: buscar dados e atualizar o estado.

```jsx
"use client";
import { useState, useEffect } from "react";

export default function Tempo() {
  const [hora, setHora] = useState(new Date().toLocaleTimeString());

  useEffect(() => {
    const interval = setInterval(() => {
      setHora(new Date().toLocaleTimeString());
    }, 1000);

    return () => clearInterval(interval); // Limpa o timer
  }, []);

  return <h2 className="text-center">{hora}</h2>;
}
```

📘 **Explicação:**

- Atualiza o estado a cada segundo.
- Quando o componente sai da tela, o timer é limpo.

---

## 1.9. 🧩 **Exemplo – Efeito Condicional (busca dinâmica)**

```jsx
"use client";
import { useState, useEffect } from "react";

export default function BuscaUsuario() {
  const [id, setId] = useState(1);
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch(`https://jsonplaceholder.typicode.com/users/${id}`)
      .then((res) => res.json())
      .then((data) => setUser(data));
  }, [id]); // refaz a busca quando "id" muda

  return (
    <div className="p-4 text-center">
      <button
        onClick={() => setId(id + 1)}
        className="bg-green-600 text-white px-4 py-2 rounded"
      >
        Próximo usuário
      </button>
      {user && <p className="mt-2">{user.name}</p>}
    </div>
  );
}
```

---

## 1.10. ⚠️ **Erros Comuns**

| Erro                                            | Causa                                                  |
| ----------------------------------------------- | ------------------------------------------------------ |
| Loop infinito                                   | Efeito sem dependências ou com dependências incorretas |
| Chamar `useEffect` dentro de condições ou loops | Hooks devem estar no **topo** do componente            |
| Atualizar estado sem dependências               | Pode reexecutar infinitamente o `useEffect`            |
| Esquecer de limpar eventos/timers               | Pode causar vazamentos de memória                      |

---

## 1.11. 🔍 **Resumo de Uso Correto**

| Objetivo                  | Sintaxe Ideal                                |
| ------------------------- | -------------------------------------------- |
| Executar uma vez          | `useEffect(() => {}, []);`                   |
| Executar quando algo muda | `useEffect(() => {}, [variavel]);`           |
| Executar e limpar         | `useEffect(() => { return () => {}; }, []);` |

---

## 1.12. 🚀 **Exemplo Completo – Requisição + Atualização Dinâmica**

```jsx
"use client";
import { useState, useEffect } from "react";
import axios from "axios";

export default function ListaUsuarios() {
  const [users, setUsers] = useState([]);
  const [reload, setReload] = useState(false);

  useEffect(() => {
    async function buscarUsuarios() {
      const res = await axios.get("https://jsonplaceholder.typicode.com/users");
      setUsers(res.data);
    }

    buscarUsuarios();
  }, [reload]); // reexecuta quando reload mudar

  return (
    <div className="p-4 text-center">
      <button
        onClick={() => setReload(!reload)}
        className="bg-blue-600 text-white px-4 py-2 rounded mb-3"
      >
        Recarregar lista
      </button>

      <ul>
        {users.map((u) => (
          <li key={u.id}>{u.name}</li>
        ))}
      </ul>
    </div>
  );
}
```

📘 **Explicação:**

- O `useEffect` busca os usuários.
- Ao clicar no botão, o estado `reload` muda, forçando uma **nova execução do efeito**.

---

## 1.13. 🧭 **Conclusão**

O `useEffect` é o **cérebro do ciclo de vida** em componentes React.
Ele substitui métodos antigos como `componentDidMount`, `componentDidUpdate` e `componentWillUnmount`.

🔑 **Em resumo:**

- Controla _quando e como_ efeitos acontecem.
- Deve sempre considerar suas **dependências**.
- Deve **limpar** qualquer recurso criado dentro dele.
- É essencial para lidar com **API calls, timers e eventos externos**.
