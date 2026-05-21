# 🐱 PROVA — API de Pets para Adoção
**Nível:** Básico | **Tempo:** 2h30 | **Pontuação:** 100 pts

---

## 🎯 Contexto

Desenvolva uma API REST em PHP puro para um abrigo de animais.  
O sistema permite cadastrar animais disponíveis para adoção, registrar interessados e acompanhar adoções realizadas.

---

## 📐 Entidades e Relacionamentos

```
especies (1) ──< animais (N)
animais (1) ──< adocoes (1)
adotantes (1) ──< adocoes (N)
animais (N) >──< caracteristicas (N)   [tabela: animal_caracteristica]
```

---

## 🗄️ QUESTÃO 1 — Banco de Dados (25 pts)

### 1.1 — Crie as tabelas:

**`especies`:** `id`, `nome` (ex: cão, gato, coelho), `descricao`, `porte` ENUM(`'pequeno'`,`'medio'`,`'grande'`)

**`animais`:** `id`, `nome`, `especie_id`, `idade_meses` INT, `sexo` ENUM(`'M'`,`'F'`), `peso_kg` DECIMAL(4,2), `foto_url`, `descricao`, `castrado` TINYINT(1) DEFAULT 0, `vacinado` TINYINT(1) DEFAULT 0, `status` ENUM(`'disponivel'`,`'reservado'`,`'adotado'`), `criado_em` TIMESTAMP DEFAULT CURRENT_TIMESTAMP

**`adotantes`:** `id`, `nome`, `cpf` (único), `email`, `telefone`, `cidade`, `estado`, `criado_em`

**`adocoes`:** `id`, `animal_id` (único), `adotante_id`, `data_adocao` DATE, `observacoes`, `criado_em`

**`caracteristicas`:** `id`, `nome` (ex: brincalhão, dócil, sociável)

**`animal_caracteristica`:** `animal_id`, `caracteristica_id`

### 1.2 — Insira dados de exemplo (seeds):

- 3 espécies
- 8 animais variados
- 4 adotantes
- 3 adoções realizadas
- 5 características

### 1.3 — Escreva as queries:

**a)** Liste todos os animais disponíveis com o nome da espécie e suas características separadas por vírgula (`GROUP_CONCAT`)

**b)** Conte quantos animais existem por espécie e por status — use `GROUP BY especie_id, status`

**c)** Mostre o total de adoções por mês do ano atual — `GROUP BY MONTH(data_adocao)`

**d)** Calcule a média de idade (em meses) e peso dos animais disponíveis, agrupado por espécie

**e)** Liste os adotantes que já adotaram mais de 1 animal — `GROUP BY adotante_id HAVING COUNT > 1`

---

## 📁 QUESTÃO 2 — Estrutura MVC (5 pts)

Monte a estrutura de pastas e crie o `.htaccess`:

```
pets-api/
├── config/Database.php
├── models/
├── dao/
├── services/
├── controllers/
├── helpers/
├── public/
│   ├── index.php
│   └── .htaccess
└── routes.php
```

---

## 🧱 QUESTÃO 3 — Model (10 pts)

### Crie `models/Animal.php`:

- Propriedades em **camelCase**: `$id`, `$nome`, `$especieId`, `$idadeMeses`, `$sexo`, `$pesoKg`, `$fotoUrl`, `$castrado`, `$vacinado`, `$status`
- Método `getIdadeFormatada()`: retorna `"3 meses"` ou `"1 ano e 2 meses"` conforme o valor de `$idadeMeses`
- Método `isCastrado()`: retorna bool
- Método `toArray()`: chaves em **snake_case**
- Método estático `fromArray(array $data): self`

---

## 🗃️ QUESTÃO 4 — DAO (25 pts)

### Crie `dao/AnimalDAO.php`:

```php
public function findAll(): array;
public function findById(int $id): ?Animal;
public function findDisponiveis(): array;               // status = 'disponivel'
public function findByEspecie(int $especieId): array;
public function findComCaracteristicas(int $id): array; // JOIN animal_caracteristica + caracteristicas
public function save(Animal $animal): bool;
public function update(Animal $animal): bool;
public function delete(int $id): bool;
public function atualizarStatus(int $id, string $status): bool;
public function contarPorStatus(): array;               // GROUP BY status
public function mediaPorEspecie(): array;               // AVG idade e peso, GROUP BY especie
```

---

## ⚙️ QUESTÃO 5 — Service (15 pts)

### Crie `services/AnimalService.php`:

```php
// Regra: nome obrigatório, idade_meses >= 0, peso_kg > 0
public function cadastrar(array $dados): array;

// Regra: só pode adotar animal com status 'disponivel'
// Muda status do animal para 'adotado' e registra adocao
public function registrarAdocao(int $animalId, int $adotanteId, string $dataAdocao): array;

// Retorna animais agrupados por espécie
public function listarPorEspecie(): array;

// Retorna estatísticas gerais: total por status, média de idade, total adotados no mês
public function estatisticas(): array;
```

---

## 🌐 QUESTÃO 6 — Rotas e Controller (15 pts)

### Rotas obrigatórias:

| Método | Rota | Ação |
|---|---|---|
| GET | `/animais` | Listar todos |
| GET | `/animais/{id}` | Buscar por ID com características |
| POST | `/animais` | Cadastrar |
| PUT | `/animais/{id}` | Atualizar |
| DELETE | `/animais/{id}` | Remover |
| GET | `/animais/disponiveis` | Listar disponíveis |
| POST | `/adocoes` | Registrar adoção |
| GET | `/especies/{id}/animais` | Animais por espécie |
| GET | `/estatisticas` | Métricas gerais |

### Crie `controllers/AnimalController.php` com todos os métodos retornando JSON.

---

## 🔡 QUESTÃO 7 — Manipulação de Arrays (5 pts)

```php
$animais = [
    ['nome' => 'mr bigodes', 'especie' => 'gato', 'idade_meses' => 14, 'peso_kg' => 3.5, 'status' => 'disponivel'],
    ['nome' => 'rex', 'especie' => 'cao', 'idade_meses' => 36, 'peso_kg' => 18.0, 'status' => 'adotado'],
    ['nome' => 'bolinha', 'especie' => 'cao', 'idade_meses' => 8, 'peso_kg' => 6.2, 'status' => 'disponivel'],
    ['nome' => 'fofinha', 'especie' => 'gato', 'idade_meses' => 3, 'peso_kg' => 1.1, 'status' => 'reservado'],
    ['nome' => 'pipoca', 'especie' => 'coelho', 'idade_meses' => 6, 'peso_kg' => 0.9, 'status' => 'disponivel'],
];
```

**a)** Converta todos os `nome` para **Title Case** (`mr bigodes` → `Mr Bigodes`)

**b)** Filtre apenas os disponíveis e ordene por `peso_kg` crescente com `usort`

**c)** Agrupe por `especie` usando `array_reduce`

**d)** Converta chaves de snake_case para camelCase em todos os elementos

**e)** Calcule a média de `idade_meses` por espécie

---

## ✅ Critérios de Avaliação

| Critério | Peso |
|---|---|
| SQL com GROUP BY e agregações | 25% |
| Model com métodos utilitários | 10% |
| DAO completo com prepared statements | 25% |
| Service com regras de negócio | 15% |
| Rotas e controller com JSON | 15% |
| Manipulação de arrays | 5% |
| Estrutura de pastas + .htaccess | 5% |
