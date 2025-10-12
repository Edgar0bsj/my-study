# 1. ⚛️ **React Hook – useContext**

- [1. ⚛️ **React Hook – useContext**](#1-️-react-hook--usecontext)
	- [1.1. ⚙️ **Fluxo de Funcionamento**](#11-️-fluxo-de-funcionamento)
	- [1.2. 🧩 **Estrutura Base**](#12--estrutura-base)
	- [1.3. 🧱 **Explicando as Partes**](#13--explicando-as-partes)
	- [1.4. 🌍 **Exemplo Prático Completo**](#14--exemplo-prático-completo)
	- [1.5. 💡 **Boas Práticas**](#15--boas-práticas)
	- [1.6. 🧩 **Contexto com Tipagem (TypeScript)**](#16--contexto-com-tipagem-typescript)
	- [1.7. 🧰 **Conclusão**](#17--conclusão)

💡 _Em resumo:_

> O `useContext` é ideal quando vários componentes precisam acessar as mesmas informações — como tema, idioma, autenticação ou dados do usuário.

---

## 1.1. ⚙️ **Fluxo de Funcionamento**

1. **Criação do Contexto:**
   Com `createContext()`, criamos um objeto que armazenará os dados compartilhados.

2. **Provedor (Provider):**
   Um componente que _envolve_ a parte da aplicação que terá acesso aos dados.

3. **Consumidor (useContext):**
   Qualquer componente dentro do `Provider` pode acessar os dados usando o hook `useContext`.

---

## 1.2. 🧩 **Estrutura Base**

```jsx
import { createContext, useContext, useState } from "react";

// 1. Criar o contexto
const TemaContext = createContext();

// 2. Criar o Provider
export function TemaProvider({ children }) {
  const [tema, setTema] = useState("claro");

  return (
    <TemaContext.Provider value={{ tema, setTema }}>
      {children}
    </TemaContext.Provider>
  );
}

// 3. Consumir o contexto
export function Componente() {
  const { tema, setTema } = useContext(TemaContext);

  return (
    <div>
      <p>Tema atual: {tema}</p>
      <button onClick={() => setTema(tema === "claro" ? "escuro" : "claro")}>
        Alternar Tema
      </button>
    </div>
  );
}
```

---

## 1.3. 🧱 **Explicando as Partes**

| Parte             | Descrição                                                    |
| :---------------- | :----------------------------------------------------------- |
| `createContext()` | Cria o “container” que armazenará os dados globais.          |
| `Provider`        | Componente que distribui os dados aos filhos.                |
| `useContext()`    | Hook que consome o valor do contexto em qualquer componente. |
| `value`           | Objeto com os dados e funções disponíveis no contexto.       |

---

## 1.4. 🌍 **Exemplo Prático Completo**

```jsx
import { createContext, useContext, useState } from "react";

// 1️⃣ Criar o contexto
const AuthContext = createContext();

// 2️⃣ Criar o Provider
export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);

  function login(nome) {
    setUser({ nome });
  }

  function logout() {
    setUser(null);
  }

  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

// 3️⃣ Componente consumidor
function Perfil() {
  const { user, login, logout } = useContext(AuthContext);

  return (
    <div>
      {user ? (
        <>
          <p>Bem-vindo, {user.nome}!</p>
          <button onClick={logout}>Sair</button>
        </>
      ) : (
        <button onClick={() => login("Edgar")}>Entrar</button>
      )}
    </div>
  );
}

// 4️⃣ App principal
export default function App() {
  return (
    <AuthProvider>
      <Perfil />
    </AuthProvider>
  );
}
```

---

## 1.5. 💡 **Boas Práticas**

✅ **Mantenha o contexto específico.**
Evite colocar tudo em um único contexto global — crie contextos separados (ex: `AuthContext`, `ThemeContext`, `ConfigContext`).

✅ **Encapsule lógica dentro do Provider.**
Deixe toda a lógica de estado e funções dentro do Provider para manter o código organizado.

✅ **Evite re-renderizações desnecessárias.**
Divida contextos muito grandes para evitar que componentes recarreguem sem necessidade.

✅ **Use hooks personalizados.**
Crie um _custom hook_ para consumir o contexto, simplificando o código:

```jsx
export const useAuth = () => useContext(AuthContext);
```

Agora, basta usar:

```jsx
const { user, login } = useAuth();
```

---

## 1.6. 🧩 **Contexto com Tipagem (TypeScript)**

Exemplo rápido para projetos tipados:

```tsx
import { createContext, useContext, useState, ReactNode } from "react";

type AuthType = {
  user: string | null;
  login: (nome: string) => void;
  logout: () => void;
};

const AuthContext = createContext<AuthType | undefined>(undefined);

export function AuthProvider({ children }: { children: ReactNode }) {
  const [user, setUser] = useState<string | null>(null);

  function login(nome: string) {
    setUser(nome);
  }

  function logout() {
    setUser(null);
  }

  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  const context = useContext(AuthContext);
  if (!context)
    throw new Error("useAuth deve ser usado dentro de AuthProvider");
  return context;
}
```

---

## 1.7. 🧰 **Conclusão**

O `useContext` é uma ferramenta poderosa para **compartilhar dados entre componentes sem props desnecessárias**.
É ideal para estados globais como:

- Autenticação de usuário
- Tema (dark/light)
- Idioma da interface
- Configurações do sistema
