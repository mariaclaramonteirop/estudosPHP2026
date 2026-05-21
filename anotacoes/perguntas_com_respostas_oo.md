## 1. Base da Orientação a Objetos

### O que diferencia objeto de classe?

* **Classe** é o molde.
* **Objeto** é a instância concreta desse molde.

Ex:

```php
class Usuario {}
```

```php
$usuario = new Usuario();
```

---

### Como modelar entidades do mundo real sem copiar literalmente a realidade?

POO não é copiar o mundo real perfeitamente.
É representar apenas o que importa para o sistema.

Ex:
Num sistema bancário, uma pessoa pode virar:

```php
Cliente {
    nome
    cpf
    saldo
}
```

Você ignora coisas irrelevantes para o domínio.

---

### Como saber se estou usando POO ou apenas organizando funções dentro de classes?

Se as classes:

* possuem estado,
* comportamento,
* responsabilidade clara,
* protegem regras internas,

você está usando POO.

Se são apenas:

```php
UsuarioService::criar()
UsuarioService::deletar()
```

com entidades vazias cheias de getters/setters, isso tende a ser procedural disfarçado.

---

### Se outro dev olhar minha modelagem, ela comunica intenção claramente?

Boa modelagem:

* nomes claros,
* responsabilidades claras,
* pouco acoplamento,
* domínio fácil de entender.

Se precisa explicar tudo oralmente, provavelmente a modelagem não está clara.

---

# 2. Encapsulamento

### Por que esconder detalhes internos de uma classe?

Para proteger o estado interno e evitar uso incorreto.

Ex:

```php
$conta->saldo = -1000;
```

Isso quebra regra de negócio.

Melhor:

```php
$conta->sacar(100);
```

---

### O que é encapsulamento na prática além da definição teórica?

É controlar:

* como dados mudam,
* quem pode mudar,
* quais regras precisam ser respeitadas.

---

### Quando faz sentido deixar atributos privados?

Quase sempre.

Porque a classe deve controlar suas próprias regras.

---

### O que pode acontecer se qualquer parte do sistema puder alterar estados livremente?

* inconsistência,
* bugs difíceis,
* quebra de regras,
* forte acoplamento.

---

### Por que getters/setters excessivos podem indicar problema?

Porque a classe vira apenas um “pacote de dados”.

Isso geralmente indica:

* objeto anêmico,
* lógica espalhada,
* perda de encapsulamento.

---

# 3. Responsabilidade e Coesão

### Essa classe tem uma única responsabilidade?

Uma classe deve ter um único motivo para mudar.

Ruim:

```php
UsuarioService
- envia email
- salva usuário
- gera relatório
- autentica
```

---

### O que faz um método ter responsabilidade demais?

Quando ele:

* valida,
* salva,
* envia email,
* chama API,
* gera log,
* transforma dados.

Tudo ao mesmo tempo.

---

### Como evitar objetos “faz-tudo”?

Separando responsabilidades.

---

### Como identificar baixa coesão?

Quando os métodos da classe quase não se relacionam.

---

### Se eu mudar uma regra de negócio, quantas classes precisam mudar junto?

Idealmente:

* poucas.

Se precisa alterar 15 arquivos → alto acoplamento.

---

# 4. Acoplamento

### O que acontece quando meu código depende de implementação concreta e não de abstração?

Seu sistema fica rígido.

Ex:

```php
class PedidoService {
   private MysqlRepository $repo;
}
```

Trocar MySQL vira dor.

Melhor:

```php
private PedidoRepositoryInterface $repo;
```

---

### O que indica que uma classe está muito acoplada?

* muitas dependências,
* conhece detalhes internos de outras classes,
* difícil de testar,
* pequenas mudanças quebram tudo.

---

### Qual o custo de um alto acoplamento no crescimento do sistema?

* manutenção difícil,
* medo de alterar código,
* efeito cascata,
* baixa escalabilidade.

---

### Como separar regra de negócio de infraestrutura?

Regra:

* domínio.

Infraestrutura:

* banco,
* fila,
* email,
* API externa.

Domínio não deveria depender diretamente dessas tecnologias.

---

### Por que “instanciar tudo dentro da classe” dificulta evolução?

Porque cria dependência fixa.

Ruim:

```php
$this->mailer = new Mailer();
```

Melhor:

```php
public function __construct(MailerInterface $mailer)
```

---

# 5. Interfaces e Abstrações

### Por que criar uma interface ao invés de usar a classe diretamente?

Para desacoplar.

Você depende do contrato, não da implementação.

---

### Como interfaces ajudam a diminuir acoplamento?

Permitem trocar implementação sem alterar quem consome.

---

### Qual a diferença entre “o que um objeto faz” e “como ele faz”?

Interface:

* define o que faz.

Classe:

* define como faz.

---

### Como saber se uma abstração faz sentido?

Quando existe possibilidade real de:

* troca,
* variação,
* múltiplas implementações.

---

### Quando usar classe abstrata ao invés de interface?

Quando existe:

* comportamento compartilhado,
* implementação parcial comum.

---

# 6. Herança, Composição e Polimorfismo

### “É um” ou “tem um”?

Herança:

```txt
Cachorro É UM Animal
```

Composição:

```txt
Carro TEM UM Motor
```

---

### Quando composição é melhor que herança?

Quase sempre que quiser flexibilidade.

---

### Por que herança excessiva pode virar problema?

Porque cria:

* forte acoplamento,
* rigidez,
* hierarquias difíceis.

---

### O que o polimorfismo resolve além de sobrescrever métodos?

Permite tratar objetos diferentes da mesma forma.

---

### Como o polimorfismo ajuda a evitar vários `if/else`?

Ruim:

```php
if ($pagamento == 'pix')
```

Melhor:

```php
$metodo->pagar();
```

Cada implementação resolve seu comportamento.

---

### O que acontece quando subclasses quebram comportamento esperado?

Violação do LSP.

Isso torna o sistema imprevisível.

---

# 7. SOLID

## SRP

### O que significa uma classe ter apenas um motivo para mudar?

Uma única responsabilidade.

---

## OCP

### O que significa “aberto para extensão e fechado para modificação”?

Adicionar comportamento sem alterar código existente.

---

### O design atual facilita mudança ou só funciona “por enquanto”?

Boa arquitetura suporta evolução sem caos.

---

## LSP

### O que acontece quando subclasses quebram comportamento esperado?

Polimorfismo deixa de funcionar corretamente.

---

## ISP

### Por que interfaces muito grandes são ruins?

Forçam classes a implementar métodos desnecessários.

---

## DIP

### Por que módulos de alto nível não deveriam depender dos de baixo nível?

Porque regras de negócio não deveriam depender de tecnologia.

---

### Por que depender de abstrações facilita testes?

Porque você pode mockar implementações.

---

# 8. Injeção de Dependência e Testabilidade

### Como injeção de dependência reduz acoplamento?

A classe recebe dependências prontas.

Ela não controla criação delas.

---

### O que torna um código difícil de testar?

* dependências concretas,
* estados globais,
* acoplamento alto,
* efeitos colaterais.

---

### Por que mocks normalmente trabalham com interfaces?

Porque são substituições controladas do contrato.

---

### Se eu precisar trocar a tecnologia amanhã, quanto código precisaria mudar?

Idealmente:

* apenas infraestrutura.

Não regra de negócio.

---

# 9. Modelagem de Domínio

### Quando uma regra deve ficar na entidade e não no service?

Quando pertence ao comportamento natural da entidade.

Ex:

```php
Conta->sacar()
```

faz mais sentido que:

```php
ContaService->sacar()
```

---

### O que diferencia objeto rico de objeto anêmico?

Objeto rico:

* possui comportamento.

Objeto anêmico:

* só guarda dados.

---

### Qual a diferença entre entidade e value object?

Entidade:

* possui identidade.

Value Object:

* definido pelos valores.

---

### O que caracteriza um bom domínio orientado a objetos?

* regras bem encapsuladas,
* linguagem clara,
* entidades coerentes,
* baixo acoplamento.

---

# 10. Arquitetura e Evolução

### Como POO ajuda manutenção em sistemas grandes?

* modularidade,
* reutilização,
* isolamento de mudanças.

---

### Como eventos e contratos ajudam desacoplamento?

Os módulos deixam de se conhecer diretamente.

---

### O que torna uma arquitetura flexível de verdade?

Capacidade de mudar sem quebrar tudo.

---

# EXTRAS

# Objetos Imutáveis

## O que é um objeto imutável?

É um objeto que, depois de criado, **não pode ter seu estado alterado**.

Ou seja:

* os valores são definidos no construtor,
* não existem setters,
* qualquer “alteração” gera um novo objeto.

---

# Ideia principal

O objeto nasce:

* pronto,
* consistente,
* válido.

E continua assim durante toda sua vida.

---

# Exemplo

```php id="rj06pc"
class Usuario
{
    public function __construct(
        private readonly int $id = 0,
        private readonly string $nome = ""
    ) {}

    public function id(): int
    {
        return $this->id;
    }

    public function nome(): string
    {
        return $this->nome;
    }

    public function comNome(string $nome): self
    {
        return new self(
            id: $this->id,
            nome: $nome
        );
    }
}
```

Uso:

```php id="u6cklu"
$usuario = new Usuario();

$novoUsuario = $usuario->comNome("Maria");
```

O objeto original continua intacto.

---

# Por que usar objetos imutáveis?

## 1. Previsibilidade

Se o objeto foi criado com determinados valores:

* ele continuará igual.

Isso reduz bugs.

---

## 2. Segurança de estado

Nenhuma parte do sistema altera o objeto “sem querer”.

---

## 3. Menos efeitos colaterais

Você evita mudanças inesperadas compartilhadas entre partes do sistema.

---

## 4. Objetos sempre válidos

O construtor pode garantir consistência desde o início.

Ex:

```php id="02mjlwm"
class CPF
{
    public function __construct(
        private readonly string $valor
    ) {
        if (!$this->valido($valor)) {
            throw new Exception("CPF inválido");
        }
    }
}
```

O objeto nunca nasce inválido.

---

# E o caso de:

```php id="7r18vg"
id = 0
nome = ""
```

Isso geralmente representa:

* um estado inicial,
* um objeto vazio,
* ou um Null Object.

A ideia continua sendo:

* o objeto nasce pronto,
* com estado definido,
* sem depender de `null`.

---

# Então objeto imutável não significa “valor fixo manualmente”

Significa:

* o estado não muda depois da criação.

Mesmo que exista:

```php id="tw57b5"
new Usuario()
```

com valores padrão.

---

# Características comuns de objetos imutáveis

* atributos privados,
* sem setters,
* estado definido no construtor,
* métodos retornam novos objetos,
* estado consistente,
* previsibilidade.

---

# Quando usar?

Muito comum em:

* Value Objects,
* dinheiro,
* CPF,
* e-mail,
* datas,
* coordenadas,
* configurações,
* regras matemáticas.

---

# Pergunta importante

## “Faz sentido esse objeto mudar depois de criado?”

Se a resposta for “não”:

* imutabilidade provavelmente faz sentido.

---
