# 🛒 PROVA 04 — Sistema de E-commerce
**Nível:** Expert | **Tema:** Loja Virtual com Pedidos e Estoque | **Tempo:** 6h

---

## 🎯 Contexto do Projeto

Desenvolva uma **API REST de alto nível em PHP puro** para um sistema de e-commerce completo.  
Padrão: **MVC + DAO + Service + Observer Pattern + MySQL com transações**.

---

## 📐 Entidades e Relacionamentos

```
categorias (N) >──< produtos (N)   [produto_categoria]
produtos (1) ──< variantes (N)      [tam, cor, etc]
usuarios (1) ──< enderecos (N)
usuarios (1) ──< pedidos (N)
pedidos (1) ──< itens_pedido (N)
variantes (1) ──< itens_pedido (N)
pedidos (1) ──< pagamentos (N)
pedidos (1) ──< rastreamentos (N)
cupons (N) >──< pedidos (N)
```

---

## 🗄️ QUESTÃO 1 — Banco de Dados (20 pts)

### 1.1 — Crie o schema completo:

**`categorias`:** `id`, `nome`, `slug` (único), `categoria_pai_id` (auto-referência, nullable), `ativa`

**`produtos`:** `id`, `nome`, `slug` (único), `descricao`, `descricao_curta`, `preco_base` DECIMAL(10,2), `ativo`, `destaque`, `criado_em`, `atualizado_em`

**`produto_categoria`:** `produto_id`, `categoria_id`

**`variantes`:** `id`, `produto_id`, `sku` (único), `atributos` JSON, `preco_adicional` DECIMAL(8,2), `estoque` INT, `reservado` INT DEFAULT 0

**`usuarios`:** `id`, `nome`, `email` (único), `senha`, `telefone`, `ativo`, `email_verificado_em`

**`enderecos`:** `id`, `usuario_id`, `apelido`, `cep`, `logradouro`, `numero`, `complemento`, `bairro`, `cidade`, `estado`, `principal` TINYINT(1)

**`cupons`:** `id`, `codigo` (único), `tipo` ENUM(`'percentual'`,`'fixo'`), `valor` DECIMAL(8,2), `uso_minimo` DECIMAL(8,2), `limite_uso` INT, `usos_realizados` INT DEFAULT 0, `valido_ate` DATE, `ativo`

**`pedidos`:** `id`, `usuario_id`, `endereco_id`, `status` ENUM(`'aguardando_pagamento'`,`'pago'`,`'em_separacao'`,`'enviado'`,`'entregue'`,`'cancelado'`), `subtotal` DECIMAL(10,2), `desconto` DECIMAL(10,2), `frete` DECIMAL(8,2), `total` DECIMAL(10,2), `cupom_id` nullable, `observacoes`, `criado_em`

**`itens_pedido`:** `id`, `pedido_id`, `variante_id`, `quantidade` INT, `preco_unitario` DECIMAL(10,2), `subtotal` DECIMAL(10,2)

**`pagamentos`:** `id`, `pedido_id`, `metodo` ENUM(`'pix'`,`'boleto'`,`'cartao_credito'`,`'cartao_debito'`), `status` ENUM(`'pendente'`,`'aprovado'`,`'recusado'`,`'estornado'`), `valor` DECIMAL(10,2), `transacao_id`, `payload` JSON, `criado_em`

**`rastreamentos`:** `id`, `pedido_id`, `status`, `descricao`, `criado_em` DATETIME

### 1.2 — Triggers obrigatórios:

**a)** Crie um trigger `AFTER INSERT` em `itens_pedido` que decrementa o `estoque` e incrementa `reservado` na tabela `variantes`

**b)** Crie um trigger `AFTER UPDATE` em `pedidos` que, quando status mudar para `cancelado`, restaure o estoque das variantes

**c)** Crie um trigger `BEFORE INSERT` em `pedidos` que valide se o cupom está válido e não expirado

### 1.3 — Procedures:

**a)** Procedure `sp_fechar_pedido(pedido_id INT)` que verifica estoque de todos os itens e confirma ou cancela

**b)** Procedure `sp_relatorio_vendas(data_inicio DATE, data_fim DATE)` que retorna métricas detalhadas

---

## 📁 QUESTÃO 2 — Estrutura Avançada (5 pts)

### 2.1 — Estrutura com padrão Observer:

```
ecommerce-api/
├── src/
│   ├── Events/
│   │   ├── EventInterface.php
│   │   ├── PedidoCriadoEvent.php
│   │   └── PagamentoAprovadoEvent.php
│   ├── Listeners/
│   │   ├── ListenerInterface.php
│   │   ├── EnviarEmailPedidoListener.php
│   │   └── AtualizarEstoqueListener.php
│   ├── EventDispatcher.php
│   └── ...
```

### 2.2 — Implemente o `EventDispatcher.php`:

```php
class EventDispatcher {
    private array $listeners = [];
    
    public function listen(string $event, ListenerInterface $listener): void;
    public function dispatch(EventInterface $event): void;
}
```

---

## 🧱 QUESTÃO 3 — Models (10 pts)

### 3.1 — Crie `models/Produto.php`:

```php
class Produto {
    // Implemente:
    public function getPrecoCom(float $adicional): float;
    public function getPrecoFormatado(): string;
    public function temEstoque(): bool;          // verifica se alguma variante tem estoque > 0
    public function getEstoqueTotal(): int;      // soma de estoque de todas as variantes
    public function toArray(): array;
    public static function fromArray(array $data): self;
    public function toArrayPublico(): array;     // sem dados sensíveis, com preço formatado
}
```

### 3.2 — Crie `models/Pedido.php`:

```php
class Pedido {
    // Implemente:
    public function calcularSubtotal(): float;
    public function calcularDesconto(Cupom $cupom): float;
    public function calcularTotal(): float;
    public function podeSerCancelado(): bool;   // só se não foi enviado
    public function adicionarRastreamento(string $status, string $descricao): void;
    public function toArray(): array;
    public static function fromArray(array $data): self;
}
```

---

## 🗃️ QUESTÃO 4 — DAO (25 pts)

### 4.1 — Crie `dao/ProdutoDAO.php`:

```php
// Busca com filtros, paginação e ordenação
public function findAll(array $filtros = [], string $ordem = 'id', string $direcao = 'ASC', int $page = 1, int $limit = 20): array;

// Busca full-text por nome e descrição
public function buscarPorTexto(string $termo): array;

// Produtos em destaque com estoque disponível
public function findDestaques(int $limite = 8): array;

// Produtos de uma categoria (incluindo subcategorias recursivamente)
public function findPorCategoria(int $categoriaId): array;

// Salva produto com variantes em transação
public function saveComVariantes(Produto $produto, array $variantes): int;

// Atualiza estoque de uma variante
public function atualizarEstoque(int $varianteId, int $quantidade): bool;
```

### 4.2 — Crie `dao/PedidoDAO.php`:

```php
public function save(Pedido $pedido, array $itens): int;   // transação completa
public function findById(int $id): ?Pedido;
public function findByUsuario(int $usuarioId): array;
public function atualizarStatus(int $id, string $status): bool;
public function findComItens(int $id): array;              // JOIN com variantes e produtos
public function findPorStatus(string $status): array;
public function metricasGerais(int $mes, int $ano): array; // total, ticket médio, top produtos
```

### 4.3 — Atenção em `PedidoDAO::save()`:

A transação deve:
1. Inserir o pedido
2. Inserir todos os itens
3. Decrementar estoque das variantes
4. Registrar primeiro rastreamento
5. Aplicar uso do cupom (incrementar `usos_realizados`)
6. Fazer rollback completo em qualquer falha

---

## ⚙️ QUESTÃO 5 — Service (15 pts)

### 5.1 — Crie `services/PedidoService.php`:

```php
// Regras completas:
// - Verificar estoque de cada item antes de criar
// - Calcular frete (simulado: CEP começa com 0-4 = R$15, 5-9 = R$25)
// - Validar cupom: ativo, não expirado, usos < limite, subtotal >= uso_minimo
// - Calcular desconto conforme tipo do cupom (percentual ou fixo)
// - Disparar evento PedidoCriadoEvent após criação
public function criarPedido(int $usuarioId, array $itens, int $enderecoId, ?string $cupomCodigo = null): array;

// Cancela pedido: restaura estoque, muda status, dispara evento
public function cancelarPedido(int $pedidoId, int $usuarioId): array;

// Simula confirmação de pagamento
// - Atualiza pagamento para 'aprovado'
// - Muda status do pedido para 'pago'
// - Dispara PagamentoAprovadoEvent
public function confirmarPagamento(int $pedidoId, array $dadosPagamento): array;
```

### 5.2 — Crie `services/EstoqueService.php`:

```php
// Verifica se todos os itens têm estoque suficiente
// Retorna array com itens sem estoque
public function verificarDisponibilidade(array $itens): array;

// Reserva estoque temporariamente (incrementa 'reservado')
public function reservar(array $itens): bool;

// Confirma reserva (decrementa 'estoque' e 'reservado')
public function confirmarReserva(array $itens): bool;

// Libera reserva (decrementa só 'reservado')
public function liberarReserva(array $itens): bool;

// Retorna produtos com estoque crítico (< 5 unidades)
public function alertaEstoqueCritico(): array;
```

---

## 🌐 QUESTÃO 6 — Rotas (15 pts)

### 6.1 — Defina todas as rotas:

**Produtos (públicas):**
```
GET    /produtos                    → listagem com filtros
GET    /produtos/{slug}             → detalhe por slug
GET    /produtos/busca?q=termo      → busca full-text
GET    /categorias                  → árvore de categorias
GET    /categorias/{slug}/produtos  → produtos da categoria
```

**Carrinho (autenticado):**
```
GET    /carrinho                    → exibir carrinho
POST   /carrinho/itens              → adicionar item
PUT    /carrinho/itens/{id}         → atualizar quantidade
DELETE /carrinho/itens/{id}         → remover item
POST   /carrinho/cupom              → aplicar cupom
DELETE /carrinho/cupom              → remover cupom
```

**Pedidos (autenticado):**
```
POST   /pedidos                     → criar pedido
GET    /pedidos                     → meus pedidos
GET    /pedidos/{id}                → detalhe
POST   /pedidos/{id}/cancelar       → cancelar
GET    /pedidos/{id}/rastreamento   → histórico de status
```

**Admin (autenticado + admin):**
```
GET    /admin/pedidos               → todos os pedidos
PATCH  /admin/pedidos/{id}/status   → atualizar status
GET    /admin/produtos              → gestão de produtos
POST   /admin/produtos              → cadastrar produto
PUT    /admin/produtos/{id}         → editar produto
DELETE /admin/produtos/{id}         → remover
GET    /admin/estoque/alertas       → alertas de estoque
GET    /admin/relatorios            → métricas gerais
```

---

## 🔡 QUESTÃO 7 — Manipulação de Arrays (5 pts)

### 7.1 — Trabalhe com o array de pedidos abaixo:

```php
$pedidos = [
    [
        'id' => 1, 'status' => 'entregue', 'total' => 259.90,
        'itens' => [
            ['produto' => 'camiseta basica', 'quantidade' => 2, 'preco_unitario' => 79.90],
            ['produto' => 'calca jeans slim', 'quantidade' => 1, 'preco_unitario' => 100.10],
        ],
        'criado_em' => '2024-11-15'
    ],
    [
        'id' => 2, 'status' => 'cancelado', 'total' => 89.90,
        'itens' => [
            ['produto' => 'meia esportiva', 'quantidade' => 3, 'preco_unitario' => 29.97],
        ],
        'criado_em' => '2024-12-01'
    ],
    [
        'id' => 3, 'status' => 'enviado', 'total' => 430.00,
        'itens' => [
            ['produto' => 'tenis running pro', 'quantidade' => 1, 'preco_unitario' => 430.00],
        ],
        'criado_em' => '2025-01-10'
    ],
];
```

**a)** Extraia todos os itens de todos os pedidos em um único array flat usando `array_merge` + `array_map`

**b)** Converta **recursivamente** todas as chaves de snake_case para camelCase (inclusive dentro dos itens aninhados)

**c)** Calcule o ticket médio apenas dos pedidos com status `entregue` ou `enviado`

**d)** Retorne um array indexado pelo `id` do pedido usando `array_column`

**e)** Agrupe os itens de todos os pedidos pelo nome do produto, somando quantidade total vendida

**f)** Crie uma função `formatarMoeda(float $valor, string $moeda = 'BRL'): string` e aplique em todos os preços do array

**g)** Implemente `arrayDeepMap(array $arr, callable $fn): array` — aplica a função recursivamente em todos os valores do array (não apenas no primeiro nível)

---

## 🏆 QUESTÕES BÔNUS — (+20 pts)

### Bônus A — JWT Authentication (+10 pts):

Implemente `middlewares/AuthMiddleware.php` com JWT:
- `POST /auth/login` → retorna token JWT
- `POST /auth/refresh` → renova token
- Token deve conter: `user_id`, `email`, `role`, `exp`
- Middleware valida o token no header `Authorization: Bearer {token}`
- **Sem bibliotecas externas** — implemente a assinatura HMAC-SHA256 manualmente

### Bônus B — Rate Limiting (+5 pts):

Crie `middlewares/RateLimitMiddleware.php`:
- Limite de 60 requisições por minuto por IP
- Armazene contagem em arquivo (sem Redis)
- Retorne header `X-RateLimit-Remaining` e `X-RateLimit-Reset`
- Responda `429 Too Many Requests` quando excedido

### Bônus C — Testes Unitários (+5 pts):

Escreva testes (PHPUnit) para `services/PedidoService.php`:
- Teste que pedido com estoque insuficiente lança exceção
- Teste que cupom expirado é rejeitado
- Teste que desconto percentual é calculado corretamente
- Teste que desconto fixo não ultrapassa o subtotal

---

## ✅ Critérios de Avaliação

| Critério | Peso |
|---|---|
| SQL com triggers e procedures | 20% |
| Observer Pattern implementado | 5% |
| Models com lógica de negócio | 10% |
| DAOs com transações complexas | 25% |
| Services com regras e eventos | 15% |
| Roteamento com autenticação | 15% |
| Arrays aninhados e recursivos | 5% |
| Bônus JWT + Rate Limit + Testes | +20% |

---

## 📌 Observações Finais

- Este é um projeto de nível profissional — espera-se código limpo e bem documentado
- Use `PHP 8.2+` — aproveite `readonly classes`, `enum`, `Fibers` se quiser
- Todo endpoint deve ter validação de entrada e resposta padronizada
- Docblocks obrigatórios em todos os métodos públicos
- Não há limite de criatividade nas implementações — vá além do pedido!
