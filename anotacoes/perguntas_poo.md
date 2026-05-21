## 1. Base da Orientação a Objetos

* O que diferencia objeto de classe?
* Como modelar entidades do mundo real sem copiar literalmente a realidade?
* Como saber se estou usando POO ou apenas organizando funções dentro de classes?
* Se outro dev olhar minha modelagem, ela comunica intenção claramente?

---

## 2. Encapsulamento

* Por que esconder detalhes internos de uma classe?
* O que é encapsulamento na prática além da definição teórica?
* Quando faz sentido deixar atributos privados?
* O que pode acontecer se qualquer parte do sistema puder alterar estados livremente?
* Por que getters/setters excessivos podem indicar problema?

---

## 3. Responsabilidade e Coesão

* Essa classe tem uma única responsabilidade?
* O que faz um método ter responsabilidade demais?
* Como evitar objetos “faz-tudo”?
* Como identificar baixa coesão?
* Se eu mudar uma regra de negócio, quantas classes precisam mudar junto?

---

## 4. Acoplamento

* O que acontece quando meu código depende de implementação concreta e não de abstração?
* O que indica que uma classe está muito acoplada?
* Qual o custo de um alto acoplamento no crescimento do sistema?
* Como separar regra de negócio de infraestrutura?
* Por que “instanciar tudo dentro da classe” dificulta evolução?

---

## 5. Interfaces e Abstrações

* Por que criar uma interface ao invés de usar a classe diretamente?
* Como interfaces ajudam a diminuir acoplamento?
* Qual a diferença entre “o que um objeto faz” e “como ele faz”?
* Como saber se uma abstração faz sentido ou está sendo criada “porque sim”?
* Quando usar classe abstrata ao invés de interface?

---

## 6. Herança, Composição e Polimorfismo

* “É um” ou “tem um”? (herança vs composição)
* Quando composição é melhor que herança?
* Por que herança excessiva pode virar problema?
* O que o polimorfismo resolve além de “sobrescrever métodos”?
* Como o polimorfismo ajuda a evitar vários `if/else`?
* O que acontece quando subclasses quebram comportamento esperado?

---

## 7. SOLID

### SRP — Single Responsibility Principle

* O que significa uma classe ter apenas um motivo para mudar?

### OCP — Open/Closed Principle

* O que significa “aberto para extensão e fechado para modificação”?
* O design atual facilita mudança ou só funciona “por enquanto”?

### LSP — Liskov Substitution Principle

* O que acontece quando subclasses quebram comportamento esperado?

### ISP — Interface Segregation Principle

* Por que interfaces muito grandes são ruins?

### DIP — Dependency Inversion Principle

* Por que módulos de alto nível não deveriam depender dos de baixo nível?
* Por que depender de abstrações facilita testes?

---

## 8. Injeção de Dependência e Testabilidade

* Como injeção de dependência reduz acoplamento?
* O que torna um código difícil de testar?
* Por que mocks normalmente trabalham com interfaces?
* Se eu precisar trocar a tecnologia amanhã (ex: banco, gateway de pagamento, fila), quanto código precisaria mudar?

---

## 9. Modelagem de Domínio

* Quando uma regra deve ficar na entidade e não no service?
* O que diferencia objeto rico de objeto anêmico?
* Qual a diferença entre entidade e value object?
* O que caracteriza um bom domínio orientado a objetos?

---

## 10. Arquitetura e Evolução

* Como POO ajuda manutenção em sistemas grandes?
* Como eventos e contratos ajudam desacoplamento?
* O que torna uma arquitetura flexível de verdade?
* Meu código está preparado para extensão sem modificar tudo?
