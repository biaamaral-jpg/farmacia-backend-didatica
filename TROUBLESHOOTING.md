# Guia de Solução de Erros 404 - Farmácia Backend

## ✅ Erros Corrigidos

### 1. **Database Connection Pool (Crítico)**
- **Problema**: Cada arquivo de modelo criava uma nova instância de `DatabaseModel`, desperdiçando conexões de banco de dados
- **Solução**: Implementado padrão **Singleton** para compartilhar uma única conexão pool
- **Arquivos modificados**:
  - `src/model/DatabaseModel.ts` - Adicionado método estático `getInstance()`
  - `src/app.ts` - Usa `DatabaseModel.getInstance()`
  - `src/model/Cliente.ts`, `Produtos.ts`, `Pedido.ts`, `ItemPedido.ts` - Todas usam singleton

### 2. **Variáveis de Ambiente**
- **Problema**: `.env` arquivo estava presente mas faltava verificação
- **Solução**: Criado arquivo `.env.example` com template
- **Seu `.env` atual**:
  ```
  DB_USER=postgres
  DB_PASSWORD=admin
  DB_HOST=localhost
  DB_PORT=5432
  DB_NAME=farmacia-didatica
  ```

### 3. **Script de Inicialização**
- **Adicionado** ao `package.json`:
  - `npm start` - Inicia o servidor em produção
  - `npm run dev` - Inicia com nodemon (desenvolvimento)

---

## 🚀 Como Executar Corretamente

### 1. **Instale as dependências** (se não tiver feito):
```bash
npm install
```

### 2. **Verifique e configure o banco de dados**:
- Certifique-se que PostgreSQL está rodando
- Crie o banco de dados (ou use um existente):
  ```sql
  CREATE DATABASE "farmacia-didatica";
  ```
- Execute o script SQL de inicialização:
  ```bash
  psql -U postgres -d farmacia-didatica -f infra/sql/init_farmacia_nfn.sql
  ```

### 3. **Compile o TypeScript**:
```bash
npm run build
```

### 4. **Inicie o servidor**:
```bash
npm start
```

Ou para desenvolvimento com auto-reload:
```bash
npm run dev
```

---

## ✅ Verificar se o Servidor Está Funcionando

Se o servidor iniciou corretamente, você verá:
```
Database connected!
Servidor rodando em http://localhost:3333
```

### Teste os Endpoints

**1. Teste básico** (sem banco de dados):
```bash
curl http://localhost:3333/api
```
Resposta esperada:
```json
{"mensagem": "Olá, seja bem-vindo!"}
```

**2. Listar clientes**:
```bash
curl http://localhost:3333/clientes
```

**3. Listar produtos**:
```bash
curl http://localhost:3333/produtos
```

**4. Listar pedidos**:
```bash
curl http://localhost:3333/pedidos
```

---

## 🔍 Diagnosticar Problemas

### Erro: "Não foi possível conectar ao banco de dados"

**Verificar**:
1. PostgreSQL está rodando?
   ```bash
   psql -U postgres
   ```

2. Credenciais estão corretas no `.env`?
   ```
   DB_USER=postgres
   DB_PASSWORD=admin
   DB_HOST=localhost
   DB_PORT=5432
   DB_NAME=farmacia-didatica
   ```

3. Banco de dados existe?
   ```bash
   psql -U postgres -l | grep farmacia
   ```

4. Tabelas foram criadas?
   ```bash
   psql -U postgres -d farmacia-didatica -c "\dt"
   ```

### Erro: 404 na rota específica

**Possíveis causas**:
- URL digitada errada (verifique a grafia)
- Servidor não está respondendo (não iniciou)
- Banco de dados não conectou (veja logs do servidor)

**Exemplo de URLs válidas**:
- GET `http://localhost:3333/api` ✅
- GET `http://localhost:3333/clientes` ✅
- GET `http://localhost:3333/produtos` ✅
- GET `http://localhost:3333/pedidos` ✅
- GET `http://localhost:3333/itens/:idVenda` ✅

---

## 📋 Resumo das Rotas

| Método | Rota | Descrição |
|--------|------|-----------|
| GET | `/api` | Teste básico |
| GET/POST | `/clientes` | Listar/criar clientes |
| GET/POST | `/produtos` | Listar/criar produtos |
| GET | `/produtos/alerta` | Produtos com baixo estoque |
| PUT | `/produtos/:idProduto` | Atualizar produto |
| GET/POST | `/pedidos` | Listar/criar pedidos |
| GET | `/pedidos/:id` | Detalhes do pedido |
| GET/POST | `/pedidos/:id/itens` | Itens do pedido |
| GET/POST/PUT/DELETE | `/itens/:idVenda/:idProduto` | Crud de itens |

---

## 🔧 Verificacao de Imports

Todos os imports foram validados:
- ✅ Routes importa Controllers corretamente
- ✅ Controllers herdam corretamente de Models
- ✅ Models importam DatabaseModel como singleton
- ✅ Server monta express com CORS e JSON

**Próximo passo**: Teste executando `npm start` e use o Postman ou curl para verificar as rotas!
