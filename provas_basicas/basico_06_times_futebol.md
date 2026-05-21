# ⚽ PROVA — API de Times de Futebol
**Nível:** Básico | **Tempo:** 2h30 | **Pontuação:** 100 pts

---

## 🎯 Contexto

API REST em PHP puro para gerenciar informações de times de futebol.  
Padrão: **MVC + DAO + Service + MySQL**.

---

## 📐 Entidades e Relacionamentos

```
paises    (1) ──< times      (N)
estadios  (1) ──< times      (N)
times     (1) ──< titulos    (N)
times     (1) ──< elencos    (N)   [temporada atual]
competicoes(1) ──< titulos   (N)
```

---

## 🗄️ QUESTÃO 1 — Banco de Dados (25 pts)

### 1.1 — Crie as tabelas:

```sql
-- paises: id, nome, continente ENUM('america_sul','america_norte','europa','africa','asia','oceania')
-- estadios: id, nome, cidade, capacidade INT, ano_inauguracao INT
-- times: id, nome, apelido, ano_fundacao INT, cores VARCHAR(50),
--        estadio_id, pais_id, divisao ENUM('primeira','segunda','terceira'),
--        ativo TINYINT(1)
-- competicoes: id, nome (ex: Libertadores, Copa do Brasil), tipo ENUM('nacional','continental','mundial')
-- titulos: id, time_id, competicao_id, ano INT, observacao
-- elencos: id, time_id, jogador_nome, posicao ENUM('goleiro','defensor','meio_campo','atacante'),
--          nacionalidade, numero_camisa INT, titular TINYINT(1) DEFAULT 0
```

### 1.2 — Queries:

**a)** Total de títulos por time (todos, mesmo sem título com LEFT JOIN)

```sql
-- Colunas: time, ano_fundacao, total_titulos
-- ORDER BY total_titulos DESC
```

**b)** Títulos por competição com time campeão mais recente

```sql
-- Colunas: competicao, tipo, total_edicoes, ultimo_campeao, ano_mais_recente
```

**c)** Média de capacidade dos estádios por continente

```sql
-- Colunas: continente, total_estadios, capacidade_media, maior_estadio
```

**d)** Distribuição do elenco por posição para cada time

```sql
-- Colunas: time, posicao, total_jogadores, total_titulares
-- GROUP BY time_id, posicao
```

**e)** Times com mais títulos por tipo de competição

```sql
-- Colunas: tipo_competicao, time, total_titulos_neste_tipo
```

---

## 📁 QUESTÃO 2 — Estrutura (5 pts)

```
futebol-api/
├── config/Database.php
├── models/Time.php, Titulo.php
├── dao/TimeDAO.php, TituloDAO.php
├── services/TimeService.php
├── controllers/TimeController.php
├── public/index.php
└── public/.htaccess
```

---

## 🧱 QUESTÃO 3 — Model (10 pts)

### Crie `models/Time.php`:

```php
class Time {
    private int $id;
    private string $nome;
    private string $apelido;
    private int $anoFundacao;
    private string $cores;
    private int $estadioId;
    private int $paisId;
    private string $divisao;

    // Retorna quantos anos de fundação: "Fundado há 112 anos (1910)"
    public function getIdadeFormatada(): string;

    // "1ª Divisão" | "2ª Divisão" | "3ª Divisão"
    public function getDivisaoLabel(): string;

    public function toArray(): array;
    public static function fromArray(array $data): self;
}
```

---

## 🗃️ QUESTÃO 4 — DAO (25 pts)

### Crie `dao/TimeDAO.php`:

```php
public function findAll(): array;
public function findById(int $id): ?Time;
public function save(Time $time): bool;
public function update(Time $time): bool;
public function delete(int $id): bool;

// JOIN com paises, estadios e COUNT de titulos
public function findComDetalhes(): array;

// Times por país
public function findByPais(int $paisId): array;

// SELECT t.nome, COUNT(ti.id) as total_titulos
// LEFT JOIN titulos ON time_id GROUP BY time_id ORDER BY total_titulos DESC
public function rankingPorTitulos(): array;

// Conta elenco por posição para um time
// GROUP BY posicao
public function distribuicaoElenco(int $timeId): array;

// Títulos agrupados por tipo de competição
public function titulosPorTipoCompeticao(int $timeId): array;
```

---

## ⚙️ QUESTÃO 5 — Service (15 pts)

### Crie `services/TimeService.php`:

```php
// Regras: nome obrigatório, ano_fundacao entre 1850 e ano atual
// estadio e pais devem existir
public function cadastrar(array $dados): array;

// Registra título: competição e time devem existir, ano deve ser válido
public function registrarTitulo(int $timeId, int $competicaoId, int $ano): array;

// Retorna ranking dos N times mais vencedores
public function ranking(int $top = 10): array;

// Agrupa times por continente com total de títulos de cada grupo
public function timesPorContinente(): array;
```

---

## 🌐 QUESTÃO 6 — Rotas (15 pts)

| Método | Rota | Ação |
|---|---|---|
| GET | `/times` | Listar todos |
| GET | `/times/{id}` | Detalhes |
| POST | `/times` | Cadastrar |
| PUT | `/times/{id}` | Atualizar |
| DELETE | `/times/{id}` | Remover |
| GET | `/times/{id}/titulos` | Títulos do time |
| GET | `/times/{id}/elenco` | Elenco |
| POST | `/times/{id}/titulos` | Registrar título |
| GET | `/paises/{id}/times` | Times por país |
| GET | `/relatorios/ranking` | Ranking por títulos |
| GET | `/relatorios/por-continente` | Por continente |
| GET | `/relatorios/elenco/{id}` | Distribuição elenco |

---

## 🔡 QUESTÃO 7 — Manipulação de Arrays (5 pts)

```php
$times = [
    ['nome' => 'flamengo',          'pais' => 'brasil',    'titulos' => 8,  'ano_fundacao' => 1895, 'divisao' => 'primeira'],
    ['nome' => 'boca juniors',      'pais' => 'argentina', 'titulos' => 6,  'ano_fundacao' => 1905, 'divisao' => 'primeira'],
    ['nome' => 'river plate',       'pais' => 'argentina', 'titulos' => 4,  'ano_fundacao' => 1901, 'divisao' => 'primeira'],
    ['nome' => 'sao paulo',         'pais' => 'brasil',    'titulos' => 3,  'ano_fundacao' => 1930, 'divisao' => 'primeira'],
    ['nome' => 'atletico mineiro',  'pais' => 'brasil',    'titulos' => 1,  'ano_fundacao' => 1908, 'divisao' => 'primeira'],
    ['nome' => 'nacional',          'pais' => 'uruguai',   'titulos' => 3,  'ano_fundacao' => 1899, 'divisao' => 'primeira'],
];
```

**a)** Converta todos os nomes para Title Case

**b)** Agrupe por `pais` com `array_reduce` e some o total de títulos por país

**c)** Ordene por `titulos` decrescente e `ano_fundacao` crescente (em caso de empate) com `usort`

**d)** Calcule a idade de cada time a partir de `ano_fundacao` e adicione como chave `anos_existencia` com `array_map`

**e)** Converta chaves de snake_case para camelCase

