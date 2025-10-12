# 1. ⚛️ **React Hook useRouter**

- [1. ⚛️ **React Hook useRouter**](#1-️-react-hook-userouter)
	- [1.1. ⚙️ **Fluxo de Funcionamento**](#11-️-fluxo-de-funcionamento)
	- [1.2. 🧩 **Importação**](#12--importação)
	- [1.3. 📘 **Exemplo Base – Navegação**](#13--exemplo-base--navegação)
	- [1.4. 📍 **Acessando Parâmetros da URL (Dynamic Routes)**](#14--acessando-parâmetros-da-url-dynamic-routes)
		- [1.4.1. 🧱 Estrutura de pastas:](#141--estrutura-de-pastas)
		- [1.4.2. 🧩 Código:](#142--código)
	- [1.5. 🧭 **Obter Caminho Atual**](#15--obter-caminho-atual)
	- [1.6. ⚙️ **Redirecionamento com replace()**](#16-️-redirecionamento-com-replace)
	- [1.7. 🚀 **Forçando Recarregamento**](#17--forçando-recarregamento)
	- [1.8. 🧱 **Versão Antiga (Pages Router – até Next 12)**](#18--versão-antiga-pages-router--até-next-12)
	- [1.9. 💡 **Boas Práticas**](#19--boas-práticas)
	- [1.10. 🧩 **Exemplo Completo**](#110--exemplo-completo)
	- [1.11. 🧰 **Conclusão**](#111--conclusão)

---

💡 _Em resumo:_

> O `useRouter` conecta seu componente React à camada de rotas do Next.js.

---

## 1.1. ⚙️ **Fluxo de Funcionamento**

1. O Next.js cria o sistema de rotas automaticamente com base nas pastas dentro de `/app` ou `/pages`.
2. O `useRouter` permite **ler informações dessa rota** e **navegar entre páginas** de forma programática.
3. Quando o usuário navega, o hook **atualiza automaticamente** os dados (como `pathname`, `query` etc).

---

## 1.2. 🧩 **Importação**

A importação depende da **versão do Next.js** e do sistema de rotas em uso.

| Versão / Diretório                 | Importação                                     |
| :--------------------------------- | :--------------------------------------------- |
| App Router (Next 13+)              | `import { useRouter } from "next/navigation";` |
| Pages Router (Next 12 ou inferior) | `import { useRouter } from "next/router";`     |

---

## 1.3. 📘 **Exemplo Base – Navegação**

```jsx
"use client";
import { useRouter } from "next/navigation";

export default function PaginaInicial() {
  const router = useRouter();

  function irParaContato() {
    router.push("/contato");
  }

  return (
    <div className="p-4">
      <h1>Página Inicial</h1>
      <button onClick={irParaContato}>Ir para Contato</button>
    </div>
  );
}
```

🔹 `router.push("/rota")` → Redireciona para uma nova rota
🔹 `router.back()` → Volta à página anterior
🔹 `router.forward()` → Avança no histórico

---

## 1.4. 📍 **Acessando Parâmetros da URL (Dynamic Routes)**

### 1.4.1. 🧱 Estrutura de pastas:

```
/app/produtos/[id]/page.jsx
```

### 1.4.2. 🧩 Código:

```jsx
"use client";
import { useParams } from "next/navigation";

export default function Produto() {
  const params = useParams(); // retorna { id: "123" }

  return <h1>Produto ID: {params.id}</h1>;
}
```

💡 _Dica:_

- `useParams()` substitui `router.query` no novo App Router.
- Ideal para acessar parâmetros de rotas dinâmicas (ex: `/produtos/123`).

---

## 1.5. 🧭 **Obter Caminho Atual**

```jsx
"use client";
import { usePathname } from "next/navigation";

export default function CaminhoAtual() {
  const pathname = usePathname();

  return <p>Você está em: {pathname}</p>;
}
```

🧠 Útil para destacar menus ativos ou condicionar renderizações com base na rota.

---

## 1.6. ⚙️ **Redirecionamento com replace()**

`replace()` muda de rota **sem adicionar ao histórico** (não permite voltar com o botão do navegador).

```jsx
"use client";
import { useRouter } from "next/navigation";

export default function Logout() {
  const router = useRouter();

  function sair() {
    // lógica de logout...
    router.replace("/login");
  }

  return <button onClick={sair}>Sair</button>;
}
```

---

## 1.7. 🚀 **Forçando Recarregamento**

Em alguns casos (como APIs dinâmicas), você pode forçar o recarregamento da página:

```jsx
router.refresh();
```

⚠️ Disponível apenas no App Router (`next/navigation`).

---

## 1.8. 🧱 **Versão Antiga (Pages Router – até Next 12)**

Se ainda estiver usando `/pages`, a sintaxe muda levemente:

```jsx
import { useRouter } from "next/router";

export default function Produto() {
  const router = useRouter();
  const { id } = router.query; // equivalente a useParams()

  return <h1>Produto ID: {id}</h1>;
}
```

---

## 1.9. 💡 **Boas Práticas**

✅ Prefira **`next/navigation`** nas versões mais recentes (App Router).
✅ Use **`router.push()`** para transições de página e **`replace()`** para redirecionamentos de autenticação.
✅ Evite `router.push()` dentro de `useEffect()` sem controle de dependências — pode gerar loops de navegação.
✅ Combine com **middlewares** para redirecionar usuários automaticamente (ex: páginas protegidas).

---

## 1.10. 🧩 **Exemplo Completo**

```jsx
"use client";
import { useRouter, usePathname, useSearchParams } from "next/navigation";

export default function Dashboard() {
  const router = useRouter();
  const pathname = usePathname();
  const searchParams = useSearchParams();

  function irParaPerfil() {
    router.push("/perfil");
  }

  return (
    <div className="p-6">
      <h1>Dashboard</h1>
      <p>Rota atual: {pathname}</p>
      <p>Filtro: {searchParams.get("filtro") || "nenhum"}</p>
      <button onClick={irParaPerfil}>Ir para perfil</button>
    </div>
  );
}
```

---

## 1.11. 🧰 **Conclusão**

O `useRouter` (ou `useRoute`) é o **coração da navegação no Next.js**, permitindo controle total sobre rotas e parâmetros.
Ele é essencial para:

- Criar redirecionamentos dinâmicos
- Capturar parâmetros da URL
- Controlar transições de páginas
- Manipular o histórico de navegação
