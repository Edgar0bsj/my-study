# **↪︎ 📘 CONVENÇÕES DE NOMES DE COMMITS (GITHUB)**

- [**↪︎ 📘 CONVENÇÕES DE NOMES DE COMMITS (GITHUB)**](#︎--convenções-de-nomes-de-commits-github)
	- [⚙️ **Fluxo de Funcionamento**](#️-fluxo-de-funcionamento)
	- [🧩 **Exemplo de uso prático**](#-exemplo-de-uso-prático)
		- [**Resumo rápido**](#resumo-rápido)

---

## ⚙️ **Fluxo de Funcionamento**

1. **Estrutura padrão (Conventional Commits):**

   ```
   <tipo>(escopo opcional): <mensagem curta>
   ```

   🔹 **Regras principais:**

   - O tipo sempre em **minúsculas**.
   - A mensagem no **imperativo**, como se fosse um comando:
     Ex: `add`, `fix`, `update`, `remove`.
   - Não colocar ponto final na mensagem curta.
   - Usar **inglês ou português**, mas manter o padrão consistente em todo o projeto.

2. **Tipos mais comuns:**

   | Tipo       | Significado                                                    | Exemplo                                   |
   | ---------- | -------------------------------------------------------------- | ----------------------------------------- |
   | `feat`     | Adição de uma nova funcionalidade                              | `feat(auth): add login with JWT`          |
   | `fix`      | Correção de bug                                                | `fix(routes): correct 404 handler`        |
   | `docs`     | Alteração na documentação                                      | `docs(readme): update usage examples`     |
   | `style`    | Ajustes que não alteram a lógica (indentação, espaço, vírgula) | `style: format code with prettier`        |
   | `refactor` | Mudança no código sem alterar comportamento                    | `refactor(api): simplify user validation` |
   | `test`     | Adição ou modificação de testes                                | `test(user): add unit tests for register` |
   | `chore`    | Tarefas diversas, dependências, build, configs                 | `chore(deps): update express to v5`       |
   | `perf`     | Melhorias de performance                                       | `perf(db): improve query execution time`  |
   | `ci`       | Alterações no pipeline de integração contínua                  | `ci(github): add deploy workflow`         |

3. **Commit com corpo descritivo (opcional, mas recomendado):**

   ```
   feat(api): implement user authentication

   - added token validation middleware
   - integrated with MongoDB for session control
   - updated routes for /login and /logout
   ```

4. **Commit com referência a issues:**

   ```
   fix(login): handle invalid password errors (#32)
   ```

5. **Commits de versionamento (usados em releases):**

   ```
   chore(release): v1.2.0
   ```

---

## 🧩 **Exemplo de uso prático**

```bash
# Iniciando o projeto
git init

# Adicionando e comitando corretamente
git add .
git commit -m "feat(api): create user model and controller"

# Atualizando documentação
git commit -m "docs(readme): add installation instructions"

# Corrigindo bug
git commit -m "fix(auth): correct jwt expiration handling"
```

---

### **Resumo rápido**

- **Formato:** `tipo(escopo opcional): descrição curta`
- **Tipos mais usados:** `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`.
- **Objetivo:** deixar o histórico limpo, compreensível e escalável.
