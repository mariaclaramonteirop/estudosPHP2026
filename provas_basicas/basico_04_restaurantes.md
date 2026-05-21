# 🍽️ PROVA — API de Restaurantes
**Nível:** Básico | **Tempo:** 2h30 | **Pontuação:** 100 pts

---

## 🎯 Contexto

API REST em PHP puro para cadastro e avaliação de restaurantes.  
Padrão: **MVC + DAO + Service + MySQL**.

---

## 📐 Entidades e Relacionamentos

```
tipos_culinaria (1) ──< restaurantes (N)
restaurantes    (1) ──< horarios     (N)
restaurantes    (1) ──< avaliacoes   (N)
usuarios        (1) ──< avaliacoes   (N)
restaurantes    (N) >──< comodidades (N) [restaurante_comodidade]
```

---

## 🗄️ QUESTÃO 1 — Banco de Dados (25 pts)

### 1.1 — Crie as tabelas:

```sql
-- tipos_culinaria: id, nome (ex: italiana, japonesa, brasileira)
-- restaurantes: id, nome, tipo_culinaria_id, endereco, cidade, telefone,
--               preco_medio DECIMAL(6,2),
--               faixa_preco ENUM('economico','moderado','caro','luxo'),
--               ativo TINYINT(1), criado_em
-- horarios: id, restaurante_id, dia_semana ENUM('seg','ter','qua','qui','sex','sab','dom'),
--           abertura TIME, fechamento TIME, fechado TINYINT(1) DEFAULT 0
-- usuarios: id, nome, email UNIQUE
-- avaliacoes: id, restaurante_id, usuario_id, nota TINYINT (1-5),
--             comentario TEXT, criado_em
-- comodidades: id, nome (ex: wifi, estacionamento, delivery, reservas)
-- restaurante_comodidade: restaurante_id, comodidade_id
```

**Requisitos:** PKs `AUTO_INCREMENT`, FKs `ON DELETE CASCADE`, seeds 3+ por tabela

### 1.2 — Queries:

**a)** Média de nota e total de avaliações por restaurante (apenas com 2+ avaliações)

```sql
-- Colunas: restaurante, cidade, media_nota, total_avaliacoes
-- Dica: HAVING COUNT(*) >= 2
```

**b)** Top 5 restaurantes por nota média por tipo de culinária

```sql
-- Colunas: tipo_culinaria, restaurante, media_nota
```

**c)** Distribuição de avaliações por nota (quantos deram 1, 2, 3, 4, 5 estrelas)

```sql
-- Colunas: nota, total_avaliacoes, percentual
-- Dica: COUNT(*) * 100.0 / SUM(COUNT(*)) OVER()  ou subquery
```

**d)** Restaurantes abertos num determinado dia com nota acima de 4

```sql
-- Filtre por dia_semana = 'sex' (sexta) e fechado = 0
-- Colunas: restaurante, abertura, fechamento, media_nota
```

**e)** Contagem de restaurantes por faixa de preço e média de nota de cada faixa

```sql
-- Colunas: faixa_preco, total_restaurantes, media_nota_faixa
```

---

## 📁 QUESTÃO 2 — Estrutura (5 pts)

```
restaurantes-api/
├── config/Database.php
├── models/Restaurante.php, Avaliacao.php
├── dao/RestauranteDAO.php, AvaliacaoDAO.php
├── services/RestauranteService.php
├── controllers/RestauranteController.php
├── public/index.php
└── public/.htaccess
```

---

## 🧱 QUESTÃO 3 — Model (10 pts)

### Crie `models/Restaurante.php`:

```php
class Restaurante {
    private int $id;
    private string $nome;
    private int $tipoCulinariaId;
    private string $endereco;
    private string $cidade;
    private float $precoMedio;
    private string $faixaPreco;
    private bool $ativo;

    // "💰 Econômico" | "💰💰 Moderado" | "💰💰💰 Caro" | "💰💰💰💰 Luxo"
    public function getFaixaPrecoLabel(): string;

    public function estaAtivo(): bool;

    public function toArray(): array;
    public static function fromArray(array $data): self;
}
```

### Crie `models/Avaliacao.php`:

```php
class Avaliacao {
    // Propriedades: id, restauranteId, usuarioId, nota, comentario, criadoEm

    // Retorna "⭐⭐⭐⭐☆" conforme a nota (1-5)
    public function getEstrelasFormatadas(): string;

    public function toArray(): array;
    public static function fromArray(array $data): self;
}
```

---

## 🗃️ QUESTÃO 4 — DAO (25 pts)

### Crie `dao/RestauranteDAO.php`:

```php
public function findAll(): array;
public function findById(int $id): ?Restaurante;
public function save(Restaurante $r): bool;
public function update(Restaurante $r): bool;
public function delete(int $id): bool;

// JOIN com avaliacoes: retorna restaurante + media_nota + total_avaliacoes
public function findComMediaNotas(): array;

// Filtra por tipo_culinaria_id
public function findByTipoCulinaria(int $tipoId): array;

// SELECT nota, COUNT(*) as total GROUP BY nota ORDER BY nota
public function distribuicaoNotas(int $restauranteId): array;

// Top N restaurantes por média de nota
public function topPorNota(int $limite = 5): array;

// Agrupa por faixa_preco com COUNT e AVG(nota)
public function resumoPorFaixaPreco(): array;
```

---

## ⚙️ QUESTÃO 5 — Service (15 pts)

### Crie `services/RestauranteService.php`:

```php
// Regras: nome obrigatório, preco_medio > 0, tipo_culinaria deve existir
public function cadastrar(array $dados): array;

// Regra: nota deve ser entre 1 e 5, usuario não pode avaliar mesmo restaurante 2x
public function avaliar(int $restauranteId, int $usuarioId, int $nota, string $comentario): array;

// Retorna restaurantes com nota >= 4.0, ordenados por nota desc
public function melhoresAvaliados(): array;

// Retorna contagem e média por tipo de culinária
public function resumoPorCulinaria(): array;
```

---

## 🌐 QUESTÃO 6 — Rotas (15 pts)

| Método | Rota | Ação |
|---|---|---|
| GET | `/restaurantes` | Listar com média de nota |
| GET | `/restaurantes/{id}` | Detalhes |
| POST | `/restaurantes` | Cadastrar |
| PUT | `/restaurantes/{id}` | Atualizar |
| DELETE | `/restaurantes/{id}` | Remover |
| GET | `/restaurantes/{id}/avaliacoes` | Avaliações |
| POST | `/restaurantes/{id}/avaliar` | Avaliar |
| GET | `/restaurantes/top` | Melhores avaliados |
| GET | `/tipos-culinaria/{id}/restaurantes` | Por culinária |
| GET | `/relatorios/notas` | Distribuição de notas |
| GET | `/relatorios/faixa-preco` | Por faixa de preço |

---

## 🔡 QUESTÃO 7 — Manipulação de Arrays (5 pts)

```php
$restaurantes = [
    ['nome' => 'la bella italia',  'culinaria' => 'italiana',   'nota' => 4.8, 'preco_medio' => 85.00, 'faixa_preco' => 'caro'],
    ['nome' => 'sushi express',    'culinaria' => 'japonesa',   'nota' => 4.2, 'preco_medio' => 65.00, 'faixa_preco' => 'moderado'],
    ['nome' => 'churrascaria do ze','culinaria' => 'brasileira','nota' => 4.5, 'preco_medio' => 55.00, 'faixa_preco' => 'moderado'],
    ['nome' => 'burger fast',      'culinaria' => 'americana',  'nota' => 3.7, 'preco_medio' => 35.00, 'faixa_preco' => 'economico'],
    ['nome' => 'le cordon bleu',   'culinaria' => 'francesa',   'nota' => 4.9, 'preco_medio' => 250.00,'faixa_preco' => 'luxo'],
];
```

**a)** Converta todos os nomes para Title Case

**b)** Filtre apenas restaurantes com nota >= 4.5 e ordene por nota decrescente

**c)** Calcule a média de `preco_medio` por `faixa_preco` usando `array_reduce`

**d)** Adicione uma chave `nota_formatada` em cada item: `"★ 4.8"` usando `array_map`

**e)** Converta chaves snake_case para camelCase em todos os elementos

