# 🎬 PROVA — API de Filmes
**Nível:** Básico | **Tempo:** 2h30 | **Pontuação:** 100 pts

---

## 🎯 Contexto

API REST em PHP puro para catálogo de filmes com avaliações e premiações.  
Padrão: **MVC + DAO + Service + MySQL**.

---

## 📐 Entidades e Relacionamentos

```
diretores   (1) ──< filmes      (N)
filmes      (1) ──< avaliacoes  (N)
usuarios    (1) ──< avaliacoes  (N)
filmes      (N) >──< generos    (N)   [filme_genero]
filmes      (N) >──< premios    (N)   [filme_premio]
```

---

## 🗄️ QUESTÃO 1 — Banco de Dados (25 pts)

### 1.1 — Crie as tabelas:

```sql
-- diretores: id, nome, nacionalidade, data_nascimento DATE, bio TEXT
-- generos:   id, nome (Ação, Drama, Comédia, Terror, Ficção Científica...)
-- filmes:    id, titulo, titulo_original, ano INT, duracao_min INT,
--            classificacao ENUM('livre','10','12','14','16','18'),
--            sinopse TEXT, poster_url, diretor_id,
--            orcamento DECIMAL(15,2), bilheteria DECIMAL(15,2),
--            criado_em
-- usuarios:  id, nome, email UNIQUE
-- avaliacoes: id, filme_id, usuario_id, nota DECIMAL(3,1), comentario TEXT, criado_em
--             (nota de 0.0 a 10.0, UNIQUE(filme_id, usuario_id))
-- premios:   id, nome (Oscar, BAFTA, Globo de Ouro...), categoria, ano INT
-- filme_premio: filme_id, premio_id, ganhou TINYINT(1) DEFAULT 0
```

### 1.2 — Queries:

**a)** Nota média, maior e menor por gênero (apenas filmes com 3+ avaliações)

```sql
-- Colunas: genero, total_filmes, media_nota, maior_nota, menor_nota
-- HAVING COUNT(avaliacoes.id) >= 3
```

**b)** Diretores com maior bilheteria total e média por filme

```sql
-- Colunas: diretor, total_filmes, bilheteria_total, media_bilheteria
-- ORDER BY bilheteria_total DESC LIMIT 5
```

**c)** Filmes nomeados vs premiados por premiação

```sql
-- Colunas: premio, total_nomeados, total_ganhadores, percentual_ganhou
```

**d)** Evolução da nota média por ano de lançamento

```sql
-- Colunas: ano, total_filmes, media_nota, media_duracao_min
-- ORDER BY ano DESC
```

**e)** ROI (retorno sobre investimento) por filme: (bilheteria - orcamento) / orcamento * 100

```sql
-- Colunas: titulo, ano, orcamento_formatado, bilheteria_formatada, roi_percentual
-- Apenas filmes onde orcamento > 0 e bilheteria > 0
-- ORDER BY roi DESC LIMIT 10
```

---

## 📁 QUESTÃO 2 — Estrutura (5 pts)

```
filmes-api/
├── config/Database.php
├── models/Filme.php, Avaliacao.php
├── dao/FilmeDAO.php, AvaliacaoDAO.php
├── services/FilmeService.php
├── controllers/FilmeController.php
├── public/index.php
└── public/.htaccess
```

---

## 🧱 QUESTÃO 3 — Model (10 pts)

### Crie `models/Filme.php`:

```php
class Filme {
    private int $id;
    private string $titulo;
    private string $tituloOriginal;
    private int $ano;
    private int $duracaoMin;
    private string $classificacao;
    private ?float $orcamento;
    private ?float $bilheteria;
    private int $diretorId;

    // "2h 18min" | "58min" | "3h 05min"
    public function getDuracaoFormatada(): string;

    // Se orcamento e bilheteria existirem: retorna ROI em %
    public function getRoi(): ?float;

    // "US$ 250.000.000" (usa number_format)
    public function getBilheteriaFormatada(): string;

    public function toArray(): array;
    public static function fromArray(array $data): self;
}
```

---

## 🗃️ QUESTÃO 4 — DAO (25 pts)

### Crie `dao/FilmeDAO.php`:

```php
public function findAll(): array;
public function findById(int $id): ?Filme;
public function save(Filme $f): bool;
public function update(Filme $f): bool;
public function delete(int $id): bool;

// Busca por texto no titulo ou titulo_original
public function buscar(string $termo): array;

// JOIN com avaliacoes, traz media_nota
public function findComMediaNotas(): array;

// Filtra por genero, ano_min, ano_max, classificacao
public function filtrar(array $filtros): array;

// SELECT genero, COUNT(*), AVG(nota), AVG(duracao_min)
// JOIN filme_genero, generos, LEFT JOIN avaliacoes GROUP BY genero_id
public function estatisticasPorGenero(): array;

// Top N filmes por nota média com mínimo de X avaliações
public function topFilmes(int $minAvaliacoes = 5, int $top = 10): array;
```

---

## ⚙️ QUESTÃO 5 — Service (15 pts)

### Crie `services/FilmeService.php`:

```php
// Regras: titulo e ano obrigatórios, ano entre 1888 e ano_atual+2
// diretor deve existir
public function cadastrar(array $dados): array;

// Regra: nota entre 0 e 10, usuario não pode avaliar mesmo filme 2x
public function avaliar(int $filmeId, int $usuarioId, float $nota, string $comentario): array;

// Retorna filmes agrupados por gênero com nota média
public function catalogoPorGenero(): array;

// Top 10 filmes com maior bilheteria e seu ROI
public function maioresBilheterias(): array;
```

---

## 🌐 QUESTÃO 6 — Rotas (15 pts)

| Método | Rota | Ação |
|---|---|---|
| GET | `/filmes` | Listar com nota média |
| GET | `/filmes/{id}` | Detalhes |
| POST | `/filmes` | Cadastrar |
| PUT | `/filmes/{id}` | Atualizar |
| DELETE | `/filmes/{id}` | Remover |
| POST | `/filmes/{id}/avaliar` | Avaliar |
| GET | `/filmes/{id}/avaliacoes` | Ver avaliações |
| GET | `/filmes/busca?q=termo` | Busca |
| GET | `/diretores/{id}/filmes` | Por diretor |
| GET | `/generos/{id}/filmes` | Por gênero |
| GET | `/relatorios/por-genero` | Stats por gênero |
| GET | `/relatorios/bilheterias` | Maiores bilheterias |

---

## 🔡 QUESTÃO 7 — Manipulação de Arrays (5 pts)

```php
$filmes = [
    ['titulo' => 'o senhor dos aneis',       'ano' => 2001, 'duracao_min' => 178, 'nota' => 9.1, 'genero' => 'fantasia',         'bilheteria' => 871000000],
    ['titulo' => 'interestelar',             'ano' => 2014, 'duracao_min' => 169, 'nota' => 8.7, 'genero' => 'ficcao_cientifica', 'bilheteria' => 701000000],
    ['titulo' => 'parasita',                 'ano' => 2019, 'duracao_min' => 132, 'nota' => 8.6, 'genero' => 'drama',             'bilheteria' => 258000000],
    ['titulo' => 'vingadores guerra infinita','ano' => 2018, 'duracao_min' => 149, 'nota' => 8.4, 'genero' => 'acao',             'bilheteria' => 2048000000],
    ['titulo' => 'joker',                    'ano' => 2019, 'duracao_min' => 122, 'nota' => 8.5, 'genero' => 'drama',             'bilheteria' => 1079000000],
    ['titulo' => 'duna',                     'ano' => 2021, 'duracao_min' => 155, 'nota' => 8.0, 'genero' => 'ficcao_cientifica', 'bilheteria' => 401000000],
];
```

**a)** Converta todos os títulos para Title Case

**b)** Calcule a média de nota por gênero com `array_reduce`

**c)** Formate a `bilheteria` de cada item como `"US$ 871.000.000"` com `array_map`

**d)** Ordene por nota decrescente com `usort` e retorne apenas `titulo`, `ano` e `nota`

**e)** Converta chaves snake_case para camelCase em todos os elementos

