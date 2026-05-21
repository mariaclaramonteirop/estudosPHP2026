# 🧾 PROVA — API de Cardápio
**Nível:** Básico | **Tempo:** 2h30 | **Pontuação:** 100 pts

---

## 🎯 Contexto

API REST em PHP puro para gerenciar o cardápio digital de um estabelecimento.  
Padrão: **MVC + DAO + Service + MySQL**.

---

## 📐 Entidades e Relacionamentos

```
restaurantes (1) ──< cardapios    (N)   [versões de cardápio]
cardapios    (1) ──< pratos       (N)
categorias   (1) ──< pratos       (N)
pratos       (1) ──< ingredientes (N)
pratos       (N) >──< alergenos   (N)   [prato_alergeno]
```

---

## 🗄️ QUESTÃO 1 — Banco de Dados (25 pts)

### 1.1 — Crie as tabelas:

```sql
-- restaurantes: id, nome, ativo
-- cardapios:    id, restaurante_id, nome (ex: almoço, jantar, brunch),
--               ativo TINYINT(1), criado_em
-- categorias:   id, nome ENUM('entrada','prato_principal','sobremesa','bebida','porcao'),
--               descricao, ordem INT
-- pratos:       id, cardapio_id, categoria_id, nome, descricao,
--               preco DECIMAL(8,2), calorias INT,
--               disponivel TINYINT(1) DEFAULT 1,
--               destaque TINYINT(1) DEFAULT 0,
--               tempo_preparo_min INT
-- ingredientes: id, prato_id, nome, quantidade, unidade, observacao
-- alergenos:    id, nome (ex: gluten, lactose, frutos_do_mar, nozes)
-- prato_alergeno: prato_id, alergeno_id
```

### 1.2 — Queries:

**a)** Preço médio, mínimo e máximo por categoria

```sql
-- Colunas: categoria, total_pratos, preco_medio, preco_min, preco_max
```

**b)** Pratos em destaque ordenados por categoria e preço

```sql
-- Colunas: categoria, prato, preco, calorias, tempo_preparo_min
-- Apenas disponivel = 1 e destaque = 1
```

**c)** Total de calorias disponíveis por categoria (soma)

```sql
-- Colunas: categoria, total_pratos_disponiveis, soma_calorias, media_calorias
```

**d)** Alergênios mais presentes no cardápio (top 5)

```sql
-- Colunas: alergeno, total_pratos_com_este_alergeno
```

**e)** Tempo médio de preparo por categoria (apenas pratos disponíveis)

```sql
-- Colunas: categoria, media_tempo_min, menor_tempo, maior_tempo
```

---

## 📁 QUESTÃO 2 — Estrutura (5 pts)

```
cardapio-api/
├── config/Database.php
├── models/Prato.php, Cardapio.php
├── dao/PratoDAO.php, CardapioDAO.php
├── services/PratoService.php
├── controllers/PratoController.php
├── public/index.php
└── public/.htaccess
```

---

## 🧱 QUESTÃO 3 — Model (10 pts)

### Crie `models/Prato.php`:

```php
class Prato {
    private int $id;
    private int $cardapioId;
    private int $categoriaId;
    private string $nome;
    private string $descricao;
    private float $preco;
    private ?int $calorias;
    private bool $disponivel;
    private bool $destaque;
    private ?int $tempoPrepMin;

    // "R$ 38,90"
    public function getPrecoFormatado(): string;

    // "~30 min" | "1h 10min"
    public function getTempoFormatado(): string;

    // Retorna ícone da categoria: "🥗" entrada | "🍽️" prato | "🍰" sobremesa | "🥤" bebida
    public function getCategoriaIcone(): string;

    public function estaDisponivel(): bool;
    public function toArray(): array;
    public static function fromArray(array $data): self;
}
```

---

## 🗃️ QUESTÃO 4 — DAO (25 pts)

### Crie `dao/PratoDAO.php`:

```php
public function findAll(): array;
public function findById(int $id): ?Prato;
public function save(Prato $prato): bool;
public function update(Prato $prato): bool;
public function delete(int $id): bool;

// Apenas disponivel = 1, com categoria e cardapio
public function findDisponiveis(int $cardapioId): array;

// Pratos em destaque
public function findDestaques(): array;

// SELECT categoria, COUNT(*) as total, AVG(preco), AVG(calorias)
// GROUP BY categoria_id ORDER BY ordem
public function estatisticasPorCategoria(): array;

// Busca com filtro de categoria e faixa de preço
public function filtrar(array $filtros): array;
// filtros: categoria_id, preco_min, preco_max, disponivel, sem_alergeno (id)

// JOIN com alergenos
public function findComAlergenos(int $id): array;
```

---

## ⚙️ QUESTÃO 5 — Service (15 pts)

### Crie `services/PratoService.php`:

```php
// Regras: nome obrigatório, preco > 0, cardapio e categoria devem existir
public function criar(array $dados): array;

// Alterna disponivel entre 0 e 1
public function alternarDisponibilidade(int $id): array;

// Retorna pratos agrupados por categoria para montagem do cardápio visual
public function cardapioCompleto(int $cardapioId): array;

// Retorna os N pratos mais baratos por categoria
public function maisEconomicos(int $porCategoria = 3): array;
```

---

## 🌐 QUESTÃO 6 — Rotas (15 pts)

| Método | Rota | Ação |
|---|---|---|
| GET | `/cardapios/{id}/pratos` | Pratos do cardápio |
| GET | `/pratos/{id}` | Detalhes |
| POST | `/pratos` | Criar |
| PUT | `/pratos/{id}` | Atualizar |
| DELETE | `/pratos/{id}` | Remover |
| PATCH | `/pratos/{id}/disponibilidade` | Ativar/desativar |
| GET | `/pratos/destaques` | Em destaque |
| GET | `/categorias/{id}/pratos` | Por categoria |
| GET | `/relatorios/por-categoria` | Stats por categoria |
| GET | `/relatorios/alergenos` | Alergênios mais comuns |
| GET | `/relatorios/tempo-preparo` | Tempo médio por cat. |

---

## 🔡 QUESTÃO 7 — Manipulação de Arrays (5 pts)

```php
$pratos = [
    ['nome' => 'bruschetta ao tomate', 'categoria' => 'entrada',        'preco' => 28.90, 'calorias' => 180, 'tempo_preparo_min' => 10],
    ['nome' => 'file ao molho madeira','categoria' => 'prato_principal', 'preco' => 89.90, 'calorias' => 650, 'tempo_preparo_min' => 35],
    ['nome' => 'petit gateau',         'categoria' => 'sobremesa',       'preco' => 32.00, 'calorias' => 420, 'tempo_preparo_min' => 15],
    ['nome' => 'salmao grelhado',      'categoria' => 'prato_principal', 'preco' => 95.00, 'calorias' => 480, 'tempo_preparo_min' => 25],
    ['nome' => 'limonada suica',       'categoria' => 'bebida',          'preco' => 18.00, 'calorias' => 120, 'tempo_preparo_min' => 5],
    ['nome' => 'carpaccio',            'categoria' => 'entrada',         'preco' => 45.00, 'calorias' => 220, 'tempo_preparo_min' => 8],
];
```

**a)** Converta todos os nomes para Title Case

**b)** Agrupe por `categoria` com `array_reduce` e calcule o preço médio de cada grupo

**c)** Ordene por `preco` crescente e adicione a chave `preco_formatado` = `"R$ 28,90"`

**d)** Filtre apenas `prato_principal` e calcule total de calorias com `array_sum` + `array_column`

**e)** Converta chaves snake_case para camelCase em todos os elementos

