# 🏃 PROVA — API de Jogadores de Futebol
**Nível:** Básico | **Tempo:** 2h30 | **Pontuação:** 100 pts

---

## 🎯 Contexto

API REST em PHP puro para gerenciar jogadores de futebol, suas transferências e estatísticas.  
Padrão: **MVC + DAO + Service + MySQL**.

---

## 📐 Entidades e Relacionamentos

```
paises      (1) ──< jogadores     (N)
times       (1) ──< contratos     (N)
jogadores   (1) ──< contratos     (N)   [histórico + atual]
jogadores   (1) ──< estatisticas  (N)   [por temporada]
posicoes    (1) ──< jogadores     (N)
jogadores   (N) >──< premiacoes   (N)   [jogador_premiacao]
```

---

## 🗄️ QUESTÃO 1 — Banco de Dados (25 pts)

### 1.1 — Crie as tabelas:

```sql
-- paises: id, nome, continente
-- posicoes: id, nome (Goleiro, Zagueiro...), abreviacao (GOL, ZAG...), setor ENUM('defesa','meio','ataque')
-- times: id, nome (simplificado — apenas id e nome)
-- jogadores: id, nome_completo, nome_usual, data_nascimento DATE,
--            pais_id, posicao_id, pe_dominante ENUM('direito','esquerdo','ambidestro'),
--            altura_cm INT, peso_kg DECIMAL(4,1), ativo TINYINT(1)
-- contratos: id, jogador_id, time_id, data_inicio DATE, data_fim DATE,
--            salario_mensal DECIMAL(12,2), titular TINYINT(1),
--            status ENUM('ativo','encerrado','emprestimo')
-- estatisticas: id, jogador_id, time_id, temporada VARCHAR(9),
--               jogos INT, gols INT, assistencias INT,
--               cartoes_amarelos INT, cartoes_vermelhos INT, minutos_jogados INT
-- premiacoes: id, nome, organizacao, ano INT
-- jogador_premiacao: jogador_id, premiacao_id, posicao_ranking INT
```

### 1.2 — Queries:

**a)** Artilheiros por temporada (top 5 por temporada)

```sql
-- Colunas: temporada, jogador, time, gols, assistencias
-- Dica: use subconsulta ou ROW_NUMBER com PARTITION BY temporada
-- Alternativa básica: GROUP BY temporada, jogador ORDER BY gols DESC
```

**b)** Média de gols e assistências por posição/setor

```sql
-- Colunas: setor, posicao, media_gols, media_assistencias, total_jogadores
```

**c)** Times que mais contrataram jogadores estrangeiros (de outros países)

```sql
-- Colunas: time, total_estrangeiros, nacionalidades_distintas
-- Dica: COUNT(DISTINCT pais_id)
```

**d)** Jogadores com mais de uma premiação, ordenados por total de prêmios

```sql
-- Colunas: jogador, nacionalidade, total_premiacoes
-- HAVING COUNT(*) > 1
```

**e)** Evolução de um jogador: gols e assistências por temporada (para um jogador_id específico)

```sql
-- Colunas: temporada, time, jogos, gols, assistencias, minutos_jogados
```

---

## 📁 QUESTÃO 2 — Estrutura (5 pts)

```
jogadores-api/
├── config/Database.php
├── models/Jogador.php, Contrato.php, Estatistica.php
├── dao/JogadorDAO.php, EstatisticaDAO.php
├── services/JogadorService.php
├── controllers/JogadorController.php
├── public/index.php
└── public/.htaccess
```

---

## 🧱 QUESTÃO 3 — Model (10 pts)

### Crie `models/Jogador.php`:

```php
class Jogador {
    private int $id;
    private string $nomeCompleto;
    private string $nomeUsual;
    private string $dataNascimento;
    private int $paisId;
    private int $posicaoId;
    private string $peDominante;
    private int $alturaCm;
    private float $pesoKg;

    // "25 anos" | "1 ano" | "33 anos"
    public function getIdade(): string;

    // "1,82m — 78,5kg"
    public function getMedidasFormatadas(): string;

    // "Pé direito" | "Pé esquerdo" | "Ambidestro"
    public function getPeLabel(): string;

    public function toArray(): array;
    public static function fromArray(array $data): self;
}
```

---

## 🗃️ QUESTÃO 4 — DAO (25 pts)

### Crie `dao/JogadorDAO.php`:

```php
public function findAll(): array;
public function findById(int $id): ?Jogador;
public function save(Jogador $j): bool;
public function update(Jogador $j): bool;
public function delete(int $id): bool;

// Retorna jogador com contrato ativo e nome do time atual
public function findComContratoAtivo(): array;

// Jogadores por pais
public function findByNacionalidade(int $paisId): array;

// SELECT posicao, setor, COUNT(*) as total, AVG(gols) as media_gols
// JOIN posicoes e estatisticas GROUP BY posicao_id
public function mediasEstatisticasPorPosicao(): array;

// Top N artilheiros de uma temporada específica
public function artilheiros(string $temporada, int $top = 10): array;

// Evolução histórica por temporada de um jogador
public function historicoEstatistico(int $jogadorId): array;
```

---

## ⚙️ QUESTÃO 5 — Service (15 pts)

### Crie `services/JogadorService.php`:

```php
// Regras: nome_completo obrigatório, data_nascimento válida,
// jogador deve ter ao menos 15 anos
public function cadastrar(array $dados): array;

// Regra: encerra contrato ativo anterior antes de criar novo
// data_inicio do novo não pode ser anterior à data_fim do anterior
public function transferir(int $jogadorId, int $timeId, array $dadosContrato): array;

// Retorna artilheiros agrupados por posição
public function artilheirosPorPosicao(string $temporada): array;

// Retorna o resumo da carreira: times, gols totais, títulos
public function resumoCarreira(int $jogadorId): array;
```

---

## 🌐 QUESTÃO 6 — Rotas (15 pts)

| Método | Rota | Ação |
|---|---|---|
| GET | `/jogadores` | Listar com contrato atual |
| GET | `/jogadores/{id}` | Detalhes |
| POST | `/jogadores` | Cadastrar |
| PUT | `/jogadores/{id}` | Atualizar |
| DELETE | `/jogadores/{id}` | Remover |
| POST | `/jogadores/{id}/transferir` | Transferir jogador |
| GET | `/jogadores/{id}/estatisticas` | Histórico por temporada |
| GET | `/jogadores/{id}/carreira` | Resumo de carreira |
| GET | `/times/{id}/elenco` | Jogadores do time |
| GET | `/relatorios/artilheiros` | Top artilheiros |
| GET | `/relatorios/por-posicao` | Médias por posição |

---

## 🔡 QUESTÃO 7 — Manipulação de Arrays (5 pts)

```php
$jogadores = [
    ['nome_usual' => 'gabriel barbosa', 'posicao' => 'atacante',    'gols' => 28, 'assistencias' => 9,  'jogos' => 42, 'nacionalidade' => 'brasileiro'],
    ['nome_usual' => 'casemiro',        'posicao' => 'meio_campo',   'gols' => 5,  'assistencias' => 8,  'jogos' => 38, 'nacionalidade' => 'brasileiro'],
    ['nome_usual' => 'endrick',         'posicao' => 'atacante',     'gols' => 15, 'assistencias' => 4,  'jogos' => 30, 'nacionalidade' => 'brasileiro'],
    ['nome_usual' => 'marquinhos',      'posicao' => 'defensor',     'gols' => 3,  'assistencias' => 1,  'jogos' => 35, 'nacionalidade' => 'brasileiro'],
    ['nome_usual' => 'alisson',         'posicao' => 'goleiro',      'gols' => 0,  'assistencias' => 0,  'jogos' => 30, 'nacionalidade' => 'brasileiro'],
    ['nome_usual' => 'vinicius junior', 'posicao' => 'atacante',     'gols' => 24, 'assistencias' => 11, 'jogos' => 40, 'nacionalidade' => 'brasileiro'],
];
```

**a)** Converta todos os `nome_usual` para Title Case

**b)** Calcule `participacoes_gols` (gols + assistencias) para cada jogador com `array_map`

**c)** Agrupe por `posicao` com `array_reduce`, somando gols e assistências de cada grupo

**d)** Ordene pelo maior número de participações em gols com `usort`

**e)** Converta chaves snake_case para camelCase

