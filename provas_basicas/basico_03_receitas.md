# 🍕 PROVA — API de Receitas
**Nível:** Básico | **Tempo:** 2h30 | **Pontuação:** 100 pts

---

## 🎯 Contexto

API REST em PHP puro para publicação e consulta de receitas culinárias.  
Padrão: **MVC + DAO + Service + MySQL**.

---

## 📐 Entidades e Relacionamentos

```
categorias   (1) ──< receitas     (N)
chefs        (1) ──< receitas     (N)
receitas     (1) ──< ingredientes (N)
receitas     (1) ──< passos       (N)
receitas     (N) >──< tags        (N)  [receita_tag]
```

---

## 🗄️ QUESTÃO 1 — Banco de Dados (25 pts)

### 1.1 — Crie as tabelas:

```sql
-- categorias: id, nome (ex: massas, sobremesas, grelhados), descricao
-- chefs:      id, nome, bio, especialidade, ativo TINYINT(1)
-- receitas:   id, nome, descricao, tempo_preparo_min INT,
--             porcoes INT, dificuldade ENUM('facil','medio','dificil'),
--             calorias_porcao INT, categoria_id, chef_id,
--             publicada TINYINT(1) DEFAULT 0, criado_em
-- ingredientes: id, receita_id, nome, quantidade, unidade (xícara, g, ml, unid)
-- passos:     id, receita_id, ordem INT, descricao TEXT
-- tags:       id, nome (ex: vegetariano, sem-gluten, rapido)
-- receita_tag: receita_id, tag_id
```

**Requisitos:** PKs `INT AUTO_INCREMENT`, FKs `ON DELETE CASCADE`, seeds com 3+ registros

### 1.2 — Escreva as queries:

**a)** Tempo médio de preparo por categoria e por dificuldade

```sql
-- Colunas: categoria, dificuldade, media_minutos, total_receitas
```

**b)** Chef com mais receitas publicadas e média de porções

```sql
-- Colunas: chef, total_receitas_publicadas, media_porcoes
-- Apenas receitas publicadas, top 1
```

**c)** Total de receitas por dificuldade com total de ingredientes médio

```sql
-- Colunas: dificuldade, total_receitas, media_ingredientes
-- Dica: subquery ou JOIN com COUNT
```

**d)** Receitas com menor caloria por porção por categoria (apenas publicadas)

```sql
-- Colunas: categoria, receita, calorias_porcao, dificuldade
```

**e)** Tags mais usadas (top 5 com contagem de receitas)

```sql
-- Colunas: tag, total_receitas
```

---

## 📁 QUESTÃO 2 — Estrutura (5 pts)

```
receitas-api/
├── config/Database.php
├── models/Receita.php, Ingrediente.php
├── dao/ReceitaDAO.php
├── services/ReceitaService.php
├── controllers/ReceitaController.php
├── public/index.php
└── public/.htaccess
```

---

## 🧱 QUESTÃO 3 — Model (10 pts)

### Crie `models/Receita.php`:

```php
class Receita {
    private int $id;
    private string $nome;
    private string $descricao;
    private int $tempoPrepMinutos;
    private int $porcoes;
    private string $dificuldade;
    private ?int $caloriasPorcao;
    private int $categoriaId;
    private int $chefId;
    private bool $publicada;

    // "1h 30min" | "45min" | "2h"
    public function getTempoFormatado(): string;

    // "Fácil" | "Médio" | "Difícil"
    public function getDificuldadeLabel(): string;

    public function estaPublicada(): bool;

    public function toArray(): array;     // snake_case
    public static function fromArray(array $data): self;
}
```

---

## 🗃️ QUESTÃO 4 — DAO (25 pts)

### Crie `dao/ReceitaDAO.php`:

```php
public function findAll(): array;
public function findById(int $id): ?Receita;
public function save(Receita $receita): bool;
public function update(Receita $receita): bool;
public function delete(int $id): bool;

// Apenas publicadas, com nome do chef e categoria
public function findPublicadas(): array;

// Busca por texto no nome ou descrição
public function buscar(string $termo): array;

// SELECT dificuldade, COUNT(*) as total, ROUND(AVG(tempo_preparo_min)) as media_tempo
// GROUP BY dificuldade
public function estatisticasPorDificuldade(): array;

// Receitas com JOIN em ingredientes, passos e tags
public function findCompleta(int $id): array;

// SELECT categoria, COUNT(*) as total FROM receitas JOIN categorias GROUP BY categoria_id
public function countPorCategoria(): array;
```

---

## ⚙️ QUESTÃO 5 — Service (15 pts)

### Crie `services/ReceitaService.php`:

```php
// Regras: nome obrigatório, tempo_preparo > 0, porcoes > 0
// Ao salvar: salva ingredientes e passos em sequência
public function publicar(array $dados, array $ingredientes, array $passos): array;

// Regra: receita deve estar publicada = 0 para poder publicar
public function ativar(int $id): array;

// Retorna receitas ordenadas por menor tempo de preparo, apenas publicadas
public function maisRapidas(int $limite = 5): array;

// Retorna contagem por categoria e dificuldade
public function resumoEstatistico(): array;
```

---

## 🌐 QUESTÃO 6 — Rotas e Controller (15 pts)

| Método | Rota | Ação |
|---|---|---|
| GET | `/receitas` | Listar publicadas |
| GET | `/receitas/{id}` | Detalhes completos |
| POST | `/receitas` | Criar |
| PUT | `/receitas/{id}` | Atualizar |
| DELETE | `/receitas/{id}` | Remover |
| POST | `/receitas/{id}/publicar` | Ativar receita |
| GET | `/receitas/busca?q=termo` | Busca por texto |
| GET | `/categorias/{id}/receitas` | Por categoria |
| GET | `/chefs/{id}/receitas` | Por chef |
| GET | `/relatorios/dificuldade` | Stats por dificuldade |
| GET | `/relatorios/categorias` | Count por categoria |

---

## 🔡 QUESTÃO 7 — Manipulação de Arrays (5 pts)

```php
$receitas = [
    ['nome' => 'bolo de cenoura',     'dificuldade' => 'facil',  'tempo_preparo_min' => 60,  'calorias_porcao' => 320, 'porcoes' => 8],
    ['nome' => 'lasanha bolonhesa',   'dificuldade' => 'medio',  'tempo_preparo_min' => 120, 'calorias_porcao' => 580, 'porcoes' => 6],
    ['nome' => 'frango grelhado',     'dificuldade' => 'facil',  'tempo_preparo_min' => 30,  'calorias_porcao' => 210, 'porcoes' => 2],
    ['nome' => 'risoto de cogumelos', 'dificuldade' => 'dificil','tempo_preparo_min' => 90,  'calorias_porcao' => 450, 'porcoes' => 4],
    ['nome' => 'mousse de chocolate', 'dificuldade' => 'medio',  'tempo_preparo_min' => 45,  'calorias_porcao' => 380, 'porcoes' => 6],
];
```

**a)** Converta todos os nomes para Title Case com `array_map`

**b)** Calcule a média de calorias por dificuldade usando `array_filter` + `array_column` + `array_sum`

**c)** Ordene por `tempo_preparo_min` crescente com `usort`

**d)** Adicione uma chave `calorias_totais` em cada receita (calorias_porcao × porcoes) com `array_map`

**e)** Converta chaves snake_case para camelCase em todos os elementos

