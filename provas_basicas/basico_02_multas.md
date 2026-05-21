# 🚗 PROVA — API de Multas e Infrações de Trânsito
**Nível:** Básico | **Tempo:** 2h30 | **Pontuação:** 100 pts

---

## 🎯 Contexto

API REST em PHP puro para registro e consulta de infrações de trânsito.  
Padrão: **MVC + DAO + Service + MySQL**.

---

## 📐 Entidades e Relacionamentos

```
categorias_infracao (1) ──< tipos_infracao (N)
veiculos            (1) ──< multas         (N)
tipos_infracao      (1) ──< multas         (N)
condutores          (1) ──< multas         (N)
```

---

## 🗄️ QUESTÃO 1 — Banco de Dados (25 pts)

### 1.1 — Crie as tabelas:

```sql
-- categorias_infracao: id, nome (ex: velocidade, estacionamento, alcool)
-- tipos_infracao: id, codigo VARCHAR(10) UNIQUE, descricao, pontos INT,
--                valor DECIMAL(8,2), gravidade ENUM('leve','media','grave','gravissima'),
--                categoria_id
-- condutores: id, nome, cpf UNIQUE, cnh, validade_cnh DATE, pontos_atuais INT DEFAULT 0
-- veiculos:   id, placa UNIQUE, modelo, cor, ano, condutor_id
-- multas:     id, veiculo_id, tipo_infracao_id, condutor_id,
--             data_infracao DATETIME, local_infracao, observacoes,
--             status ENUM('pendente','paga','recorrida','cancelada'),
--             data_pagamento DATE, criado_em
```

**Requisitos:**
- PKs `INT AUTO_INCREMENT`, FKs com `ON DELETE RESTRICT`
- Insira ao menos 3 registros por tabela

### 1.2 — Escreva as queries:

**a)** Total de multas por gravidade e valor total arrecadado por gravidade

```sql
-- Colunas: gravidade, total_multas, valor_total, valor_medio
```

**b)** Top 3 condutores com mais pontos (somente multas com status != 'cancelada')

```sql
-- Colunas: condutor, cpf, total_pontos, total_multas
```

**c)** Quantidade de multas por mês no ano atual

```sql
-- Colunas: mes, total_multas, valor_total
-- Dica: MONTH(data_infracao), YEAR(data_infracao)
```

**d)** Tipos de infração mais aplicados (top 5)

```sql
-- Colunas: codigo, descricao, gravidade, total_aplicacoes, valor_total
```

**e)** Valor total de multas pendentes agrupado por categoria de infração

```sql
-- Colunas: categoria, total_pendentes, valor_total_pendente
```

---

## 📁 QUESTÃO 2 — Estrutura (5 pts)

```
multas-api/
├── config/Database.php
├── models/Multa.php, TipoInfracao.php
├── dao/MultaDAO.php, TipoInfracaoDAO.php
├── services/MultaService.php
├── controllers/MultaController.php
├── public/index.php
└── public/.htaccess
```

---

## 🧱 QUESTÃO 3 — Model (10 pts)

### Crie `models/Multa.php`:

```php
class Multa {
    private int $id;
    private int $veiculoId;
    private int $tipoInfracaoId;
    private int $condutorId;
    private string $dataInfracao;
    private string $localInfracao;
    private string $status;
    private ?string $dataPagamento;

    public function estaAtiva(): bool;
    // Retorna true se status = 'pendente' ou 'recorrida'

    public function podeSerPaga(): bool;
    // Retorna true apenas se status = 'pendente'

    public function toArray(): array;   // snake_case
    public static function fromArray(array $data): self;
}
```

### Crie `models/TipoInfracao.php`:

```php
class TipoInfracao {
    // Implemente getValorFormatado(): string → "R$ 293,47"
    // Implemente getGravidadeLabel(): string → "🔴 Gravíssima" / "🟡 Leve"
    public function toArray(): array;
    public static function fromArray(array $data): self;
}
```

---

## 🗃️ QUESTÃO 4 — DAO (25 pts)

### Crie `dao/MultaDAO.php`:

```php
public function findAll(): array;
public function findById(int $id): ?Multa;
public function save(Multa $multa): bool;
public function update(Multa $multa): bool;
public function delete(int $id): bool;

// JOIN com tipos_infracao, veiculos e condutores
public function findByCondutor(int $condutorId): array;

public function findPendentes(): array;

// SELECT status, COUNT(*) as total, SUM(valor) as valor_total
// JOIN com tipos_infracao GROUP BY status
public function totalPorStatus(): array;

// Agrupa por gravidade com COUNT e SUM(valor)
public function totalPorGravidade(): array;

// Multas do mes atual
public function findDoMesAtual(): array;
```

---

## ⚙️ QUESTÃO 5 — Service (15 pts)

### Crie `services/MultaService.php`:

```php
// Regras: veiculo e condutor devem existir, data_infracao não pode ser futura
public function registrar(array $dados): array;

// Regra: só paga se status = 'pendente', atualiza data_pagamento
// Também deve incrementar pontos_atuais do condutor
public function pagar(int $id): array;

// Regra: só recorre se status = 'pendente', muda para 'recorrida'
public function recorrer(int $id): array;

// Retorna resumo: total multas, total pendentes, valor pendente, top infração
public function dashboard(): array;
```

---

## 🌐 QUESTÃO 6 — Rotas e Controller (15 pts)

| Método | Rota | Ação |
|---|---|---|
| GET | `/multas` | Listar todas |
| GET | `/multas/{id}` | Buscar |
| POST | `/multas` | Registrar |
| PUT | `/multas/{id}` | Atualizar |
| DELETE | `/multas/{id}` | Remover |
| POST | `/multas/{id}/pagar` | Pagar multa |
| POST | `/multas/{id}/recorrer` | Recorrer |
| GET | `/condutores/{id}/multas` | Por condutor |
| GET | `/tipos-infracao` | Listar tipos |
| GET | `/relatorios/por-status` | Agrupado por status |
| GET | `/relatorios/por-gravidade` | Agrupado por gravidade |
| GET | `/relatorios/mensal` | Multas do mês |

---

## 🔡 QUESTÃO 7 — Manipulação de Arrays (5 pts)

```php
$multas = [
    ['codigo' => 'AIT-001', 'descricao' => 'excesso de velocidade', 'pontos' => 7,  'valor' => 293.47, 'gravidade' => 'grave'],
    ['codigo' => 'AIT-002', 'descricao' => 'avançar sinal vermelho', 'pontos' => 7, 'valor' => 293.47, 'gravidade' => 'grave'],
    ['codigo' => 'AIT-003', 'descricao' => 'dirigir alcoolizado',    'pontos' => 7, 'valor' => 2934.70,'gravidade' => 'gravissima'],
    ['codigo' => 'AIT-004', 'descricao' => 'estacionar em local proibido', 'pontos' => 3, 'valor' => 88.38, 'gravidade' => 'media'],
    ['codigo' => 'AIT-005', 'descricao' => 'celular ao volante',     'pontos' => 5, 'valor' => 293.47, 'gravidade' => 'grave'],
    ['codigo' => 'AIT-006', 'descricao' => 'sem cinto de seguranca', 'pontos' => 5, 'valor' => 195.23, 'gravidade' => 'grave'],
];
```

**a)** Converta todas as `descricao` para Title Case

**b)** Agrupe por gravidade com `array_reduce` e some os pontos de cada grupo

**c)** Calcule o valor total de todas as multas com `array_sum` + `array_column`

**d)** Ordene por valor decrescente com `usort` e extraia apenas `codigo` e `valor` formatado como `"R$ 293,47"`

**e)** Converta todas as chaves de snake_case para camelCase

