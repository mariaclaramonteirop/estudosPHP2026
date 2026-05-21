# 🚗 PROVA 03 — Sistema de Concessionária de Veículos
**Nível:** Avançado | **Tema:** Venda e Gestão de Veículos | **Tempo:** 5h

---

## 🎯 Contexto do Projeto

Desenvolva uma **API REST completa em PHP puro** para uma concessionária de veículos.  
O sistema deve gerenciar estoque, vendas, clientes e revisões.  
Padrão: **MVC + DAO + Service + Repository Pattern + MySQL**.

---

## 📐 Entidades e Relacionamentos

```
fabricantes (1) ──< modelos (N)
modelos (1) ──< veiculos (N)
clientes (1) ──< vendas (N)
vendedores (1) ──< vendas (N)
veiculos (1) ──< vendas (1)         [veiculo vendido apenas 1x]
veiculos (1) ──< revisoes (N)
vendas (1) ──< parcelas (N)
veiculos (N) >──< opcionais (N)     [ar, couro, teto solar...]
```

---

## 🗄️ QUESTÃO 1 — Banco de Dados (20 pts)

### 1.1 — Crie o schema SQL completo:

**`fabricantes`:** `id`, `nome`, `pais_origem`, `ano_fundacao`, `ativo`

**`modelos`:** `id`, `nome`, `fabricante_id`, `categoria` ENUM(`'hatch'`,`'sedan'`,`'suv'`,`'pickup'`,`'eletrico'`,`'hibrido'`), `numero_portas`, `capacidade_passageiros`

**`veiculos`:** `id`, `modelo_id`, `ano_fabricacao`, `ano_modelo`, `cor`, `placa` (único, nullable), `chassi` (único), `km_atual`, `combustivel` ENUM(`'flex'`,`'gasolina'`,`'diesel'`,`'eletrico'`,`'hibrido'`), `cambio` ENUM(`'manual'`,`'automatico'`,`'cvt'`), `preco_custo` DECIMAL(12,2), `preco_venda` DECIMAL(12,2), `status` ENUM(`'disponivel'`,`'reservado'`,`'vendido'`,`'em_revisao'`)

**`opcionais`:** `id`, `nome`, `descricao`, `valor_adicional` DECIMAL(8,2)

**`veiculo_opcional`:** `veiculo_id`, `opcional_id`

**`clientes`:** `id`, `tipo` ENUM(`'pf'`,`'pj'`), `nome`, `cpf_cnpj` (único), `email`, `telefone`, `endereco`, `cep`, `cidade`, `estado`

**`vendedores`:** `id`, `nome`, `cpf`, `email`, `comissao_percentual` DECIMAL(4,2), `meta_mensal` DECIMAL(12,2), `ativo`

**`vendas`:** `id`, `veiculo_id`, `cliente_id`, `vendedor_id`, `data_venda` DATETIME, `valor_venda` DECIMAL(12,2), `valor_entrada` DECIMAL(12,2), `num_parcelas` INT, `forma_pagamento` ENUM(`'avista'`,`'financiado'`,`'consorcio'`), `observacoes`, `cancelada` TINYINT(1) DEFAULT 0

**`parcelas`:** `id`, `venda_id`, `numero`, `valor` DECIMAL(10,2), `data_vencimento` DATE, `data_pagamento` DATE nullable, `paga` TINYINT(1) DEFAULT 0

**`revisoes`:** `id`, `veiculo_id`, `data_revisao` DATE, `km_revisao` INT, `tipo` ENUM(`'preventiva'`,`'corretiva'`,`'recall'`), `descricao`, `valor_mao_obra` DECIMAL(8,2), `valor_pecas` DECIMAL(8,2), `mecanico_responsavel`

### 1.2 — Escreva as seguintes queries:

**a)** Relatório de vendas do mês com: nome do cliente, modelo, vendedor, valor e comissão calculada

**b)** Estoque atual agrupado por fabricante e categoria com valor total em estoque

**c)** Top 5 vendedores do ano com total vendido e percentual de atingimento de meta

**d)** Veículos com revisão vencida (última revisão > 1 ano ou km_atual > ultima_km_revisao + 10000)

**e)** View `vw_estoque_completo` que retorna veículo com fabricante, modelo, opcionais e preço total

---

## 📁 QUESTÃO 2 — Estrutura do Projeto (5 pts)

### 2.1 — Monte a estrutura completa:

```
concessionaria-api/
├── config/
│   ├── Database.php
│   ├── App.php
│   └── env.php
├── src/
│   ├── models/
│   ├── dao/
│   │   └── interfaces/
│   ├── services/
│   ├── controllers/
│   ├── middlewares/
│   ├── helpers/
│   └── exceptions/
├── public/
│   ├── index.php
│   └── .htaccess
├── routes/
│   ├── api.php
│   └── Router.php
└── tests/            ← descreva o que cada test testaria
```

### 2.2 — Implemente a classe `Router.php`:

A classe deve suportar:
```php
$router->get('/veiculos', [VeiculoController::class, 'index']);
$router->post('/veiculos', [VeiculoController::class, 'store']);
$router->get('/veiculos/{id}', [VeiculoController::class, 'show']);
$router->put('/veiculos/{id}', [VeiculoController::class, 'update']);
$router->delete('/veiculos/{id}', [VeiculoController::class, 'destroy']);

// Middleware em grupo:
$router->group('/admin', function($router) {
    $router->get('/relatorios', [RelatorioController::class, 'index']);
}, [AuthMiddleware::class]);
```

---

## 🧱 QUESTÃO 3 — Models (10 pts)

### 3.1 — Crie `models/Veiculo.php`:

```php
class Veiculo {
    // Propriedades camelCase
    private int $id;
    private int $modeloId;
    private int $anoFabricacao;
    private int $anoModelo;
    private string $cor;
    private ?string $placa;
    private string $chassi;
    private int $kmAtual;
    private string $combustivel;
    private string $cambio;
    private float $precoCusto;
    private float $precoVenda;
    private string $status;
    private array $opcionais = [];

    // Implemente:
    public function getMargemLucro(): float;          // % de lucro sobre custo
    public function getMargemLucroFormatada(): string; // "R$ 5.000,00 (15,3%)"
    public function estaDisponivel(): bool;
    public function toArray(): array;                  // snake_case keys
    public static function fromArray(array $data): self;
    public function toJson(): string;                  // JSON snake_case
}
```

### 3.2 — Crie `models/Venda.php`:

```php
class Venda {
    // Implemente:
    public function getValorFinanciado(): float;     // valor_venda - valor_entrada
    public function getValorParcela(): float;         // valor_financiado / num_parcelas
    public function getValorComissao(): float;        // usa comissao_percentual do vendedor
    public function estaCancelada(): bool;
    public function toArray(): array;
    public static function fromArray(array $data): self;
}
```

---

## 🗃️ QUESTÃO 4 — DAO (25 pts)

### 4.1 — Crie a interface `dao/interfaces/IVeiculoDAO.php`:

```php
interface IVeiculoDAO {
    public function findAll(array $filtros = [], int $page = 1, int $limit = 15): array;
    public function findById(int $id): ?Veiculo;
    public function findDisponiveis(): array;
    public function findByModelo(int $modeloId): array;
    public function findComOpcionais(int $id): array;
    public function save(Veiculo $veiculo): int;   // retorna o novo ID
    public function update(Veiculo $veiculo): bool;
    public function updateStatus(int $id, string $status): bool;
    public function delete(int $id): bool;
    public function countByStatus(): array;          // agrupa por status
    public function valorTotalEstoque(): float;
}
```

### 4.2 — Implemente `dao/VeiculoDAO.php`:

- `findAll()` deve suportar filtros: `modelo_id`, `fabricante_id`, `status`, `combustivel`, `preco_min`, `preco_max`, `ano_min`, `ano_max`
- `findAll()` deve implementar paginação com LIMIT/OFFSET
- `findComOpcionais()` deve trazer os opcionais via JOIN
- `save()` deve usar transação quando houver opcionais para inserir em `veiculo_opcional`

### 4.3 — Crie `dao/VendaDAO.php`:

```php
// Métodos obrigatórios:
public function save(Venda $venda): int;
public function findById(int $id): ?Venda;
public function findByCliente(int $clienteId): array;
public function findByVendedor(int $vendedorId): array;
public function cancelar(int $id): bool;
public function faturamentoMensal(int $mes, int $ano): array;
public function comissoesPorVendedor(int $mes, int $ano): array;
```

**Atenção:** `save()` de venda deve usar **transação** para:
1. Inserir a venda
2. Atualizar status do veículo para `vendido`
3. Gerar as parcelas automaticamente

---

## ⚙️ QUESTÃO 5 — Service (15 pts)

### 5.1 — Crie `services/VeiculoService.php`:

```php
// Validações: preco_venda >= preco_custo, chassi 17 caracteres, ano_modelo >= ano_fabricacao
public function cadastrar(array $dados): array;

// Regra: só pode atualizar se status != 'vendido'
public function atualizar(int $id, array $dados): array;

// Regra: aplica desconto máximo de 10% sobre preco_venda, apenas se status = 'disponivel'
public function aplicarDesconto(int $id, float $percentual): array;

// Busca com filtros dinâmicos + paginação
public function buscar(array $filtros, int $page = 1): array;
```

### 5.2 — Crie `services/VendaService.php`:

```php
// Regras:
// - Veículo deve estar 'disponivel'
// - Se financiado: valor_entrada >= 20% do valor_venda
// - Se financiado: num_parcelas entre 12 e 60
// - Atualiza status do veículo para 'vendido' automaticamente
// - Gera parcelas automaticamente
public function realizarVenda(array $dados): array;

// Cancela venda e reverte status do veículo para 'disponivel'
// Só pode cancelar se data_venda <= 7 dias atrás
public function cancelarVenda(int $id): array;

// Retorna relatório: total vendas, ticket médio, comissões
public function relatorioMensal(int $mes, int $ano): array;
```

---

## 🌐 QUESTÃO 6 — Rotas e Controllers (15 pts)

### 6.1 — Defina todas as rotas em `routes/api.php`:

| Método | Rota | Descrição |
|---|---|---|
| GET | `/veiculos` | Listagem com filtros e paginação |
| GET | `/veiculos/{id}` | Detalhes com opcionais |
| POST | `/veiculos` | Cadastrar |
| PUT | `/veiculos/{id}` | Atualizar |
| DELETE | `/veiculos/{id}` | Remover |
| PATCH | `/veiculos/{id}/desconto` | Aplicar desconto |
| GET | `/veiculos/{id}/revisoes` | Histórico de revisões |
| GET | `/clientes` | Listar clientes |
| POST | `/clientes` | Cadastrar cliente |
| GET | `/clientes/{id}/vendas` | Histórico de compras |
| POST | `/vendas` | Realizar venda |
| GET | `/vendas/{id}` | Detalhes da venda |
| POST | `/vendas/{id}/cancelar` | Cancelar venda |
| GET | `/vendas/{id}/parcelas` | Parcelas da venda |
| GET | `/relatorios/mensal` | Relatório mensal |
| GET | `/relatorios/estoque` | Relatório de estoque |
| GET | `/vendedores/{id}/comissoes` | Comissões do vendedor |

### 6.2 — Implemente `controllers/VeiculoController.php`:

Todos os métodos devem:
- Retornar JSON com estrutura padrão:
```json
{
  "success": true,
  "data": {},
  "message": "string",
  "meta": {}
}
```
- Usar os HTTP status codes corretos
- Capturar exceções customizadas e retornar erros padronizados

---

## 🔡 QUESTÃO 7 — Manipulação de Arrays (5 pts)

### 7.1 — Dado o inventário de veículos:

```php
$estoque = [
    ['modelo' => 'gol', 'fabricante' => 'volkswagen', 'ano' => 2022, 'preco' => 65000, 'combustivel' => 'flex', 'km' => 0],
    ['modelo' => 'onix plus', 'fabricante' => 'chevrolet', 'ano' => 2023, 'preco' => 89000, 'combustivel' => 'flex', 'km' => 0],
    ['modelo' => 'compass', 'fabricante' => 'jeep', 'ano' => 2021, 'preco' => 175000, 'combustivel' => 'diesel', 'km' => 45000],
    ['modelo' => 'kwid', 'fabricante' => 'renault', 'ano' => 2023, 'preco' => 72000, 'combustivel' => 'flex', 'km' => 0],
    ['modelo' => 'hilux', 'fabricante' => 'toyota', 'ano' => 2022, 'preco' => 320000, 'combustivel' => 'diesel', 'km' => 12000],
    ['modelo' => 'hb20', 'fabricante' => 'hyundai', 'ano' => 2023, 'preco' => 78000, 'combustivel' => 'flex', 'km' => 0],
    ['modelo' => 'argo', 'fabricante' => 'fiat', 'ano' => 2022, 'preco' => 71000, 'combustivel' => 'flex', 'km' => 8000],
];
```

**a)** Converta todos os campos string para **PascalCase** (`gol` → `Gol`, `onix plus` → `Onix Plus`)

**b)** Converta as chaves do array de **snake_case para camelCase** em todos os elementos

**c)** Agrupe os veículos por fabricante usando `array_reduce`, resultado:
```php
['volkswagen' => [...], 'chevrolet' => [...], ...]
```

**d)** Calcule o preço médio por combustível com `array_filter` + `array_column` + `array_sum`

**e)** Ordene os veículos por preço decrescente e retorne apenas `modelo`, `fabricante` e `preco` formatado como `"R$ 65.000,00"`

**f)** Implemente a função `arrayKeysToCamel(array $arr): array` que converte recursivamente todas as chaves de um array aninhado de snake_case para camelCase

**g)** Implemente `arrayKeysToSnake(array $arr): array` — o inverso da função acima

---

## 🏆 QUESTÃO BÔNUS — (+15 pts)

### Implemente um sistema de cache simples:

Crie `helpers/Cache.php` que:
1. Armazena resultados em arquivos JSON em `storage/cache/`
2. Aceita TTL em segundos
3. Invalida cache automaticamente quando expirado
4. Implemente nos métodos mais pesados do DAO (ex: relatórios)

```php
// Uso esperado:
$cache = Cache::getInstance();
$dados = $cache->remember('estoque_completo', 300, function() {
    return $this->veiculoDAO->findAll();
});
```

---

## ✅ Critérios de Avaliação

| Critério | Peso |
|---|---|
| SQL com JOINs, transações e VIEW | 20% |
| Router com grupos e middlewares | 5% |
| Models completos com lógica de negócio | 10% |
| DAOs com filtros, paginação e transações | 25% |
| Services com validações complexas | 15% |
| Controllers com resposta padronizada | 15% |
| Manipulação de arrays recursiva | 5% |
| Bônus: sistema de cache | +15% |

---

## 📌 Observações

- Todo valor monetário deve ser tratado como `float` internamente e formatado apenas na camada de apresentação
- Use `try/catch` em toda operação de banco com transação
- Exceções customizadas: `VeiculoNotFoundException`, `VendaInvalidaException`, `EstoqueException`
- O código deve ser funcional — não apenas declarar métodos sem corpo
- Implemente pelo menos **uma rota completa do zero até o banco** de forma totalmente funcional
