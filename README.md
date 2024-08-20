# 🌟 Guia Divertido para Criar um Projeto Node.js com TypeScript no GitHub com Codespaces! 🌟

Olá, pequeno aventureiro digital! 👋 Neste guia, vamos usar **GitHub** e **GitHub Codespaces** para criar um projeto Node.js com TypeScript. Vamos explorar juntos! 🚀

---

## 🧩 O que Vamos Fazer?

1. **Criar um Repositório no GitHub**
2. **Abrir um Codespace**
3. **Configurar o Projeto no Codespace**
4. **Escrever o Código**
5. **Testar e Subir o Código para o GitHub**

---

## 1. 📦 Criando um Repositório no GitHub

1. **Abra seu navegador** e vá para [GitHub](https://github.com).

2. **Faça login** na sua conta GitHub. Se ainda não tem uma conta, crie uma!

3. **Clique no botão “New”** no canto superior esquerdo.

4. **Preencha as informações do repositório**:
   - **Repository name**: Escolha um nome legal para seu projeto, como `meu-projeto-nodejs`.
   - **Description**: (Opcional) Adicione uma breve descrição, como *"Meu primeiro projeto Node.js com TypeScript"*.
   - **Public** ou **Private**: Escolha o **público** para que todos possam ver o seu projeto (incluindo seu professor).
   - **Initialize this repository with a README**: Marque esta opção para criar um arquivo README automaticamente.
   - **Add .gitignore** e **Choose a license**: Não mexa nisso agora, você não irá utilizar!

5. **Clique em "Create repository"**.

---

## 2. 🚀 Abrindo um Codespace

1. **No seu novo repositório no GitHub**, clique no botão **"Code"** e depois em **"Open with Codespaces"**.

2. **Clique em "Create codespace on main"**. Isso vai abrir um novo Codespace, que é um ambiente de desenvolvimento online diretamente no seu navegador!

3. **Aguarde alguns momentos** enquanto o Codespace é configurado. Você verá um editor de código baseado na web se abrindo.

---

## 3. 🛠️ Configurando o Projeto no Codespace

1. **Abra o terminal** no Codespace. Você pode fazer isso clicando em **"Terminal"** na barra superior e depois em **"New Terminal"**.

2. **Digite os seguintes comandos** no terminal para iniciar e configurar seu projeto:

    ```bash
    # Cria um novo projeto Node.js
    npm init -y

    # Instala o Express, CORS e SQLite
    npm install express cors sqlite3 sqlite

    # Instala TypeScript e outras ferramentas para desenvolvimento
    npm install --save-dev typescript nodemon ts-node @types/express @types/cors

    # Cria o arquivo de configuração do TypeScript
    npx tsc --init

    # Cria uma pasta para o código-fonte
    mkdir src

    # Cria o arquivo principal do TypeScript
    touch src/app.ts
    ```

3. **Configure o TypeScript**:

    Abra o arquivo `tsconfig.json` e altere as configurações para:

    ```json
    {
      "compilerOptions": {
        "target": "ES2017",
        "module": "commonjs",
        "outDir": "./dist",
        "rootDir": "./src",
        "strict": true,
        "esModuleInterop": true,
        "skipLibCheck": true,
        "forceConsistentCasingInFileNames": true
      }
    }
    ```

    Explicação:
    - `outDir` define onde o código compilado será salvo (`./dist`).
    - `rootDir` define onde o código fonte está (`./src`).

4. **Adicione o script para rodar o servidor**:

    Abra o arquivo `package.json` e adicione o seguinte dentro da seção `"scripts"`:

    ```json
    "scripts": {
      "dev": "nodemon src/app.ts"
    }
    ```

    Explicação:
    - `"dev": "nodemon src/app.ts"` define um comando que irá rodar seu servidor e reiniciar automaticamente quando você fizer mudanças.

---

## 4. ✍️ Escrevendo o Código

1. **Crie e edite o arquivo `src/app.ts`**:

    Adicione o seguinte código para configurar seu servidor:

    ```typescript
    import express from 'express';
    import cors from 'cors';
    import { connect } from './database';

    const port = 3333;
    const app = express();

    app.use(cors());
    app.use(express.json());

    app.get('/', (req, res) => {
      res.send('Hello World');
    });

    app.post('/users', async (req, res) => {
      const db = await connect();
      const { name, email } = req.body;

      const result = await db.run('INSERT INTO users (name, email) VALUES (?, ?)', [name, email]);
      const user = await db.get('SELECT * FROM users WHERE id = ?', [result.lastID]);

      res.json(user);
    });

    app.get('/users', async (req, res) => {
      const db = await connect();
      const users = await db.all('SELECT * FROM users');

      res.json(users);
    });

    app.put('/users/:id', async (req, res) => {
      const db = await connect();
      const { name, email } = req.body;
      const { id } = req.params;

      await db.run('UPDATE users SET name = ?, email = ? WHERE id = ?', [name, email, id]);
      const user = await db.get('SELECT * FROM users WHERE id = ?', [id]);

      res.json(user);
    });

    app.delete('/users/:id', async (req, res) => {
      const db = await connect();
      const { id } = req.params;

      await db.run('DELETE FROM users WHERE id = ?', [id]);

      res.json({ message: 'User deleted' });
    });

    app.listen(port, () => {
      console.log(`Server running on port ${port}`);
    });
    ```

2. **Configure o Banco de Dados**:

    Crie o arquivo `src/database.ts` e adicione o seguinte código:

    ```typescript
    import { open, Database } from 'sqlite';
    import sqlite3 from 'sqlite3';

    let instance: Database | null = null;

    export async function connect() {
      if (instance) return instance;

      const db = await open({
         filename: './src/database.sqlite',
         driver: sqlite3.Database
       });
     
     await db.exec(`
       CREATE TABLE IF NOT EXISTS users (
         id INTEGER PRIMARY KEY AUTOINCREMENT,
         name TEXT,
         email TEXT
       )
     `);

      instance = db;
      return db;
    }
    ```

    Explicação:
    - `connect()` cria uma conexão com o banco de dados SQLite e configura a tabela `users`.

---

## 5. 🧪 Testando o Código

1. **No terminal do Codespace**, digite:

    ```bash
    npm run dev
    ```

    Isso vai iniciar seu servidor.

2. **Veja o resultado**:
   - No terminal, você deve ver a mensagem `Server running on port 3333`.
   - Abra o navegador integrado no Codespace e vá para [http://localhost:3333](http://localhost:3333). Você deve ver a sua mensagem digitada na página, no caso a padrão é "Hello World".

---

## 6. 📚 Subindo o Código para o GitHub

1. **No terminal do Codespace**, execute os seguintes comandos para adicionar, commitar e subir suas mudanças para o GitHub:

    ```bash
    git add .
    git commit -m "Adiciona servidor Node.js com TypeScript e banco de dados SQLite"
    git push origin main
    ```

    Explicação:
    - `git add .` adiciona todos os arquivos ao índice do Git.
    - `git commit -m "..."` cria um commit com uma mensagem.
    - `git push origin main` envia suas mudanças para o repositório no GitHub.

---
Fim. '-' @arp.br