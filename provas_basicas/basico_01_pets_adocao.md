# 🐱 PROVA — API de Pets para Adoção
**Nível:** Básico | **Tempo:** 2h30 | **Pontuação:** 100 pts

---

## 🎯 Contexto

API REST em PHP puro para cadastro de animais disponíveis para adoção.  
Padrão obrigatório: **MVC + DAO + Service + MySQL**.

---

## 📐 Entidades e Relacionamentos

```
especies (1) ──< animais (N)
abrigos  (1) ──< animais (N)
animais  (1) ──< fotos   (N)
animais  (N) >──< caracteristicas (N)  [animal_caracteristica]
```

---

## 🗄️ QUESTÃO 1 — Banco de Dados (25 pts)

### 1.1 — Crie as tabelas:

```sql
-- especies: id, nome, descricao
-- abrigos:  id, nome, cidade, telefone, email, ativo TINYINT(1)
-- animais:  id, nome, idade_meses INT, sexo ENUM('M','F'),
--           porte ENUM('mini','pequeno','medio','grande'),
--           status ENUM('disponivel','adotado','em_tratamento'),
--           especie_id, abrigo_id, criado_em DATETIME DEFAULT NOW()
-- fotos:    id, animal_id, url, principal TINYINT(1) DEFAULT 0
-- caracteristicas: id, nome
-- animal_caracteristica: animal_id, caracteristica_id
```

**Requisitos:**
- PKs `INT AUTO_INCREMENT`, FKs com `ON DELETE CASCADE`
- Insira ao menos 3 registros por tabela (seeds)

### 1.2 — Escreva as queries SQL:

**a)** Quantidade de animais por espécie

```sql
-- Colunas esperadas: especie, total
-- Dica: use GROUP BY + COUNT(*)
```

**b)** Média de idade (meses) dos animais disponíveis, agrupada por porte

```sql
-- Colunas esperadas: porte, media_idade_meses
-- Filtre apenas status = 'disponivel'
```

**c)** Abrigo com mais animais cadastrados (apenas o primeiro)

```sql
-- Colunas esperadas: abrigo, cidade, total_animais
```

**d)** Liste cada animal com o nome de sua espécie e abrigo (JOIN triplo)

```sql
-- Colunas esperadas: animal, especie, abrigo, status
```

**e)** Total de animais por status por abrigo

```sql
-- Colunas esperadas: abrigo, status, total
-- GROUP BY com duas colunas
```

---

## 📁 QUESTÃO 2 — Estrutura (5 pts)

Monte a estrutura de pastas e crie o `.htaccess` que redireciona tudo para `index.php`.

```
pets-api/
├── config/Database.php
├── models/Animal.php
├── dao/AnimalDAO.php
├── services/AnimalService.php
├── controllers/AnimalController.php
├── helpers/StringHelper.php
├── public/index.php
└── public/.htaccess
```

Conteúdo do `.htaccess`:
```apache
Options -MultiViews
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^ index.php [QSA,L]
```

---

## 🧱 QUESTÃO 3 — Model (10 pts)

### Crie `models/Animal.php`:

```php
class Animal {
    private int $id;
    private string $nome;
    private int $idadeMeses;
    private string $sexo;
    private string $porte;
    private string $status;
    private int $especieId;
    private int $abrigoId;

    // Getters e setters para todas as propriedades

    // Retorna: "2 anos e 3 meses" | "8 meses" | "1 ano"
    public function getIdadeFormatada(): string;

    public function estaDisponivel(): bool;

    // Chaves em snake_case
    public function toArray(): array;

    public static function fromArray(array $data): self;
}
```

---

## 🗃️ QUESTÃO 4 — DAO (25 pts)

### Crie `dao/AnimalDAO.php`:

```php
public function findAll(): array;
public function findById(int $id): ?Animal;
public function save(Animal $animal): bool;
public function update(Animal $animal): bool;
public function delete(int $id): bool;

// JOIN em especies e abrigos, só status='disponivel'
public function findDisponiveis(): array;

public function findByEspecie(int $especieId): array;

// SELECT status, COUNT(*) as total FROM animais GROUP BY status
public function countByStatus(): array;

// SELECT porte, ROUND(AVG(idade_meses),1) as media FROM animais GROUP BY porte
public function mediaIdadePorPorte(): array;

// JOIN com animal_caracteristica e caracteristicas
public function findComCaracteristicas(int $id): array;
```

---

## ⚙️ QUESTÃO 5 — Service (15 pts)

### Crie `services/AnimalService.php`:

```php
// Regras: nome não vazio, idade_meses entre 0 e 300, abrigo ativo
public function cadastrar(array $dados): array;

// Regra: só adota se status = 'disponivel', muda para 'adotado'
public function adotar(int $id): array;

// Usa countByStatus() do DAO e retorna array formatado
public function resumoPorStatus(): array;

// Retorna disponíveis ordenados do mais novo ao mais velho
public function listarDisponiveis(): array;
```

---

## 🌐 QUESTÃO 6 — Rotas e Controller (15 pts)

| Método | Rota | Ação |
|---|---|---|
| GET | `/animais` | Listar todos |
| GET | `/animais/{id}` | Buscar por ID |
| POST | `/animais` | Cadastrar |
| PUT | `/animais/{id}` | Atualizar |
| DELETE | `/animais/{id}` | Remover |
| POST | `/animais/{id}/adotar` | Adotar |
| GET | `/animais/disponiveis` | Só disponíveis |
| GET | `/especies/{id}/animais` | Por espécie |
| GET | `/relatorios/por-status` | COUNT por status |
| GET | `/relatorios/media-idade` | Média de idade por porte |

Todos os endpoints retornam:
```json
{ "success": true, "data": {}, "message": "string" }
```

---

## 🔡 QUESTÃO 7 — Manipulação de Arrays (5 pts)

```php
$animais = [
    ['nome' => 'mr bigodes', 'especie' => 'gato', 'idade_meses' => 8,  'porte' => 'pequeno', 'status' => 'disponivel'],
    ['nome' => 'bolinha',    'especie' => 'cao',  'idade_meses' => 36, 'porte' => 'medio',   'status' => 'adotado'],
    ['nome' => 'fofinha',    'especie' => 'gato', 'idade_meses' => 14, 'porte' => 'pequeno', 'status' => 'disponivel'],
    ['nome' => 'rex',        'especie' => 'cao',  'idade_meses' => 60, 'porte' => 'grande',  'status' => 'disponivel'],
    ['nome' => 'pipoca',     'especie' => 'cao',  'idade_meses' => 4,  'porte' => 'mini',    'status' => 'em_tratamento'],
];
```

**a)** Converta todos os nomes para Title Case (`mr bigodes` → `Mr Bigodes`) com `array_map`

**b)** Filtre apenas os disponíveis e ordene por `idade_meses` crescente com `usort`

**c)** Agrupe por espécie com `array_reduce`:
```php
// resultado: ['gato' => [...], 'cao' => [...]]
```

**d)** Converta as chaves de snake_case para camelCase em todos os elementos

**e)** Extraia apenas os nomes dos animais disponíveis em array simples

