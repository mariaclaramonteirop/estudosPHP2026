# 🐱 PROVA 02 — Sistema de Pets e Adoção
**Nível:** Intermediário | **Tema:** Clínica/Adoção de Animais | **Tempo:** 4h

---

## 🎯 Contexto do Projeto

Você deve desenvolver uma **API REST em PHP puro** para uma clínica veterinária e sistema de adoção de pets.  
Padrão obrigatório: **MVC + DAO + Service + MySQL + Rotas em PHP puro**.

---

## 📐 Entidades e Relacionamentos

```
tutores (1) ──< pets (N)
pets (N) >──< racas (N)     [um pet pode ter raça mista]
veterinarios (1) ──< consultas (N)
pets (1) ──< consultas (N)
consultas (1) ──< prescricoes (N)
```

---

## 🗄️ QUESTÃO 1 — Banco de Dados (20 pts)

### 1.1 — Crie o schema completo:

```sql
-- Crie as tabelas com os campos abaixo:
```

**Tabela `tutores`:**
- `id`, `nome_completo`, `cpf` (único), `telefone`, `email` (único), `endereco`, `criado_em`

**Tabela `racas`:**
- `id`, `nome`, `especie` ENUM(`'cao'`, `'gato'`, `'ave'`, `'roedor'`, `'outro'`), `descricao`, `esperanca_vida_anos`

**Tabela `pets`:**
- `id`, `nome`, `data_nascimento`, `peso_kg` DECIMAL(5,2), `sexo` ENUM(`'M'`,`'F'`), `castrado` TINYINT(1), `foto_url`, `status` ENUM(`'com_tutor'`,`'disponivel_adocao'`,`'em_tratamento'`), `tutor_id`, `criado_em`

**Tabela `pet_raca`:**
- `pet_id`, `raca_id`, `percentual` INT (soma dos percentuais de um pet = 100)

**Tabela `veterinarios`:**
- `id`, `nome`, `crmv`, `especialidade`, `telefone`, `ativo` TINYINT(1)

**Tabela `consultas`:**
- `id`, `pet_id`, `veterinario_id`, `data_hora` DATETIME, `tipo` ENUM(`'rotina'`,`'emergencia'`,`'retorno'`,`'cirurgia'`), `diagnostico`, `observacoes`, `valor` DECIMAL(8,2), `pago` TINYINT(1)

**Tabela `prescricoes`:**
- `id`, `consulta_id`, `medicamento`, `dosagem`, `frequencia`, `duracao_dias`, `observacoes`

### 1.2 — Escreva queries SQL avançadas:

**a)** Liste todos os pets disponíveis para adoção com nome do tutor anterior (se houver) e raças

**b)** Calcule o faturamento total por veterinário no mês atual

**c)** Encontre os pets que não tiveram nenhuma consulta nos últimos 6 meses

**d)** Liste as raças mais comuns entre os pets cadastrados (top 5)

---

## 📁 QUESTÃO 2 — Estrutura MVC (5 pts)

### 2.1 — Monte a estrutura completa do projeto:

Além da estrutura padrão, inclua:
- `middlewares/` com `AuthMiddleware.php`
- `helpers/` com `StringHelper.php` e `DateHelper.php`
- `exceptions/` com exceções customizadas
- `.htaccess` para redirecionar tudo ao `index.php`

### 2.2 — Crie o `.htaccess` para Apache:

O arquivo deve redirecionar todas as requisições para `index.php` mantendo a query string.

---

## 🔌 QUESTÃO 3 — Conexão e Config (5 pts)

### 3.1 — Crie `config/Database.php` com Singleton PDO

### 3.2 — Crie `config/App.php` com as constantes:

```php
// Deve conter: BASE_URL, API_VERSION, DEBUG_MODE, MAX_UPLOAD_SIZE
```

---

## 🧱 QUESTÃO 4 — Models (10 pts)

### 4.1 — Crie `models/Pet.php`:

- Todas as propriedades em camelCase
- Método `getIdade()` que calcula a idade em anos e meses a partir de `$dataNascimento`
- Método `toArray()` com snake_case nas chaves
- Método `fromArray(array $data)` estático
- Método `estaDisponivel()` retorna bool

### 4.2 — Crie `models/Consulta.php`:

- Propriedades em camelCase
- Método `getValorFormatado()` que retorna string `"R$ 1.250,00"`
- Método `isCirurgia()` retorna bool
- Método `toArray()` com snake_case

### 4.3 — Explique com comentários no código a diferença entre:
```php
$pet->getNome();      // getter
$pet->toArray();      // serialização
$pet->fromArray();    // hidratação
```

---

## 🗃️ QUESTÃO 5 — DAO (25 pts)

### 5.1 — Crie `dao/PetDAO.php`:

Métodos obrigatórios:

```php
public function findAll(): array;
public function findById(int $id): ?Pet;
public function save(Pet $pet): bool;
public function update(Pet $pet): bool;
public function delete(int $id): bool;

// Extras obrigatórios:
public function findDisponiveisAdocao(): array;
public function findByTutor(int $tutorId): array;
public function findComRacas(int $petId): array;  // JOIN com pet_raca e racas
public function atualizarStatus(int $id, string $status): bool;
```

### 5.2 — Crie `dao/ConsultaDAO.php`:

```php
public function findAll(): array;
public function findById(int $id): ?Consulta;
public function save(Consulta $consulta): bool;
public function update(Consulta $consulta): bool;
public function delete(int $id): bool;

// Extras:
public function findByPet(int $petId): array;
public function findByVeterinario(int $vetId): array;
public function faturamentoPorMes(int $ano): array;   // agrupa por mês
public function findNaoPagas(): array;
```

### 5.3 — Em `PetDAO::findAll()`, use a seguinte query com JOIN:

```sql
-- Deve retornar: pet.*, tutor.nome_completo AS tutor_nome, GROUP_CONCAT(raca.nome) AS racas
```

---

## ⚙️ QUESTÃO 6 — Service (15 pts)

### 6.1 — Crie `services/PetService.php`:

```php
// Regra: pet só pode ser cadastrado se tutor existir e tiver menos de 10 pets
public function cadastrar(array $dados): array;

// Regra: só pode adotar pet com status 'disponivel_adocao'
// Atualiza o tutor_id e muda status para 'com_tutor'
public function adotar(int $petId, int $tutorId): array;

// Regra: retorna pets agrupados por espécie
public function listarPorEspecie(): array;

// Regra: retorna aniversariantes do mês atual (usa data_nascimento)
public function aniversariantesDoMes(): array;
```

### 6.2 — Crie `services/ConsultaService.php`:

```php
// Regra: não pode agendar consulta se veterinário já tem consulta no mesmo horário
public function agendar(array $dados): array;

// Regra: calcula total em aberto por tutor
public function totalEmAbertoPorTutor(int $tutorId): float;

// Regra: retorna histórico completo do pet com prescrições
public function historicoCompleto(int $petId): array;
```

---

## 🌐 QUESTÃO 7 — Rotas e Controllers (15 pts)

### 7.1 — Crie `routes.php` com as rotas:

| Método | Rota | Controller@Método |
|---|---|---|
| GET | `/pets` | PetController@index |
| GET | `/pets/{id}` | PetController@show |
| POST | `/pets` | PetController@store |
| PUT | `/pets/{id}` | PetController@update |
| DELETE | `/pets/{id}` | PetController@destroy |
| GET | `/pets/adocao` | PetController@disponiveis |
| POST | `/pets/{id}/adotar` | PetController@adotar |
| GET | `/pets/{id}/consultas` | ConsultaController@porPet |
| GET | `/tutores/{id}/pets` | PetController@porTutor |
| GET | `/veterinarios/{id}/agenda` | ConsultaController@agendaVet |
| POST | `/consultas` | ConsultaController@store |
| GET | `/consultas/{id}/prescricoes` | PrescricaoController@index |

### 7.2 — Crie o roteador em `index.php`:

O arquivo deve:
1. Capturar `$_SERVER['REQUEST_METHOD']` e `$_SERVER['REQUEST_URI']`
2. Remover a base da URL e query strings
3. Fazer match da rota com expressão regular para capturar `{id}`
4. Instanciar o controller correto e chamar o método
5. Responder `404` se nenhuma rota casar

---

## 🔡 QUESTÃO 8 — Manipulação de Arrays (5 pts)

### 8.1 — Dado o array de pets abaixo:

```php
$pets = [
    ['nome' => 'mr whiskers', 'especie' => 'gato', 'peso_kg' => 4.2, 'castrado' => true, 'idade_anos' => 3],
    ['nome' => 'rex', 'especie' => 'cao', 'peso_kg' => 28.5, 'castrado' => false, 'idade_anos' => 7],
    ['nome' => 'tweety bird', 'especie' => 'ave', 'peso_kg' => 0.08, 'castrado' => false, 'idade_anos' => 2],
    ['nome' => 'bolinha', 'especie' => 'cao', 'peso_kg' => 12.3, 'castrado' => true, 'idade_anos' => 5],
    ['nome' => 'princesa', 'especie' => 'gato', 'peso_kg' => 3.7, 'castrado' => true, 'idade_anos' => 1],
    ['nome' => 'thor odinson', 'especie' => 'cao', 'peso_kg' => 35.0, 'castrado' => false, 'idade_anos' => 4],
];
```

**a)** Converta todos os nomes para **Title Case** (cada palavra começa com maiúscula)

**b)** Agrupe os pets por espécie usando `array_reduce`

**c)** Calcule a média de peso por espécie

**d)** Retorne apenas os pets castrados com peso abaixo de 5kg, ordenados por nome alfabeticamente

**e)** Converta o array para que as chaves `peso_kg` e `idade_anos` virem `pesoKg` e `idadeAnos` (snake_case → camelCase) em todos os elementos

**f)** Crie uma função PHP `snakeToCamel(string $str): string` e outra `camelToSnake(string $str): string` e demonstre seu uso

---

## 🏆 QUESTÃO BÔNUS — (+10 pts)

### Implemente paginação na listagem de pets:

- A rota `GET /pets?page=1&limit=10` deve retornar resultado paginado
- A resposta JSON deve conter:

```json
{
  "data": [...],
  "meta": {
    "total": 120,
    "por_pagina": 10,
    "pagina_atual": 1,
    "ultima_pagina": 12,
    "de": 1,
    "ate": 10
  }
}
```

---

## ✅ Critérios de Avaliação

| Critério | Peso |
|---|---|
| SQL com JOINs e queries avançadas | 20% |
| Estrutura MVC + .htaccess | 5% |
| Models com métodos utilitários | 10% |
| DAOs com prepared statements e JOINs | 25% |
| Services com regras de negócio | 15% |
| Roteador funcional com regex | 15% |
| Manipulação de arrays | 5% |
| Bônus: paginação | +10% |

---

## 📌 Observações

- Usar `PDO::FETCH_ASSOC` ou `PDO::FETCH_CLASS`
- Retornar erros de validação com HTTP `422`
- Retornar `401` quando recurso não autorizado
- Não usar `die()` nem `exit()` — usar exceções customizadas
- PHP 8.1+ obrigatório (use `match`, `named arguments`, `enums` se quiser)
