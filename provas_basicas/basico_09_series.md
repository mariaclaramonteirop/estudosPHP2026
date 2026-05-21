# 📺 PROVA — API de Séries
**Nível:** Básico | **Tempo:** 2h30 | **Pontuação:** 100 pts

---

## 🎯 Contexto

API REST em PHP puro para catálogo de séries com temporadas, episódios e avaliações.  
Padrão: **MVC + DAO + Service + MySQL**.

---

## 📐 Entidades e Relacionamentos

```
plataformas (1) ──< series      (N)
series      (1) ──< temporadas  (N)
temporadas  (1) ──< episodios   (N)
series      (1) ──< avaliacoes  (N)
usuarios    (1) ──< avaliacoes  (N)
series      (N) >──< generos    (N)  [serie_genero]
```

---

## 🗄️ QUESTÃO 1 — Banco de Dados (25 pts)

### 1.1 — Crie as tabelas:

```sql
-- plataformas: id, nome (Netflix, Prime, Disney+, Max, Globoplay...), pais_origem
-- generos:     id, nome
-- series:      id, nome, ano_estreia INT, status ENUM('em_andamento','finalizada','cancelada','pausada'),
--              plataforma_id, nota_media DECIMAL(3,1), sinopse TEXT, poster_url, criado_em
-- temporadas:  id, serie_id, numero INT, ano INT,
--              total_episodios INT, disponivel TINYINT(1) DEFAULT 1
-- episodios:   id, temporada_id, numero INT, titulo, duracao_min INT,
--              sinopse, data_lancamento DATE
-- usuarios:    id, nome, email UNIQUE
-- avaliacoes:  id, serie_id, usuario_id, nota DECIMAL(3,1), comentario, criado_em
--              UNIQUE(serie_id, usuario_id)
-- serie_genero: serie_id, genero_id
```

### 1.2 — Queries:

**a)** Total de séries por plataforma e status, com nota média

```sql
-- Colunas: plataforma, status, total_series, nota_media
-- GROUP BY plataforma_id, status
```

**b)** Séries com mais episódios (total somando todas as temporadas)

```sql
-- Colunas: serie, plataforma, total_temporadas, total_episodios, status
-- ORDER BY total_episodios DESC LIMIT 10
```

**c)** Média de duração dos episódios por gênero

```sql
-- Colunas: genero, total_series, total_episodios, media_duracao_min
-- JOIN serie_genero, episodios, temporadas
```

**d)** Plataforma com maior nota média entre séries com 10+ avaliações

```sql
-- Colunas: plataforma, total_series_avaliadas, nota_media_geral
-- HAVING COUNT(avaliacoes) >= 10
```

**e)** Séries em andamento com temporada lançada no ano atual

```sql
-- Colunas: serie, plataforma, ultima_temporada, total_episodios_ultima_temp
-- WHERE status = 'em_andamento' AND temporadas.ano = YEAR(NOW())
```

---

## 📁 QUESTÃO 2 — Estrutura (5 pts)

```
series-api/
├── config/Database.php
├── models/Serie.php, Temporada.php, Episodio.php
├── dao/SerieDAO.php, TemporadaDAO.php
├── services/SerieService.php
├── controllers/SerieController.php
├── public/index.php
└── public/.htaccess
```

---

## 🧱 QUESTÃO 3 — Model (10 pts)

### Crie `models/Serie.php`:

```php
class Serie {
    private int $id;
    private string $nome;
    private int $anoEstreia;
    private string $status;
    private int $plataformaId;
    private float $notaMedia;

    // "🟢 Em andamento" | "✅ Finalizada" | "❌ Cancelada" | "⏸️ Pausada"
    public function getStatusLabel(): string;

    public function estaAtiva(): bool;
    // Retorna true se status = 'em_andamento'

    public function toArray(): array;
    public static function fromArray(array $data): self;
}
```

### Crie `models/Episodio.php`:

```php
class Episodio {
    // Propriedades: id, temporadaId, numero, titulo, duracaoMin, sinopse, dataLancamento

    // "S02E05 — Título do Episódio (42min)"
    // Recebe numero da temporada como parâmetro
    public function getCodigoFormatado(int $numeroTemporada): string;

    // "42 min" | "1h 22min"
    public function getDuracaoFormatada(): string;

    public function toArray(): array;
    public static function fromArray(array $data): self;
}
```

---

## 🗃️ QUESTÃO 4 — DAO (25 pts)

### Crie `dao/SerieDAO.php`:

```php
public function findAll(): array;
public function findById(int $id): ?Serie;
public function save(Serie $s): bool;
public function update(Serie $s): bool;
public function delete(int $id): bool;

// JOIN com plataforma, conta temporadas e total de episodios
public function findComDetalhes(): array;

// Séries com status = 'em_andamento'
public function findEmAndamento(): array;

// Busca por texto no nome
public function buscar(string $termo): array;

// SELECT plataforma, status, COUNT(*), AVG(nota_media)
// GROUP BY plataforma_id, status
public function estatisticasPorPlataformaEStatus(): array;

// Retorna serie + todas as temporadas + total de episodios por temporada
public function findComTemporadas(int $id): array;
```

---

## ⚙️ QUESTÃO 5 — Service (15 pts)

### Crie `services/SerieService.php`:

```php
// Regras: nome obrigatório, ano_estreia entre 1950 e ano atual +1
// plataforma deve existir
public function cadastrar(array $dados): array;

// Regra: nota entre 0 e 10, usuario não pode avaliar mesma série 2x
// Após avaliação, recalcular e atualizar nota_media na tabela series
public function avaliar(int $serieId, int $usuarioId, float $nota, string $comentario): array;

// Retorna séries agrupadas por plataforma com nota média
public function catalogoPorPlataforma(): array;

// Retorna top N mais bem avaliadas com 5+ avaliações
public function maisAvaliadas(int $top = 10): array;
```

---

## 🌐 QUESTÃO 6 — Rotas (15 pts)

| Método | Rota | Ação |
|---|---|---|
| GET | `/series` | Listar com detalhes |
| GET | `/series/{id}` | Detalhes |
| POST | `/series` | Cadastrar |
| PUT | `/series/{id}` | Atualizar |
| DELETE | `/series/{id}` | Remover |
| GET | `/series/{id}/temporadas` | Temporadas |
| GET | `/temporadas/{id}/episodios` | Episódios |
| POST | `/series/{id}/avaliar` | Avaliar |
| GET | `/series/em-andamento` | Em andamento |
| GET | `/series/busca?q=termo` | Busca |
| GET | `/plataformas/{id}/series` | Por plataforma |
| GET | `/relatorios/por-plataforma` | Stats por plataforma |
| GET | `/relatorios/mais-avaliadas` | Top por nota |

---

## 🔡 QUESTÃO 7 — Manipulação de Arrays (5 pts)

```php
$series = [
    ['nome' => 'breaking bad',       'plataforma' => 'netflix',  'status' => 'finalizada',    'temporadas' => 5,  'nota' => 9.5, 'genero' => 'drama'],
    ['nome' => 'the boys',           'plataforma' => 'prime',    'status' => 'em_andamento',  'temporadas' => 4,  'nota' => 8.7, 'genero' => 'acao'],
    ['nome' => 'house of the dragon','plataforma' => 'max',      'status' => 'em_andamento',  'temporadas' => 2,  'nota' => 8.5, 'genero' => 'fantasia'],
    ['nome' => 'dark',               'plataforma' => 'netflix',  'status' => 'finalizada',    'temporadas' => 3,  'nota' => 8.8, 'genero' => 'suspense'],
    ['nome' => 'mandalorian',        'plataforma' => 'disney',   'status' => 'em_andamento',  'temporadas' => 3,  'nota' => 8.6, 'genero' => 'acao'],
    ['nome' => 'squid game',         'plataforma' => 'netflix',  'status' => 'em_andamento',  'temporadas' => 2,  'nota' => 8.0, 'genero' => 'suspense'],
];
```

**a)** Converta todos os nomes para Title Case

**b)** Agrupe por `plataforma` com `array_reduce` e calcule nota média de cada plataforma

**c)** Filtre apenas `finalizada`, ordene por `nota` decrescente

**d)** Filtre apenas `em_andamento` e retorne `['nome' => ..., 'temporadas' => ..., 'nota' => ...]` com `array_map`

**e)** Converta chaves snake_case para camelCase em todos os elementos (`nome_completo` → `nomeCompleto`)

