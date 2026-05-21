# Perguntas PHP — Organizadas por Assunto

---

## 1. Introdução, Linguagem PHP e CLI

1. O que diferencia PHP de outras linguagens?
2. Como o PHP interpreta código?
3. O que acontece entre requisição e resposta?
4. Qual diferença entre PHP interpretado e compilado?
5. O que é CLI no PHP?
6. Quando usar PHP via terminal?
7. Qual diferença entre PHP web e PHP CLI?
8. Como argumentos funcionam na linha de comando?
9. O que são variáveis superglobais?
10. Como funciona o ciclo de vida de uma requisição PHP?
11. Qual diferença entre `include` e `require`?
12. Quando usar `require_once`?
13. O que significa tipagem gradual no PHP?
14. O que muda ao usar `strict_types`?
15. Como o PHP moderno evoluiu em relação ao PHP antigo?

---

## 2. Funções no PHP

1. Qual problema funções tentam resolver?
2. Quando faz sentido criar uma função?
3. O que indica que um trecho de código deveria virar função?
4. Como nomes de funções impactam legibilidade?
5. O que torna uma função difícil de entender?
6. O que caracteriza uma função com responsabilidade demais?
7. Quantos parâmetros começam a indicar problema?
8. Como identificar alta complexidade em uma função?
9. O que significa uma função ser coesa?
10. Quando uma função está muito acoplada?
11. O que diferencia função pura de função com efeitos colaterais?
12. Por que funções puras facilitam testes?
13. O que são efeitos colaterais na prática?
14. Como evitar funções gigantes?
15. Quando dividir uma função em várias menores?
16. Qual a diferença entre reutilização saudável e abstração prematura?
17. Como saber se uma função está fazendo mais do que deveria?
18. O retorno da função comunica intenção claramente?
19. Quando retornar `null` pode virar problema?
20. Faz sentido retornar boolean para tudo?
21. Quando usar exceptions ao invés de retornos booleanos?
22. Como tipagem ajuda funções?
23. Qual o impacto de funções sem tipo definido?
24. O que muda ao usar `strict_types` no PHP?
25. Como type hint melhora manutenção?
26. Qual a diferença entre parâmetro obrigatório e opcional?
27. Quando usar valor padrão em parâmetros?
28. Como evitar dependência excessiva dentro de funções?
29. O que acontece quando uma função depende de variáveis globais?
30. Por que funções globais podem gerar acoplamento?
31. Quando usar funções estáticas?
32. Qual a diferença entre função procedural e método de objeto?
33. Como evitar repetição de lógica usando funções?
34. Quando uma função utilitária começa a virar problema?
35. O que caracteriza uma "God Function"?
36. Como validar entrada de dados dentro de funções?
37. Onde deveria ficar a responsabilidade de validação?
38. Como exceptions ajudam controle de fluxo?
39. Quando try/catch em excesso pode indicar problema?
40. Como funções ajudam legibilidade?
41. Como pequenas funções ajudam refatoração?
42. Como medir qualidade de uma função?
43. Uma função deveria alterar objetos recebidos?
44. Quando imutabilidade faz sentido em funções?
45. Como evitar efeitos inesperados ao passar arrays e objetos?
46. O que muda ao passar parâmetros por referência?
47. Quando arrow functions fazem sentido?
48. Qual diferença entre closure e função comum?
49. Como callbacks ajudam desacoplamento?
50. Quando abstração em funções começa a complicar mais do que ajudar?
51. Como saber se a função comunica claramente intenção de negócio?
52. Se eu precisar alterar essa função amanhã, quantas outras quebram junto?

---

## 3. Strings

1. Qual a diferença entre `strlen()` e `mb_strlen()`?
2. Por que caracteres acentuados podem quebrar `strlen()`?
3. Quando usar funções `mb_*`?
4. O que `explode()` realmente retorna?
5. Qual a diferença entre `explode()` e `str_split()`?
6. Quando `implode()` faz sentido?
7. O que acontece se o delimitador não existir no `explode()`?
8. Qual diferença entre `trim()`, `ltrim()` e `rtrim()`?
9. Por que espaços invisíveis podem gerar bugs?
10. Quando usar `strtolower()` vs `mb_strtolower()`?
11. Qual a diferença entre `substr()` e `mb_substr()`?
12. O que acontece ao cortar caracteres UTF-8 incorretamente?
13. Como verificar se uma string contém outra?
14. Qual diferença entre `strpos()` e `str_contains()`?
15. Por que `strpos()` pode gerar bugs com `0`?
16. Quando usar `str_replace()`?
17. Qual diferença entre `preg_replace()` e `str_replace()`?
18. O que funções regex resolvem que funções simples não resolvem?
19. Quando regex começa a complicar mais do que ajudar?
20. Como validar formatos usando `preg_match()`?
21. O que significa uma regex ser gulosa?
22. Como evitar regex impossível de manter?
23. Quando usar `sprintf()`?
24. Como formatação melhora legibilidade?
25. O que `nl2br()` faz?
26. Quando `htmlspecialchars()` é obrigatório?
27. Qual problema `htmlentities()` tenta resolver?
28. Como evitar XSS manipulando strings?
29. Qual a diferença entre `addslashes()` e prepared statements?
30. Por que `addslashes()` não resolve SQL Injection?
31. O que `number_format()` resolve?
32. Como lidar com encoding em strings?
33. O que é UTF-8 na prática?
34. Como detectar encoding incorreto?
35. O que `utf8_encode()` fazia e por que é problemático hoje?
36. Como normalizar strings?
37. Como remover acentos de strings?
38. Quando usar `wordwrap()`?
39. O que `strcmp()` resolve?
40. Qual diferença entre comparação estrita e textual?
41. Como ordenar strings corretamente?
42. O que muda ao comparar strings com acentos?

---

## 4. Arrays

1. Qual a diferença entre array indexado e associativo?
2. Quando usar `array_map()`?
3. O que `array_filter()` resolve?
4. Como `array_reduce()` funciona?
5. Quando `foreach` é mais simples que funções funcionais?
6. O que `in_array()` faz?
7. Qual diferença entre `isset()` e `array_key_exists()`?
8. Por que `isset()` pode enganar com valores `null`?
9. O que `array_merge()` faz?
10. Qual diferença entre `+` e `array_merge()`?
11. Como ordenar arrays com `sort()`, `asort()` e `usort()`?
12. Quando usar `usort()`?
13. O que `array_values()` resolve?
14. O que `array_keys()` retorna?
15. Como transformar arrays em strings?
16. Como transformar strings em arrays?
17. Quando `explode()` + `array_map()` fazem sentido juntos?
18. Como evitar loops desnecessários em arrays?
19. O que `array_unique()` resolve?
20. Como buscar dados em arrays multidimensionais?
21. Quando um array começa a virar objeto?
22. Qual diferença entre coleção e array simples?
23. O que `compact()` e `extract()` fazem?
24. Por que `extract()` pode ser perigoso?
25. Como validar estrutura de arrays?
26. O que muda ao passar arrays por referência?
27. Como evitar mutação inesperada em arrays?

---

## 5. Datas

1. Qual a diferença entre `date()` e `DateTime`?
2. Por que `DateTime` costuma ser preferível?
3. O que `strtotime()` faz?
4. Quais problemas `strtotime()` pode causar?
5. Como trabalhar com timezone corretamente?
6. O que acontece ao ignorar timezone?
7. Como calcular diferença entre datas?
8. O que `DateInterval` resolve?
9. Como formatar datas no PHP?
10. Qual diferença entre timestamp e data formatada?
11. Como evitar bugs de horário de verão?
12. O que significa data imutável (`DateTimeImmutable`)?
13. Quando datas deveriam ser imutáveis?

---

## 6. Arquivos e IO

1. O que `file_get_contents()` faz?
2. Quando usar `fopen()` ao invés de `file_get_contents()`?
3. Como evitar carregar arquivos gigantes na memória?
4. O que `json_encode()` faz?
5. Como `json_decode()` funciona?
6. Qual diferença entre objeto e array no `json_decode()`?
7. Como tratar erro de JSON inválido?
8. O que `unlink()` faz?
9. Como validar upload de arquivos?
10. Qual risco de confiar apenas na extensão do arquivo?
11. Como manipular CSV no PHP?
12. O que `pathinfo()` resolve?

---

## 7. Tipagem e Validação

1. Qual diferença entre `==` e `===`?
2. Por que comparação frouxa pode gerar bugs?
3. O que `empty()` realmente verifica?
4. Quando `empty()` pode ser perigoso?
5. Qual diferença entre `is_null()` e `isset()`?
6. Como validar inteiros corretamente?
7. O que `filter_var()` resolve?
8. Quando usar `filter_input()`?
9. Como validar email corretamente?
10. Por que regex nem sempre é melhor para validação?

---

## 8. Funções Modernas do PHP

1. O que o operador `??` resolve?
2. Qual diferença entre `??` e `?:`?
3. Quando usar `match` ao invés de `switch`?
4. Como arrow functions simplificam callbacks?
5. O que `fn()` resolve?
6. Quando collections funcionais melhoram legibilidade?
7. O que `enum` resolve no PHP moderno?
8. Como `readonly` ajuda imutabilidade?
9. O que promoted properties resolvem no construtor?
10. Como attributes substituem annotations?

---

## 9. Orientação a Objetos (POO)

1. O que diferencia objeto de classe?
2. Como modelar entidades do mundo real sem copiar literalmente a realidade?
3. Como saber se estou usando POO ou apenas organizando funções dentro de classes?
4. Por que esconder detalhes internos de uma classe?
5. O que é encapsulamento na prática além da definição teórica?
6. Quando faz sentido deixar atributos privados?
7. O que pode acontecer se qualquer parte do sistema puder alterar estados livremente?
8. Por que getters/setters excessivos podem indicar problema?
9. Essa classe tem uma única responsabilidade?
10. O que faz um método ter responsabilidade demais?
11. Como evitar objetos "faz-tudo"?
12. Como identificar baixa coesão?
13. Se eu mudar uma regra de negócio, quantas classes precisam mudar junto?
14. O que indica que uma classe está muito acoplada?
15. Qual o custo de um alto acoplamento no crescimento do sistema?
16. Como separar regra de negócio de infraestrutura?
17. Por que "instanciar tudo dentro da classe" dificulta evolução?
18. Por que criar uma interface ao invés de usar a classe diretamente?
19. Como interfaces ajudam a diminuir acoplamento?
20. Qual a diferença entre "o que um objeto faz" e "como ele faz"?
21. Como saber se uma abstração faz sentido ou está sendo criada "porque sim"?
22. Quando usar classe abstrata ao invés de interface?
23. "É um" ou "tem um"? (herança vs composição)
24. Quando composição é melhor que herança?
25. Por que herança excessiva pode virar problema?
26. O que o polimorfismo resolve além de "sobrescrever métodos"?
27. Como o polimorfismo ajuda a evitar vários `if/else`?
28. O que acontece quando subclasses quebram comportamento esperado?
29. Como injeção de dependência reduz acoplamento?
30. O que torna um código difícil de testar?
31. Por que mocks normalmente trabalham com interfaces?
32. Se eu precisar trocar a tecnologia amanhã, quanto código precisaria mudar?
33. Quando uma regra deve ficar na entidade e não no service?
34. O que diferencia objeto rico de objeto anêmico?
35. Qual a diferença entre entidade e value object?
36. O que caracteriza um bom domínio orientado a objetos?
37. Como POO ajuda manutenção em sistemas grandes?
38. Como eventos e contratos ajudam desacoplamento?
39. O que torna uma arquitetura flexível de verdade?
40. O design atual facilita mudança ou só funciona "por enquanto"?

---

## 10. SOLID

1. Qual problema os princípios SOLID tentam resolver?
2. O que significa uma classe ter apenas um motivo para mudar? (SRP)
3. O que significa "aberto para extensão e fechado para modificação"? (OCP)
4. O que acontece quando subclasses quebram comportamento esperado? (LSP)
5. Por que interfaces muito grandes são ruins? (ISP)
6. Por que módulos de alto nível não deveriam depender dos de baixo nível? (DIP)
7. Por que depender de abstrações facilita testes?
8. Como cada princípio SOLID se relaciona com manutenção?

---

## 11. Objetos Imutáveis

1. O que significa um objeto ser imutável?
2. Por que impedir alteração de estado depois da criação?
3. Quais problemas objetos imutáveis tentam resolver?
4. O que muda quando um objeto não possui setters?
5. Como o construtor ajuda a garantir consistência?
6. O objeto nasce válido ou pode existir inválido?
7. Faz sentido permitir alteração desse objeto depois de criado?
8. O estado desse objeto representa valor ou identidade?
9. Quando retornar um novo objeto é melhor que alterar o atual?
10. Como imutabilidade reduz efeitos colaterais?
11. Como objetos imutáveis ajudam previsibilidade?
12. Como imutabilidade facilita testes?
13. Como imutabilidade ajuda concorrência?
14. Por que objetos compartilhados mutáveis podem gerar bugs?
15. Como identificar um objeto que deveria ser imutável?
16. Value Objects deveriam ser imutáveis?
17. Faz sentido um CPF mudar depois de criado?
18. Faz sentido um objeto Dinheiro ser mutável?
19. Qual diferença entre entidade mutável e Value Object imutável?
20. O que acontece quando muitas partes do sistema alteram o mesmo objeto?
21. Como objetos imutáveis ajudam encapsulamento?
22. O que muda ao usar `readonly` no PHP?
23. `readonly` sozinho torna um objeto totalmente imutável?
24. Como criar métodos que "alteram" sem modificar estado?
25. Qual diferença entre `$usuario->setNome("Maria")` e `$usuario->comNome("Maria")`?
26. Por que retornar nova instância pode ser mais seguro?
27. Como evitar estados inválidos em objetos?
28. O construtor deveria validar regras?
29. O que acontece quando permitimos `$produto->setPreco(-10)`?
30. Objetos vazios fazem sentido nesse domínio?
31. Qual diferença entre objeto vazio e objeto inválido?
32. Como Null Object se relaciona com imutabilidade?
33. Qual problema `null` excessivo pode causar?
34. Como objetos imutáveis ajudam modelagem de domínio?
35. O que caracteriza um objeto previsível?
36. Como imutabilidade reduz acoplamento?
37. Qual impacto da imutabilidade na memória?
38. Quando imutabilidade pode complicar demais?
39. Como equilibrar simplicidade e imutabilidade?
40. Toda classe deveria ser imutável?
41. Como saber quando imutabilidade vale a pena?
42. O domínio precisa de mudança de estado real ou apenas novos estados?
43. Meu objeto protege suas próprias regras ou qualquer parte do sistema pode quebrá-las?

---

## 12. Namespaces

1. Qual problema namespaces tentam resolver?
2. O que acontece quando duas classes possuem o mesmo nome?
3. Como namespaces ajudam organização do sistema?
4. Namespace é estrutura de pasta?
5. O que diferencia namespace lógico de diretório físico?
6. Como o autoload se relaciona com namespaces?
7. O que o PSR-4 resolve?
8. Por que namespaces ajudam projetos grandes?
9. Quando faz sentido criar subnamespaces?
10. Como evitar namespaces gigantes?
11. O namespace deveria refletir domínio ou tecnologia?
12. Qual diferença entre `App\Services` e `App\Domain`?
13. Como saber se um namespace está mal organizado?
14. O que indica excesso de profundidade em namespaces?
15. Quando um namespace começa a virar "pasta aleatória"?
16. Como namespaces ajudam desacoplamento?
17. O namespace deveria conhecer infraestrutura?
18. Como organizar namespaces em arquitetura limpa?
19. O que acontece quando tudo fica dentro de `App\Utils`?
20. Qual o impacto de classes utilitárias genéricas no projeto?
21. Como namespaces ajudam leitura de contexto?
22. O que `use` realmente faz?
23. `use` importa código ou apenas cria alias?
24. Qual diferença entre `use App\Services\UserService` e `new \App\Services\UserService()`?
25. Quando usar alias com `as`?
26. Como evitar conflitos de nomes?
27. O que significa referência totalmente qualificada?
28. Quando usar `\` no início do namespace?
29. Como funções globais se comportam dentro de namespaces?
30. O que acontece quando uma função não é encontrada no namespace atual?
31. Qual diferença entre classe global e classe namespaced?
32. Como namespaces ajudam modularização?
33. Como dividir bounded contexts usando namespaces?
34. O que diferencia namespace técnico de namespace de domínio?
35. Como organizar namespaces em DDD?
36. Faz sentido separar `Domain`, `Application` e `Infrastructure`?
37. Como namespaces ajudam testabilidade?
38. Como namespaces ajudam manutenção?
39. O que acontece quando classes de domínio dependem diretamente de Framework, ORM ou HTTP?
40. Como namespaces podem revelar acoplamento arquitetural?
41. Qual relação entre Composer e namespaces?
42. O que o autoload do Composer faz?
43. Por que evitar `require_once` manual em projetos modernos?
44. Como namespaces melhoram reutilização?
45. O que caracteriza um namespace saudável?
46. Se eu mover uma classe de lugar, quantos arquivos quebram?
47. Meu namespace comunica intenção ou apenas localização física?

---

## 13. Traits

1. Qual problema traits tentam resolver?
2. Quando usar trait ao invés de herança?
3. Traits ajudam reutilização ou escondem problema de design?
4. Como evitar excesso de traits?
5. Qual diferença entre trait e composição?
6. Quando trait começa a gerar acoplamento?

---

## 14. Exceções

1. Qual diferença entre erro e exceção?
2. Quando lançar exceptions?
3. Quando NÃO usar exceptions?
4. Como exceptions ajudam fluxo de erro?
5. O que torna um try/catch excessivo?
6. Como criar exceptions de domínio?
7. Exceptions substituem validação?

---

## 15. Manipulação de Bancos de Dados com PDO

1. Qual problema PDO resolve?
2. Qual diferença entre PDO e mysqli?
3. O que são prepared statements?
4. Como prepared statements evitam SQL Injection?
5. Por que `addslashes()` não resolve SQL Injection?
6. Como conectar múltiplos bancos com PDO?
7. O que `fetch()` retorna?
8. Qual diferença entre `fetch()` e `fetchAll()`?
9. Quando usar transações?
10. Como tratar erros de banco corretamente?
11. Como evitar SQL espalhado pelo sistema?

---

## 16. Transações e ACID

1. O que é uma transação?
2. Qual problema transações resolvem?
3. O que significa ACID?
4. Quando usar commit e rollback?
5. O que acontece se uma operação falhar no meio?
6. Como garantir consistência dos dados?
7. Qual relação entre transações e concorrência?
8. Quando transações longas viram problema?
9. Como evitar deadlocks?
10. O domínio deveria controlar transação?

---

## 17. HTTP e Manipulação de Conteúdos via HTTP

1. O que é HTTP?
2. O que significa requisição e resposta?
3. Qual diferença entre GET e POST?
4. Quando usar PUT, PATCH e DELETE?
5. O que são headers?
6. O que é body da requisição?
7. Como funciona status code?
8. Qual diferença entre 200, 201, 400, 401, 403, 404 e 500?
9. O que é Content-Type?
10. Como enviar JSON em PHP?
11. Como consumir APIs HTTP?
12. O que é stateless?
13. Como sessões funcionam no PHP?
14. Qual diferença entre cookie e sessão?

---

## 18. APIs RESTful

1. O que torna uma API RESTful?
2. O que é recurso em REST?
3. Como modelar rotas corretamente?
4. Qual diferença entre `/users` e `/getUsers`?
5. Quando usar verbo HTTP corretamente?
6. O que torna uma API previsível?
7. Como status codes ajudam clientes?
8. O que significa idempotência?
9. O que é versionamento de API?
10. Como evitar acoplamento entre front e API?
11. O que diferencia REST de RPC?
12. Como documentar APIs?

---

## 19. MVC

1. Qual problema o MVC tenta resolver?
2. Por que separar Model, View e Controller?
3. O controller deveria conter regra de negócio?
4. O que acontece quando o controller começa a fazer "tudo"?
5. Qual a responsabilidade real da View?
6. A View deveria conhecer banco de dados?
7. O Model representa dados ou comportamento?
8. Como evitar controllers gigantes?
9. O que diferencia MVC de arquitetura em camadas?
10. Onde a validação deveria acontecer?
11. Qual a diferença entre fluxo HTTP e regra de negócio?
12. O controller deveria saber qual banco está sendo usado?
13. O que acontece quando misturamos HTML, SQL e regra de negócio no mesmo lugar?
14. Como MVC melhora manutenção?
15. Como MVC ajuda testabilidade?
16. O que indica que o MVC virou apenas "organização de pastas"?
17. Como saber se minha camada Model está anêmica?
18. O que deveria acontecer no Controller antes de chamar a regra de negócio?
19. MVC resolve arquitetura sozinho?
20. Como evitar acoplamento entre camadas?

---

## 20. DAO (Data Access Object)

1. Qual problema o DAO tenta resolver?
2. Por que isolar acesso a banco em uma camada?
3. O que acontece quando SQL fica espalhado pelo sistema?
4. DAO deveria conter regra de negócio?
5. Qual a responsabilidade real de um DAO?
6. DAO deveria conhecer HTTP ou Controller?
7. O que diferencia DAO de Service?
8. Como DAO ajuda manutenção?
9. Como DAO reduz repetição de código?
10. Qual o problema de acessar banco diretamente no Controller?
11. Como DAO ajuda troca de banco de dados?
12. O DAO deveria retornar entidades ou arrays?
13. O que acontece quando um DAO começa a fazer "tudo"?
14. Como saber se um DAO está muito acoplado?
15. Um DAO deveria abrir e fechar conexão sozinho?
16. Como testar código que depende de DAO?
17. Qual o impacto de SQL hardcoded espalhado no sistema?
18. DAO é responsável por regras de negócio ou persistência?
19. Como DAO ajuda organização do sistema?
20. Qual diferença entre CRUD e regra de domínio dentro do DAO?

---

## 21. Repository Pattern

1. Qual problema o Repository tenta resolver?
2. O que diferencia Repository de DAO?
3. Repository representa tabela ou coleção de objetos?
4. O Repository deveria conhecer regra de negócio?
5. O domínio deveria saber qual banco está sendo usado?
6. Por que o Repository normalmente trabalha com entidades?
7. Repository é mais próximo do domínio ou da infraestrutura?
8. Como Repository ajuda DDD?
9. O que significa "persistência transparente"?
10. O Repository deveria expor SQL?
11. Como Repository ajuda desacoplamento?
12. O que muda ao trocar MySQL por MongoDB usando Repository?
13. Qual a diferença entre Repository e Active Record?
14. O Repository deveria retornar entidades válidas?
15. Como saber se um Repository ficou genérico demais?
16. Faz sentido criar Repository para tudo?
17. Repository substitui Service?
18. Repository deveria conter regras complexas?
19. Como Repository melhora testabilidade?
20. O que diferencia um Repository rico de um simples CRUD?

---

## 22. Repository em Banco Relacional

1. Como mapear entidades para tabelas relacionais?
2. O Repository deveria conhecer joins?
3. Como evitar SQL complexo vazando para o domínio?
4. O que muda ao trabalhar Repository com ORM?
5. Como lidar com relacionamentos 1:N e N:N?
6. O Repository deveria carregar tudo automaticamente?
7. O que é eager loading e lazy loading?
8. Como evitar problema de N+1 queries?
9. Qual o impacto de um Repository muito genérico em banco relacional?
10. Como manter entidades desacopladas do banco?
11. Quando usar query builder ao invés de ORM?
12. Como Repository ajuda na troca de ORM?
13. Como tratar transações dentro do Repository?
14. O Repository deveria controlar commit e rollback?
15. Como evitar acoplamento entre entidade e estrutura da tabela?
16. Faz sentido um Repository por agregado?
17. Como modelagem relacional impacta o domínio?
18. Como evitar que Repository vire apenas um "DAO gourmet"?
19. O que diferencia persistência de modelagem de domínio?
20. O domínio deveria saber que existe um banco relacional?

---

## 23. Composer

1. Qual problema o Composer tenta resolver?
2. Como projetos PHP funcionavam antes do Composer?
3. O que muda ao usar gerenciamento de dependências?
4. O que é uma dependência na prática?
5. Por que evitar baixar bibliotecas manualmente?
6. Como o Composer ajuda organização do projeto?
7. O que acontece quando diferentes bibliotecas precisam de versões diferentes?
8. Como o Composer resolve conflitos de dependência?
9. O que o arquivo `composer.json` representa?
10. Qual diferença entre `composer.json` e `composer.lock`?
11. Por que o `composer.lock` é importante em equipe?
12. Quando commitar o `composer.lock`?
13. O que acontece se apagar a pasta `vendor/`?
14. O que o comando `composer install` faz?
15. Qual diferença entre `composer install` e `composer update`?
16. Quando usar `update` pode ser perigoso?
17. O que significa versionamento semântico?
18. Qual diferença entre `^`, `~` e `*` nas versões?
19. Como escolher versões de dependências corretamente?
20. O que significa uma biblioteca ser compatível?
21. Como evitar "dependency hell"?
22. O que o autoload do Composer resolve?
23. Como o Composer carrega classes automaticamente?
24. Qual relação entre Composer e PSR-4?
25. O que acontece quando namespaces não seguem PSR-4?
26. Como configurar autoload no `composer.json`?
27. Qual diferença entre `autoload` e `autoload-dev`?
28. O que o comando `composer dump-autoload` faz?
29. Quando usar otimização de autoload?
30. Como o Composer ajuda modularização?
31. O que significa publicar um pacote?
32. Como criar uma biblioteca PHP reutilizável?
33. O que é o Packagist?
34. Como instalar dependências de desenvolvimento?
35. Qual diferença entre `require` e `require-dev`?
36. PHPUnit deveria ficar em `require-dev`?
37. Como Composer ajuda testes?
38. Como Composer melhora manutenção?
39. O que acontece quando um projeto depende fortemente de muitas bibliotecas?
40. Como avaliar se vale a pena instalar uma dependência?
41. Quando uma dependência pequena demais pode ser exagero?
42. Como evitar acoplamento excessivo a frameworks?
43. O domínio da aplicação deveria depender diretamente do framework?
44. Como Composer ajuda arquitetura limpa?
45. O que caracteriza um projeto PHP moderno?
46. Faz sentido usar Composer em projetos pequenos?
47. Como Composer facilita integração contínua?
48. O que significa um projeto ser reproduzível?
49. Como garantir que todos da equipe usem as mesmas versões?
50. O que acontece quando o ambiente local difere do servidor?
51. Como Composer ajuda deploy?
52. Como identificar dependências abandonadas?
53. O que significa uma biblioteca ser mantida ativamente?
54. Como segurança se relaciona com dependências?
55. O que acontece quando uma dependência possui vulnerabilidade?
56. Como Composer ajuda atualização segura?
57. O projeto realmente precisa dessa dependência ou estamos terceirizando lógica simples demais?