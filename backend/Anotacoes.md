# **↪︎ 👨‍💻 Iniciando Projeto**

- [**↪︎ 👨‍💻 Iniciando Projeto**](#︎--iniciando-projeto)
  - [**↪︎ Fluxo de Funcionamento**](#︎-fluxo-de-funcionamento)
  - [**↪︎ 1. Iniciar o projeto**](#︎-1-iniciar-o-projeto)
  - [**↪︎ 2. Instalar TypeScript e Tipos Node**](#︎-2-instalar-typescript-e-tipos-node)
  - [**↪︎ 3. Gerar o arquivo `tsconfig.json`**](#︎-3-gerar-o-arquivo-tsconfigjson)
  - [**↪︎ 4. Configurar o `package.json`**](#︎-4-configurar-o-packagejson)
  - [**↪︎ 5. Estrutura de Pastas Recomendada**](#︎-5-estrutura-de-pastas-recomendada)
  - [**↪︎ 6. Exemplo de Código Base (`src/index.ts`)**](#︎-6-exemplo-de-código-base-srcindexts)
  - [**↪︎ 7. Execução Automática (opcional)**](#︎-7-execução-automática-opcional)
  - [**↪︎ Resumo do Fluxo**](#︎-resumo-do-fluxo)
  - [**↪︎ Tipagem Global**](#︎-tipagem-global)
- [-\> Erros](#--erros)
  - [Capturando erro especifico](#capturando-erro-especifico)
  - [Criando Erros especifico](#criando-erros-especifico)

## **↪︎ Fluxo de Funcionamento**

1. **Iniciar o projeto Node.js**
2. **Instalar e configurar o TypeScript**
3. **Configurar o sistema de módulos (ES Modules)**
4. **Gerar o `tsconfig.json` com as opções ideais**
5. **Preparar o `package.json`**
6. **Criar uma estrutura inicial de pastas**
7. **Adicionar scripts de build e execução**
8. **Compilar e rodar o projeto**

---

## **↪︎ 1. Iniciar o projeto**

Crie a pasta e inicialize o projeto:

```bash
mkdir meu-projeto-ts
cd meu-projeto-ts
npm init -y
```

---

## **↪︎ 2. Instalar TypeScript e Tipos Node**

Instale o TypeScript e as definições de tipos:

```bash
npm install typescript --save-dev
npm install @types/node --save-dev
```

> `@types/node` garante que o TypeScript reconheça as APIs nativas do Node.js.

---

## **↪︎ 3. Gerar o arquivo `tsconfig.json`**

Crie o arquivo de configuração base com o comando:

```bash
npx tsc --init
```

Agora edite o `tsconfig.json` com uma configuração voltada para **escalabilidade** e **módulos ES**:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ESNext",
    "moduleResolution": "node",
    "rootDir": "src",
    "outDir": "dist",
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "skipLibCheck": true,
    "allowJs": false,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmitOnError": true,
    "removeComments": true,
    "allowSyntheticDefaultImports": true
  },
  "include": ["src"],
  "exclude": ["node_modules"]
}
```

> - `"module": "ESNext"` ativa o sistema `import/export`.
> - `"rootDir"` e `"outDir"` garantem uma estrutura limpa entre **código fonte (`src`)** e **código compilado (`dist`)**.

---

## **↪︎ 4. Configurar o `package.json`**

Abra o `package.json` e adicione a linha `"type": "module"` logo após `"main"`.
Isso informa ao Node que o projeto usa **ES Modules** em vez de CommonJS.

Exemplo:

```json
{
  "name": "meu-projeto-ts",
  "version": "1.0.0",
  "main": "dist/index.js",
  "type": "module",
  "scripts": {
    "build": "tsc",
    "start": "node dist/index.js",
    "dev": "tsc --watch"
  },
  "devDependencies": {
    "typescript": "^5.0.0",
    "@types/node": "^20.0.0"
  }
}
```

---

## **↪︎ 5. Estrutura de Pastas Recomendada**

```
meu-projeto-ts/
├── src/
│   ├── config/
│   ├── controllers/
│   ├── services/
│   ├── utils/
│   └── index.ts
├── dist/
├── package.json
└── tsconfig.json
```

> Essa estrutura facilita escalar o projeto com **separação por responsabilidade**.

---

## **↪︎ 6. Exemplo de Código Base (`src/index.ts`)**

```ts
// src/index.ts
config();

function main(): void {
  console.log("🚀 Servidor iniciado com TypeScript!");
}

main();
```

Compile com:

```bash
npx tsc
```

E rode com:

```bash
npm start
```

---

## **↪︎ 7. Execução Automática (opcional)**

Para desenvolvimento mais dinâmico, instale o `ts-node` e `nodemon`:

```bash
npm install ts-node nodemon --save-dev
```

Atualize os scripts no `package.json`:

```json
"scripts": {
  "dev": "nodemon --watch src --exec ts-node src/index.ts",
  "build": "tsc",
  "start": "node dist/index.js"
}
```

Agora, rode o projeto em modo de desenvolvimento com:

```bash
npm run dev
```

---

## **↪︎ Resumo do Fluxo**

| Etapa | Comando / Arquivo                    | Descrição                               |
| ----- | ------------------------------------ | --------------------------------------- |
| 1     | `npm init -y`                        | Cria o projeto                          |
| 2     | `npm i -D typescript @types/node`    | Instala dependências de desenvolvimento |
| 3     | `npx tsc --init`                     | Gera o `tsconfig.json`                  |
| 4     | `"type": "module"` no `package.json` | Ativa ES Modules                        |
| 5     | `npx tsc`                            | Compila para `dist/`                    |
| 6     | `npm start`                          | Executa o projeto compilado             |

---

## **↪︎ Tipagem Global**

`src/types/global.d.ts` para definir **tipos globais**, úteis em grandes projetos:

```ts
declare namespace App {
  interface User {
    id: string;
    name: string;
  }
}
```

> Assim você pode usar `App.User` em qualquer parte do projeto sem precisar importar.

---

# -> Erros

## Capturando erro especifico

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

---

## Criando Erros especifico

- Criando classe que permite mandar mais parametros

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

---

- Lançando Error

```ts
if (!token) throw new AppError("Token ausente", 400);
```

---

- Capturando Error

```ts
//Dentro de um catch
catch(err){
	console.error(`[ERROR]>> ${err.message || err}`);
	const statusCode = err.statusCode || 500;
	const message = err.message || "Erro interno do servidor";

}
```

---

- Capturando Error só lançado por você

```ts
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
      }else {
		  // Erros Genéricos
		console.error(error)

	  }

    }
```
