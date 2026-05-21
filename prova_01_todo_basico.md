# 📝 PROVA 01 — Sistema de Tarefas (TODO)
**Nível:** Iniciante | **Tema:** Gerenciamento de Tarefas | **Tempo:** 3h

---

## 🎯 Contexto do Projeto

Você deve desenvolver uma **API REST em PHP puro** para gerenciar tarefas pessoais.  
A aplicação segue o padrão **MVC + DAO + Service**, com banco de dados **MySQL**.

---

## 📐 Entidades e Relacionamentos

```
usuarios (1) ──< tarefas (N)
categorias (1) ──< tarefas (N)
tarefas (1) ──< comentarios (N)
tarefas (N) >──< etiquetas (N)  [tabela: tarefa_etiqueta]
```

---

## 🗄️ QUESTÃO 1 — Banco de Dados (20 pts)

### 1.1 — Crie o script SQL completo com as 4 tabelas abaixo:

| Tabela | Campos obrigatórios |
|---|---|
| `usuarios` | id, nome, email, senha, criado_em |
| `categorias` | id, nome, cor_hex, usuario_id, criado_em |
| `tarefas` | id, titulo, descricao, status, prioridade, data_vencimento, usuario_id, categoria_id, criado_em, atualizado_em |
| `etiquetas` | id, nome, cor_hex |
| `comentarios` | id, texto, tarefa_id, usuario_id, criado_em |
| `tarefa_etiqueta` | tarefa_id, etiqueta_id |

**Requisitos:**
- Todas as PKs devem ser `INT AUTO_INCREMENT`
- Todas as FKs devem ter `ON DELETE CASCADE`
- O campo `status` deve ser `ENUM('pendente', 'em_progresso', 'concluida', 'cancelada')`
- O campo `prioridade` deve ser `ENUM('baixa', 'media', 'alta', 'urgente')`
- Insira ao menos **3 registros por tabela** como dados de exemplo (seeds)

---

## 📁 QUESTÃO 2 — Estrutura de Diretórios (5 pts)

### 2.1 — Monte a estrutura de pastas do projeto:

```
todo-api/
├── ??? (organize os diretórios conforme o padrão MVC + DAO + Service)
```

**Exigências:**
- Pasta `config/` com configuração do banco
- Pasta `models/` com as entidades
- Pasta `dao/` com acesso a dados
- Pasta `services/` com regras de negócio
- Pasta `controllers/` para receber as requisições
- Arquivo `routes.php` na raiz
- Arquivo `index.php` como entry point

---

## 🔌 QUESTÃO 3 — Conexão e Configuração (5 pts)

### 3.1 — Crie o arquivo `config/Database.php`:

- Use PDO com tratamento de exceção
- Implemente o padrão **Singleton**
- As credenciais devem vir de variáveis de ambiente (use `$_ENV` ou `getenv()`)

---

## 🧱 QUESTÃO 4 — Models (10 pts)

### 4.1 — Crie a classe `models/Tarefa.php`:

- Propriedades em **camelCase** (`$titulo`, `$datavVencimento`, `$usuarioId`, etc.)
- Métodos `getters` e `setters` para todas as propriedades
- Método `toArray()` que retorna array associativo com chaves em **snake_case**
- Método estático `fromArray(array $data)` que instancia o objeto a partir de array

### 4.2 — Crie a classe `models/Usuario.php`:
- Mesmos requisitos acima
- Adicione um método `toArraySemSenha()` que retorna os dados excluindo a senha

---

## 🗃️ QUESTÃO 5 — DAO (25 pts)

### 5.1 — Crie a interface `dao/InterfaceDAO.php` com os métodos:

```php
public function findAll(): array;
public function findById(int $id): ?object;
public function save(object $entity): bool;
public function update(object $entity): bool;
public function delete(int $id): bool;
```

### 5.2 — Crie a classe `dao/TarefaDAO.php` implementando a interface:

Implemente **todos** os métodos do CRUD usando PDO com **prepared statements**.

Além disso, implemente os seguintes métodos extras:

```php
// Busca tarefas de um usuário com filtro de status
public function findByUsuarioEStatus(int $usuarioId, string $status): array;

// Busca tarefas com suas etiquetas (JOIN)
public function findComEtiquetas(int $tarefaId): array;

// Conta tarefas por prioridade de um usuário
public function contarPorPrioridade(int $usuarioId): array;
```

---

## ⚙️ QUESTÃO 6 — Service (15 pts)

### 6.1 — Crie a classe `services/TarefaService.php`:

Implemente os métodos abaixo com as regras de negócio:

```php
// Regra: título não pode ser vazio e deve ter entre 3 e 100 caracteres
public function criar(array $dados): array;

// Regra: só pode concluir tarefa que está 'em_progresso'
public function concluir(int $id): array;

// Regra: retorna as tarefas ordenadas por prioridade (urgente > alta > media > baixa)
public function listarPorPrioridade(int $usuarioId): array;

// Regra: lança exceção se tarefa não pertencer ao usuário
public function deletar(int $id, int $usuarioIdLogado): bool;
```

---

## 🌐 QUESTÃO 7 — Rotas e Controllers (15 pts)

### 7.1 — Crie o arquivo `routes.php` com as seguintes rotas:

| Método | Rota | Ação |
|---|---|---|
| GET | `/tarefas` | Listar todas |
| GET | `/tarefas/{id}` | Buscar por ID |
| POST | `/tarefas` | Criar nova |
| PUT | `/tarefas/{id}` | Atualizar |
| DELETE | `/tarefas/{id}` | Deletar |
| GET | `/tarefas/{id}/comentarios` | Listar comentários |
| POST | `/tarefas/{id}/comentarios` | Adicionar comentário |
| GET | `/usuarios/{id}/tarefas` | Tarefas de um usuário |

### 7.2 — Crie o `controllers/TarefaController.php`:

- Método `index()`: retorna JSON com lista de tarefas
- Método `show(int $id)`: retorna JSON com tarefa ou erro 404
- Método `store()`: valida e cria tarefa, retorna 201 em sucesso
- Método `update(int $id)`: atualiza e retorna 200
- Método `destroy(int $id)`: deleta e retorna 204

---

## 🔡 QUESTÃO 8 — Manipulação de Arrays (5 pts)

### 8.1 — Dado o array abaixo, aplique as transformações pedidas:

```php
$tarefas = [
    ['titulo' => 'comprar leite', 'prioridade' => 'alta', 'status' => 'pendente'],
    ['titulo' => 'pagar conta de luz', 'prioridade' => 'urgente', 'status' => 'pendente'],
    ['titulo' => 'fazer exercícios', 'prioridade' => 'media', 'status' => 'concluida'],
    ['titulo' => 'ler livro', 'prioridade' => 'baixa', 'status' => 'pendente'],
    ['titulo' => 'reunião de trabalho', 'prioridade' => 'alta', 'status' => 'em_progresso'],
];
```

**a)** Filtre apenas as tarefas com status `pendente`

**b)** Ordene as tarefas por prioridade (urgente primeiro, baixa por último) sem usar função pronta de ordenação por valor — monte a lógica manualmente com `usort`

**c)** Transforme o array para que cada `titulo` fique em **PascalCase** (ex: `Comprar Leite`)

**d)** Extraia apenas os títulos das tarefas pendentes em um novo array com `array_map` + `array_filter`

**e)** Converta todas as chaves do array de snake_case para camelCase (ex: `data_vencimento` → `dataVencimento`)

---

## ✅ Critérios de Avaliação

| Critério | Peso |
|---|---|
| Banco de dados correto e funcional | 20% |
| Estrutura e organização de pastas | 5% |
| Conexão Singleton com PDO | 5% |
| Models com getters/setters/toArray | 10% |
| DAO completo com prepared statements | 25% |
| Service com regras de negócio | 15% |
| Rotas e controllers com JSON | 15% |
| Manipulação de arrays | 5% |

---

## 📌 Observações Finais

- Toda resposta da API deve ser em **JSON**
- Usar `header('Content-Type: application/json')`
- Tratar erros com HTTP status codes corretos
- **Não** usar frameworks (sem Laravel, Slim, etc.)
- Código deve ser compatível com **PHP 8.1+**
