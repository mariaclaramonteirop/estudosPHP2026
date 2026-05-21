# Respostas PHP — Organizadas por Assunto

---

## 1. Introdução, Linguagem PHP e CLI

**1. O que diferencia PHP de outras linguagens?**
PHP é uma linguagem interpretada, de tipagem dinâmica (com suporte a tipagem gradual), criada originalmente para web. Executa no lado do servidor, gera HTML dinamicamente e integra nativamente com bancos de dados. Diferente de linguagens compiladas, não exige build; o interpretador lê e executa o código diretamente.

**2. Como o PHP interpreta código?**
O arquivo `.php` é lido pelo motor Zend Engine, que converte o código em opcodes (instruções de baixo nível) e os executa. Com OPcache ativado, os opcodes são armazenados em memória, evitando recompilação a cada requisição.

**3. O que acontece entre requisição e resposta?**
O servidor web (Apache/Nginx) recebe a requisição HTTP, repassa ao processo PHP-FPM ou módulo PHP, que executa o script, processa dados, acessa banco se necessário e retorna a resposta (HTML, JSON, etc.) ao cliente.

**4. Qual diferença entre PHP interpretado e compilado?**
PHP é interpretado: o código é lido e executado em tempo de execução, sem etapa de compilação prévia. Linguagens compiladas (C, Go, Rust) geram binário nativo antes da execução, resultando em desempenho maior, mas exigindo build. PHP com OPcache aproxima o desempenho compilando para opcodes em memória.

**5. O que é CLI no PHP?**
CLI (Command Line Interface) permite executar scripts PHP diretamente no terminal, sem servidor web. Usado para scripts de manutenção, workers, migrações, tarefas agendadas (cron jobs) e ferramentas de linha de comando.

**6. Quando usar PHP via terminal?**
Para tarefas que não precisam de HTTP: processar filas de mensagens, importar dados, rodar migrations, enviar e-mails em lote, gerar relatórios, executar testes automatizados.

**7. Qual diferença entre PHP web e PHP CLI?**
No PHP web, o ciclo de vida é: recebe requisição HTTP → executa script → devolve resposta → encerra processo. No CLI, não há requisição HTTP; o script recebe argumentos via `$argv`, acessa `STDIN/STDOUT/STDERR` e roda até concluir ou ser interrompido.

**8. Como argumentos funcionam na linha de comando?**
Acessados via `$argv` (array de strings) e `$argc` (quantidade de argumentos). O índice 0 é sempre o nome do script. Exemplo: `php script.php foo bar` resulta em `$argv = ['script.php', 'foo', 'bar']`.

**9. O que são variáveis superglobais?**
Arrays globais disponíveis em qualquer escopo do PHP sem necessidade de declaração. Principais: `$_GET`, `$_POST`, `$_SERVER`, `$_SESSION`, `$_COOKIE`, `$_FILES`, `$_ENV`, `$_REQUEST`, `$GLOBALS`.

**10. Como funciona o ciclo de vida de uma requisição PHP?**
1. Cliente envia requisição HTTP. 2. Servidor web repassa ao PHP. 3. PHP inicializa variáveis superglobais. 4. Executa o script (autoload, roteamento, lógica). 5. Gera resposta (HTML, JSON). 6. Envia resposta ao cliente. 7. Encerra o processo e libera memória.

**11. Qual diferença entre `include` e `require`?**
Ambos incluem arquivos externos. A diferença está no comportamento em caso de falha: `include` emite um `warning` e continua a execução; `require` emite um `fatal error` e encerra o script.

**12. Quando usar `require_once`?**
Quando o arquivo deve ser incluído exatamente uma vez, evitando redeclaração de classes ou funções. Útil para arquivos de configuração e definições. Em projetos modernos com Composer, o autoload torna `require_once` desnecessário na maioria dos casos.

**13. O que significa tipagem gradual no PHP?**
O PHP permite usar ou não type hints. Sem `strict_types`, tipos são convertidos automaticamente (coerção). Com `strict_types=1`, o PHP exige que os tipos sejam exatamente os declarados, sem conversão silenciosa.

**14. O que muda ao usar `strict_types`?**
Com `declare(strict_types=1)` no topo do arquivo, o PHP passa a exigir tipos exatos em chamadas de função/método dentro daquele arquivo. Passar `"5"` para um parâmetro `int` gera `TypeError` em vez de conversão silenciosa.

**15. Como o PHP moderno evoluiu em relação ao PHP antigo?**
PHP moderno (8.x) trouxe: tipagem forte, match expression, named arguments, enums, fibers, readonly properties, promoted properties, union types, nullsafe operator (`?->`), atributos nativos e JIT compiler. O PHP antigo era procedural, sem namespaces, sem autoload, sem tipagem.

---

## 2. Funções no PHP

**1. Qual problema funções tentam resolver?**
Evitar repetição de código, organizar lógica em blocos com nome e responsabilidade, permitir reuso e facilitar manutenção.

**2. Quando faz sentido criar uma função?**
Quando um bloco de código se repete, quando uma lógica tem responsabilidade clara e nomeável, ou quando a complexidade de um trecho prejudica leitura.

**3. O que indica que um trecho de código deveria virar função?**
Repetição (usado em mais de um lugar), complexidade que obscurece o fluxo principal, lógica que pode ser nomeada com clareza.

**4. Como nomes de funções impactam legibilidade?**
Um bom nome comunica intenção sem exigir leitura do corpo. `calcularImpostoSobreRenda()` é legível; `calc()` não diz nada. Nomes ruins forçam quem lê a entrar na função para entender o que ela faz.

**5. O que torna uma função difícil de entender?**
Nome genérico, muitos parâmetros, lógica misturada (faz várias coisas), efeitos colaterais ocultos, dependência de variáveis globais, retornos inconsistentes.

**6. O que caracteriza uma função com responsabilidade demais?**
Faz mais de uma coisa: valida, acessa banco, formata saída e envia e-mail ao mesmo tempo. Se o nome precisar de "e" para descrever o que faz, provavelmente tem responsabilidade demais.

**7. Quantos parâmetros começam a indicar problema?**
A partir de 3–4 parâmetros, a função começa a ficar difícil de chamar e entender. Mais de 4 geralmente indica que os dados deveriam ser agrupados em um objeto ou array associativo.

**8. Como identificar alta complexidade em uma função?**
Muitos `if/else` aninhados, múltiplos loops, tratamentos de casos especiais acumulados. A complexidade ciclomática mede isso: cada `if`, `for`, `while`, `case` soma +1 ao caminho possível.

**9. O que significa uma função ser coesa?**
Faz uma única coisa bem-definida. Todos os seus passos internos contribuem para o mesmo objetivo.

**10. Quando uma função está muito acoplada?**
Quando depende de classes concretas instanciadas internamente, de variáveis globais ou de estados externos sem recebê-los como parâmetro.

**11. O que diferencia função pura de função com efeitos colaterais?**
Função pura: dado o mesmo input, sempre retorna o mesmo output e não altera nada externo. Função com efeitos colaterais: altera estado externo (banco, arquivo, variável global, propriedade de objeto).

**12. Por que funções puras facilitam testes?**
Não dependem de estado externo. Para testar, basta chamar com os inputs e verificar o retorno. Não precisam de mocks ou setup de banco.

**13. O que são efeitos colaterais na prática?**
Gravar no banco, enviar e-mail, modificar variável global, alterar propriedade de objeto recebido, escrever em arquivo, chamar API externa.

**14. Como evitar funções gigantes?**
Extração de subfunções com nomes claros, cada uma responsável por uma etapa. O corpo da função principal deve ler como uma lista de passos de alto nível.

**15. Quando dividir uma função em várias menores?**
Quando o corpo tem mais de ~15–20 linhas, quando contém blocos claramente distintos, quando ler o código exige entender muitos detalhes ao mesmo tempo.

**16. Qual a diferença entre reutilização saudável e abstração prematura?**
Reutilização saudável: extrair código que já se repetiu. Abstração prematura: criar generalizações antes de ter dois casos reais para justificar, tornando o código mais complexo sem benefício.

**17. Como saber se uma função está fazendo mais do que deveria?**
Tente descrevê-la em uma frase sem usar "e" ou "também". Se não conseguir, ela faz mais de uma coisa.

**18. O retorno da função comunica intenção claramente?**
O tipo de retorno deve ser previsível e consistente. Retornar `bool`, `null`, `array` ou objeto dependendo do caminho confunde quem usa a função.

**19. Quando retornar `null` pode virar problema?**
Quando quem chama a função precisa sempre verificar se o retorno é `null` antes de usar. Isso espalha verificações pelo sistema e pode gerar `NullPointerException`.

**20. Faz sentido retornar boolean para tudo?**
Não. Boolean só diz "deu certo ou não". Quando há razão para a falha, exceptions ou objetos de resultado comunicam melhor o motivo.

**21. Quando usar exceptions ao invés de retornos booleanos?**
Quando a falha é excepcional e o chamador precisa saber o motivo. `false` não explica por que falhou; uma exception com mensagem clara, sim.

**22. Como tipagem ajuda funções?**
Define o contrato da função: o que ela espera receber e o que garante retornar. Reduz bugs por tipos inesperados e melhora autocompletar e análise estática.

**23. Qual o impacto de funções sem tipo definido?**
Qualquer valor pode ser passado ou retornado. Erros de tipo só aparecem em tempo de execução, e frequentemente em contextos distantes da origem do problema.

**24. O que muda ao usar `strict_types` no PHP?**
O PHP para de fazer coerção silenciosa de tipos. `"5"` passado para `int` vira `TypeError`. Força os chamadores a passarem o tipo correto.

**25. Como type hint melhora manutenção?**
Documenta a interface da função no próprio código, permite ferramentas de análise estática (PHPStan, Psalm) detectarem erros antes de executar.

**26. Qual a diferença entre parâmetro obrigatório e opcional?**
Obrigatório: deve ser fornecido em toda chamada. Opcional: possui valor padrão e pode ser omitido. Parâmetros obrigatórios devem vir antes dos opcionais.

**27. Quando usar valor padrão em parâmetros?**
Quando há um comportamento padrão razoável que se aplica na maioria dos casos, evitando que o chamador precise sempre fornecê-lo.

**28. Como evitar dependência excessiva dentro de funções?**
Receber dependências como parâmetro (injeção) em vez de instanciar dentro da função. Isso torna o comportamento controlável de fora.

**29. O que acontece quando uma função depende de variáveis globais?**
O comportamento depende de estado externo invisível. Dificulta testes, causa bugs difíceis de rastrear e torna a função impossível de reusar em contextos diferentes.

**30. Por que funções globais podem gerar acoplamento?**
Qualquer arquivo pode chamá-las sem declarar dependência explícita. Mudanças na função afetam todos os chamadores de forma implícita.

**31. Quando usar funções estáticas?**
Para operações que não dependem de estado de objeto e não precisam de injeção de dependência. Útil para helpers puros (transformações, cálculos). Evitar para lógica que precisa ser mockada em testes.

**32. Qual a diferença entre função procedural e método de objeto?**
Função procedural: existe de forma isolada, sem contexto de estado. Método de objeto: opera sobre o estado do objeto ao qual pertence, podendo acessar `$this`.

**33. Como evitar repetição de lógica usando funções?**
Identificar trechos idênticos ou muito similares, extrair para uma função com parâmetros que cubram as variações, e substituir todas as ocorrências pela chamada.

**34. Quando uma função utilitária começa a virar problema?**
Quando acumula responsabilidades não relacionadas, quando qualquer coisa que não tem lugar óbvio vai parar nela, quando o arquivo de "utils" cresce sem critério.

**35. O que caracteriza uma "God Function"?**
Uma função que faz tudo: valida input, acessa banco, processa regras, formata saída, envia notificação. Tem centenas de linhas, é impossível de testar isoladamente e qualquer mudança pode quebrar algo inesperado.

**36. Como validar entrada de dados dentro de funções?**
Validar no início da função (guard clauses), lançar exception se o input for inválido, retornar cedo. Não misturar validação com lógica de negócio no meio do código.

**37. Onde deveria ficar a responsabilidade de validação?**
Depende do contexto: validação de formato (email, CPF) pode ficar em Value Objects. Validação de regra de negócio fica na entidade ou no service de domínio. Validação de input HTTP fica no controller ou em um objeto de request.

**38. Como exceptions ajudam controle de fluxo?**
Permitem interromper o fluxo em qualquer ponto e tratar o erro no nível adequado, sem precisar propagar `false` ou `null` por várias camadas.

**39. Quando try/catch em excesso pode indicar problema?**
Quando exceptions são usadas para fluxo normal (não excepcional), quando cada chamada tem seu próprio try/catch sem critério, ou quando exceptions são capturadas e ignoradas silenciosamente.

**40. Como funções ajudam legibilidade?**
Substituem blocos anônimos de código por nomes que comunicam intenção. Quem lê o código principal vê "o quê" acontece, não "como".

**41. Como pequenas funções ajudam refatoração?**
Cada função tem escopo limitado. Refatorar uma não afeta as outras. É mais fácil mover, renomear ou substituir uma função pequena do que extrair parte de uma gigante.

**42. Como medir qualidade de uma função?**
Clareza do nome, tamanho (linhas), número de parâmetros, número de responsabilidades, facilidade de testar isoladamente, consistência do retorno.

**43. Uma função deveria alterar objetos recebidos?**
Geralmente não. Alterar objetos recebidos cria efeitos colaterais inesperados para quem chamou. Prefira retornar um novo valor ou uma nova instância.

**44. Quando imutabilidade faz sentido em funções?**
Quando a função recebe dados que não devem ser alterados (Value Objects, configurações), quando o resultado deve ser um novo valor derivado do original.

**45. Como evitar efeitos inesperados ao passar arrays e objetos?**
Arrays em PHP são passados por valor (cópia), mas objetos são passados por referência de handle. Para evitar mutação de objetos, clonar antes de modificar ou retornar nova instância.

**46. O que muda ao passar parâmetros por referência?**
Com `&$param`, a função altera a variável original do chamador. Útil em casos específicos (ex: funções nativas como `sort()`), mas deve ser evitado em código de negócio por criar efeitos colaterais ocultos.

**47. Quando arrow functions fazem sentido?**
Em callbacks simples de uma expressão usados com `array_map`, `array_filter`, `usort`. Capturam o escopo externo automaticamente com `use` implícito.

**48. Qual diferença entre closure e função comum?**
Closure (função anônima) pode capturar variáveis do escopo onde foi criada via `use`. Pode ser atribuída a variável, passada como argumento e retornada. Função comum tem nome fixo e escopo global.

**49. Como callbacks ajudam desacoplamento?**
Permitem que a lógica específica seja injetada de fora, tornando a função genérica. Ex: `array_filter($items, $criterio)` — a função não sabe o critério, quem chama decide.

**50. Quando abstração em funções começa a complicar mais do que ajudar?**
Quando a generalização adiciona parâmetros, switches e condicionais para cobrir casos que ainda não existem. Abstraia quando houver pelo menos dois casos reais, não por antecipação.

**51. Como saber se a função comunica claramente intenção de negócio?**
Se alguém do negócio (não técnico) ler o nome e entender o que ela faz sem explicação adicional, a função comunica bem. `aprovarPedido()` é claro; `processar()` não é.

**52. Se eu precisar alterar essa função amanhã, quantas outras quebram junto?**
Esse é o teste de acoplamento. Se mudar a assinatura ou comportamento de uma função quebra muitos outros lugares, ela está muito acoplada. Funções com contrato estável e bem definido minimizam esse impacto.

---

## 3. Strings

**1. Qual a diferença entre `strlen()` e `mb_strlen()`?**
`strlen()` conta bytes, não caracteres. Em UTF-8, um caractere acentuado ocupa 2 ou mais bytes. `mb_strlen()` conta caracteres reais, respeitando o encoding.

**2. Por que caracteres acentuados podem quebrar `strlen()`?**
`strlen("ação")` retorna 6 (bytes), não 4 (caracteres). Operações baseadas nesse valor produzirão resultados incorretos ao cortar ou posicionar strings com acentos.

**3. Quando usar funções `mb_*`?**
Sempre que trabalhar com strings que podem conter caracteres fora do ASCII: textos em português, espanhol, árabe, japonês, etc. Especialmente em `strlen`, `substr`, `strtolower`, `strtoupper`, `strpos`.

**4. O que `explode()` realmente retorna?**
Retorna um array de strings resultante da divisão da string pelo delimitador fornecido.

**5. Qual a diferença entre `explode()` e `str_split()`?**
`explode()` divide por um delimitador (string). `str_split()` divide em pedaços de tamanho fixo (em bytes). Para caracteres multibyte, `str_split()` pode cortar caracteres no meio.

**6. Quando `implode()` faz sentido?**
Para unir elementos de um array em uma string com um separador. Ex: `implode(", ", $nomes)` → `"Ana, Bia, Carlos"`.

**7. O que acontece se o delimitador não existir no `explode()`?**
Retorna um array com um único elemento contendo a string original inteira.

**8. Qual diferença entre `trim()`, `ltrim()` e `rtrim()`?**
`trim()` remove espaços (e outros caracteres brancos) de ambos os lados. `ltrim()` remove só da esquerda. `rtrim()` remove só da direita.

**9. Por que espaços invisíveis podem gerar bugs?**
Comparações de string falham porque `"João"` ≠ `"João "`. Validações de campos passam com valor aparentemente vazio mas com espaço.

**10. Quando usar `strtolower()` vs `mb_strtolower()`?**
`strtolower()` funciona apenas para ASCII. Para converter "Ação" para "ação" corretamente, usar `mb_strtolower()` com o encoding adequado.

**11. Qual a diferença entre `substr()` e `mb_substr()`?**
`substr()` opera em bytes. `mb_substr()` opera em caracteres reais. Cortar uma string UTF-8 com `substr()` pode resultar em caracteres corrompidos.

**12. O que acontece ao cortar caracteres UTF-8 incorretamente?**
O byte de meio de um caractere multibyte é incluído, gerando caractere inválido que aparece como símbolo estranho (ex: `?` ou `�`).

**13. Como verificar se uma string contém outra?**
PHP 8+: `str_contains($string, $busca)`. PHP anterior: `strpos($string, $busca) !== false`.

**14. Qual diferença entre `strpos()` e `str_contains()`?**
`strpos()` retorna a posição (inteiro) ou `false`. `str_contains()` retorna `bool`. `str_contains` é mais legível e elimina o bug clássico de `strpos() == 0` ser interpretado como `false`.

**15. Por que `strpos()` pode gerar bugs com `0`?**
Se a substring está no início da string, `strpos()` retorna `0`. Com comparação frouxa (`if (strpos(...))`) o `0` é tratado como `false`, gerando falso negativo. Sempre usar `!== false`.

**16. Quando usar `str_replace()`?**
Para substituições simples de uma string por outra, sem necessidade de padrões. Mais rápido e legível que regex para casos simples.

**17. Qual diferença entre `preg_replace()` e `str_replace()`?**
`str_replace()` substitui strings literais. `preg_replace()` substitui com base em padrão regex. Use `str_replace()` quando o padrão é fixo; `preg_replace()` quando envolve variações.

**18. O que funções regex resolvem que funções simples não resolvem?**
Padrões variáveis: "qualquer sequência de dígitos", "string que começa com maiúscula seguida de letras", "espaços múltiplos". Coisas que não podem ser descritas por string literal.

**19. Quando regex começa a complicar mais do que ajudar?**
Quando a expressão tem mais de 2–3 linhas de padrão, quando não há como entender sem executar, quando um método específico (como `DateTime::createFromFormat`) resolveria melhor.

**20. Como validar formatos usando `preg_match()`?**
`preg_match('/^\d{3}\.\d{3}\.\d{3}-\d{2}$/', $cpf)` — retorna `1` se casar, `0` se não. Use grupos de captura para extrair partes.

**21. O que significa uma regex ser gulosa?**
Por padrão, quantificadores como `.*` tentam casar o máximo possível. `.*?` é não-guloso (lazy) e casa o mínimo necessário.

**22. Como evitar regex impossível de manter?**
Comentar o padrão com `VERBOSE` (flag `x` no PCRE), dividir em partes nomeadas, usar funções específicas quando disponíveis, documentar o formato esperado.

**23. Quando usar `sprintf()`?**
Para formatar strings com múltiplos valores inseridos em posições específicas. Mais legível que concatenação: `sprintf("Olá, %s! Você tem %d mensagens.", $nome, $total)`.

**24. Como formatação melhora legibilidade?**
Separa a estrutura da string dos valores variáveis. Fica claro o template e o que é dinâmico.

**25. O que `nl2br()` faz?**
Insere `<br>` antes de cada quebra de linha `\n` na string. Útil para exibir texto com quebras de linha em HTML.

**26. Quando `htmlspecialchars()` é obrigatório?**
Sempre que exibir dados do usuário em HTML. Converte `<`, `>`, `&`, `"`, `'` em entidades HTML, impedindo injeção de tags.

**27. Qual problema `htmlentities()` tenta resolver?**
Similar ao `htmlspecialchars()`, mas converte todos os caracteres que têm entidade HTML equivalente. Mais abrangente, mas `htmlspecialchars()` geralmente é suficiente.

**28. Como evitar XSS manipulando strings?**
Sempre escapar output com `htmlspecialchars($valor, ENT_QUOTES, 'UTF-8')` antes de exibir em HTML. Nunca confiar em input do usuário sem escapar.

**29. Qual a diferença entre `addslashes()` e prepared statements?**
`addslashes()` escapa aspas com barra invertida — proteção fraca e incompleta contra SQL Injection. Prepared statements parametrizam a query, separando código SQL de dados, eliminando o risco.

**30. Por que `addslashes()` não resolve SQL Injection?**
Depende do encoding e contexto. Em certas codificações multibyte, a barra invertida pode fazer parte de um caractere legítimo. Prepared statements são a solução correta.

**31. O que `number_format()` resolve?**
Formata números com separadores de milhar e casas decimais: `number_format(1234567.89, 2, ',', '.')` → `"1.234.567,89"`.

**32. Como lidar com encoding em strings?**
Garantir que o arquivo PHP, o banco de dados, a conexão e o HTTP response estejam todos em UTF-8. Usar `mb_*` para operações de string.

**33. O que é UTF-8 na prática?**
Um encoding de comprimento variável que representa todos os caracteres Unicode. Caracteres ASCII ocupam 1 byte; caracteres especiais ocupam 2 a 4 bytes.

**34. Como detectar encoding incorreto?**
`mb_detect_encoding($string)` tenta identificar o encoding. Strings com encoding errado aparecem com caracteres `?` ou `â€` no lugar de acentos.

**35. O que `utf8_encode()` fazia e por que é problemático hoje?**
Convertia Latin-1 (ISO-8859-1) para UTF-8. Foi descontinuado no PHP 8.2 por ser limitado. Use `mb_convert_encoding($string, 'UTF-8', 'ISO-8859-1')`.

**36. Como normalizar strings?**
Converter para lowercase, remover espaços extras com `trim()`, normalizar encoding. Para comparações, remover acentos ou usar collation adequada no banco.

**37. Como remover acentos de strings?**
`transliterator_transliterate('Any-Latin; Latin-ASCII; Lower()', $string)` usando a extensão Intl. Ou substituição manual via `str_replace()` com arrays de mapeamento.

**38. Quando usar `wordwrap()`?**
Para quebrar linhas longas em uma largura máxima de caracteres. Útil para e-mails em texto puro ou terminais.

**39. O que `strcmp()` resolve?**
Compara duas strings lexicograficamente. Retorna 0 se iguais, negativo se a primeira é menor, positivo se maior. Útil para ordenação.

**40. Qual diferença entre comparação estrita e textual?**
`===` compara tipo e valor (estrita). `strcmp()` compara conteúdo de strings como sequências de bytes. `"10" === "10"` é `true`; `"10" > "9"` com `==` pode ser surpreendente por comparação numérica.

**41. Como ordenar strings corretamente?**
`usort()` com `strcmp()` ou `strcoll()` para respeitar locale. Para ordem natural (`file1, file2, file10`), usar `strnatcmp()`.

**42. O que muda ao comparar strings com acentos?**
A ordem lexicográfica byte a byte não corresponde à ordem alfabética do idioma. Para ordenação correta, usar `Collator` da extensão Intl ou `strcoll()` com locale configurado.

---

## 4. Arrays

**1. Qual a diferença entre array indexado e associativo?**
Indexado: chaves são inteiros automáticos (`[0, 1, 2, ...]`). Associativo: chaves são strings definidas pelo programador (`['nome' => 'Ana', 'idade' => 30]`).

**2. Quando usar `array_map()`?**
Para transformar cada elemento de um array aplicando uma função, retornando novo array com os resultados. Não altera o original.

**3. O que `array_filter()` resolve?**
Remove elementos que não satisfazem um critério. Retorna novo array com os elementos que passaram no callback (ou os "truthy" sem callback).

**4. Como `array_reduce()` funciona?**
Acumula um valor percorrendo o array. Cada iteração recebe o acumulador e o elemento atual, retorna novo acumulador. Útil para somar, concatenar ou construir estrutura a partir de lista.

**5. Quando `foreach` é mais simples que funções funcionais?**
Quando a lógica é complexa demais para um callback de uma linha, quando precisar de múltiplas operações no mesmo loop, ou quando legibilidade importa mais que estilo funcional.

**6. O que `in_array()` faz?**
Verifica se um valor existe no array. Retorna `bool`. Usar `in_array($valor, $array, true)` para comparação estrita (tipo + valor).

**7. Qual diferença entre `isset()` e `array_key_exists()`?**
`isset()` retorna `false` se a chave não existe OU se o valor é `null`. `array_key_exists()` retorna `true` mesmo se o valor é `null`. Use `array_key_exists()` quando `null` é um valor válido.

**8. Por que `isset()` pode enganar com valores `null`?**
`isset($array['chave'])` retorna `false` se o valor for `null`, mesmo que a chave exista. Pode gerar falso negativo em verificações de existência.

**9. O que `array_merge()` faz?**
Une dois ou mais arrays. Para arrays indexados, reindexa numericamente. Para associativos, chaves duplicadas são sobrescritas pelo último valor.

**10. Qual diferença entre `+` e `array_merge()`?**
O operador `+` preserva as chaves do primeiro array em caso de conflito (o segundo não sobrescreve). `array_merge()` sobrescreve chaves duplicadas com o valor do último array.

**11. Como ordenar arrays com `sort()`, `asort()` e `usort()`?**
`sort()`: ordena por valor, reindexa. `asort()`: ordena por valor, preserva chaves. `usort()`: ordena por critério customizado via callback.

**12. Quando usar `usort()`?**
Quando a ordenação é mais complexa que comparação simples: ordenar objetos por propriedade, ordenar por múltiplos critérios.

**13. O que `array_values()` resolve?**
Reindexa um array associativo ou com lacunas nos índices, retornando array com índices sequenciais começando em 0.

**14. O que `array_keys()` retorna?**
Array com todas as chaves do array original. Se passado um valor de busca, retorna apenas as chaves que têm aquele valor.

**15. Como transformar arrays em strings?**
`implode($separador, $array)` junta os elementos com o separador. Para estruturas complexas, `json_encode($array)`.

**16. Como transformar strings em arrays?**
`explode($delimitador, $string)` divide por delimitador. `str_split($string, $tamanho)` divide em pedaços. `json_decode($json, true)` para JSON.

**17. Quando `explode()` + `array_map()` fazem sentido juntos?**
Para processar listas em formato string: `array_map('trim', explode(',', $csv))` — divide por vírgula e remove espaços de cada item.

**18. Como evitar loops desnecessários em arrays?**
Usar funções nativas: `array_map`, `array_filter`, `array_search`, `in_array`. Elas são implementadas em C e geralmente mais eficientes que loops PHP equivalentes.

**19. O que `array_unique()` resolve?**
Remove valores duplicados de um array, preservando a primeira ocorrência de cada valor.

**20. Como buscar dados em arrays multidimensionais?**
Loops aninhados, `array_column()` para extrair coluna específica, `array_filter()` com callback que acessa subarrays, ou funções recursivas para estruturas profundas.

**21. Quando um array começa a virar objeto?**
Quando começa a ter comportamentos associados (funções que sempre recebem aquele array), quando a estrutura tem regras de integridade, quando o array representa uma entidade do domínio.

**22. Qual diferença entre coleção e array simples?**
Coleção é um objeto que encapsula lista de itens e oferece métodos de domínio (`$pedidos->totalAbertos()`). Array simples é estrutura de dados sem comportamento.

**23. O que `compact()` e `extract()` fazem?**
`compact('nome', 'idade')` cria array associativo com variáveis locais. `extract($array)` faz o inverso: cria variáveis locais a partir de array associativo.

**24. Por que `extract()` pode ser perigoso?**
Se o array vier de input do usuário, pode sobrescrever variáveis locais existentes, incluindo `$_SESSION`, `$conexao`, etc. Nunca usar com dados não confiáveis.

**25. Como validar estrutura de arrays?**
Verificar existência de chaves com `array_key_exists()` ou `isset()`, tipos dos valores, e usar bibliotecas de validação para estruturas complexas.

**26. O que muda ao passar arrays por referência?**
Por padrão, arrays são passados por valor (cópia). Com `&$array`, a função altera o array original. Modificações dentro da função afetam o chamador.

**27. Como evitar mutação inesperada em arrays?**
Não passar por referência sem necessidade. Criar cópias explícitas quando precisar modificar sem afetar o original. Documentar quando uma função modifica seu argumento.

---

## 5. Datas

**1. Qual a diferença entre `date()` e `DateTime`?**
`date()` é função procedural que formata timestamp Unix. `DateTime` é uma classe orientada a objetos com métodos para manipulação, comparação e aritmética de datas.

**2. Por que `DateTime` costuma ser preferível?**
Mais expressivo, suporta timezone explicitamente, permite operações com `DateInterval`, é mais fácil de testar e mais seguro com `DateTimeImmutable`.

**3. O que `strtotime()` faz?**
Converte string de data para timestamp Unix. Aceita formatos como `"2024-01-15"`, `"next Monday"`, `"+1 month"`. Retorna `false` para strings inválidas.

**4. Quais problemas `strtotime()` pode causar?**
Ambiguidade de formato: `"01/02/2024"` pode ser 1 de fevereiro ou 2 de janeiro dependendo do locale. Strings ambíguas retornam interpretações inesperadas.

**5. Como trabalhar com timezone corretamente?**
Definir timezone padrão com `date_default_timezone_set('America/Sao_Paulo')` ou usar `DateTimeZone` explicitamente em cada instância. Armazenar datas em UTC no banco.

**6. O que acontece ao ignorar timezone?**
Datas podem ser exibidas ou calculadas erradas para usuários em fusos diferentes. Conversões incorretas geram bugs em agendamentos, relatórios e cálculos de prazo.

**7. Como calcular diferença entre datas?**
`$diff = $dataFim->diff($dataInicio)` retorna `DateInterval` com propriedades `days`, `months`, `years`, etc.

**8. O que `DateInterval` resolve?**
Representa uma duração (ex: "2 meses e 3 dias"). Usado em `DateTime::add()`, `DateTime::sub()` e retornado por `DateTime::diff()`.

**9. Como formatar datas no PHP?**
`$data->format('d/m/Y H:i:s')` para exibição. Usar `IntlDateFormatter` para formatos localizados.

**10. Qual diferença entre timestamp e data formatada?**
Timestamp é inteiro (segundos desde 01/01/1970 UTC), independente de timezone. Data formatada é string legível, dependente de locale e timezone.

**11. Como evitar bugs de horário de verão?**
Armazenar em UTC no banco, converter para o timezone do usuário apenas na exibição. Não fazer aritmética de horas assumindo que 1 dia sempre tem 86400 segundos.

**12. O que significa data imutável (`DateTimeImmutable`)?**
`DateTimeImmutable` não se altera: `add()`, `sub()`, `modify()` retornam nova instância em vez de modificar a original. Elimina bugs de mutação acidental.

**13. Quando datas deveriam ser imutáveis?**
Sempre que a data representa um fato (data de criação, data de nascimento) ou é compartilhada entre partes do sistema. `DateTimeImmutable` deve ser preferido por padrão.

---

## 6. Arquivos e IO

**1. O que `file_get_contents()` faz?**
Lê o conteúdo inteiro de um arquivo (ou URL) para uma string. Simples e direto para arquivos pequenos.

**2. Quando usar `fopen()` ao invés de `file_get_contents()`?**
Para arquivos grandes (leitura por partes/stream), para escrita incremental, ou quando precisar controlar o ponteiro de leitura.

**3. Como evitar carregar arquivos gigantes na memória?**
Usar `fopen()` + `fgets()` ou `fread()` para processar linha a linha ou por chunks. Generators PHP também ajudam a processar sem carregar tudo.

**4. O que `json_encode()` faz?**
Converte array ou objeto PHP para string JSON.

**5. Como `json_decode()` funciona?**
Converte string JSON para estrutura PHP. Por padrão retorna `stdClass`; com segundo parâmetro `true`, retorna array associativo.

**6. Qual diferença entre objeto e array no `json_decode()`?**
`json_decode($json)` retorna `stdClass` (acesso por `->propriedade`). `json_decode($json, true)` retorna array associativo (acesso por `['chave']`).

**7. Como tratar erro de JSON inválido?**
Verificar com `json_last_error() === JSON_ERROR_NONE` após decodificar, ou usar `json_validate()` disponível no PHP 8.3+.

**8. O que `unlink()` faz?**
Remove um arquivo do sistema de arquivos.

**9. Como validar upload de arquivos?**
Verificar `$_FILES['arquivo']['error'] === UPLOAD_ERR_OK`, validar MIME type real com `finfo_file()` (não apenas extensão), verificar tamanho, e mover para diretório seguro com `move_uploaded_file()`.

**10. Qual risco de confiar apenas na extensão do arquivo?**
Extensão é facilmente falsificada. Um arquivo `.jpg` pode conter PHP executável. Validar o MIME type real lendo os bytes do arquivo com `finfo`.

**11. Como manipular CSV no PHP?**
Leitura: `fgetcsv($handle)` linha a linha. Escrita: `fputcsv($handle, $array)`. Para arquivos grandes, preferir leitura streaming.

**12. O que `pathinfo()` resolve?**
Extrai componentes de um caminho de arquivo: `dirname`, `basename`, `filename`, `extension`. Mais seguro que manipulação manual de strings.

---

## 7. Tipagem e Validação

**1. Qual diferença entre `==` e `===`?**
`==` compara com coerção de tipo (`0 == "foo"` é `true`). `===` compara tipo e valor sem coerção (`0 === "foo"` é `false`). Sempre preferir `===`.

**2. Por que comparação frouxa pode gerar bugs?**
`0 == false`, `"" == false`, `"0" == false`, `null == false` são todos `true`. Isso pode causar falsos positivos em validações e condicionais.

**3. O que `empty()` realmente verifica?**
Retorna `true` se o valor é `""`, `"0"`, `0`, `0.0`, `null`, `false`, `[]`. É uma combinação de verificações que pode surpreender.

**4. Quando `empty()` pode ser perigoso?**
`empty("0")` retorna `true`, então `"0"` (string zero válida) seria tratado como vazio. Para campos que podem ser `"0"`, usar `=== ""` ou `=== null` explicitamente.

**5. Qual diferença entre `is_null()` e `isset()`?**
`is_null($x)` verifica se o valor é `null` (variável deve existir). `isset($x)` verifica se a variável existe E não é `null`. `!isset($x)` é equivalente a `is_null($x)` quando a variável pode não existir.

**6. Como validar inteiros corretamente?**
`filter_var($valor, FILTER_VALIDATE_INT)` retorna o inteiro ou `false`. Ou `is_int($valor)` para variáveis já tipadas.

**7. O que `filter_var()` resolve?**
Valida e filtra dados com filtros pré-definidos: `FILTER_VALIDATE_EMAIL`, `FILTER_VALIDATE_INT`, `FILTER_VALIDATE_URL`, `FILTER_SANITIZE_STRING`, etc.

**8. Quando usar `filter_input()`?**
Para validar/filtrar diretamente de `$_GET`, `$_POST`, `$_SERVER` etc.: `filter_input(INPUT_POST, 'email', FILTER_VALIDATE_EMAIL)`.

**9. Como validar email corretamente?**
`filter_var($email, FILTER_VALIDATE_EMAIL)` para formato básico. Para garantir que o domínio existe, verificar DNS com `checkdnsrr()`. Validação completa requer envio de e-mail de confirmação.

**10. Por que regex nem sempre é melhor para validação?**
Regex para email, URL ou CPF pode ser incompleta ou excessivamente complexa. Funções nativas (`filter_var`) ou bibliotecas especializadas são mais confiáveis e legíveis.

---

## 8. Funções Modernas do PHP

**1. O que o operador `??` resolve?**
Null coalescing: retorna o primeiro operando se não for `null`, senão retorna o segundo. `$nome = $usuario['nome'] ?? 'Anônimo'` — evita `isset()` verbose.

**2. Qual diferença entre `??` e `?:`?**
`??` verifica se é `null` (ou não definido). `?:` verifica se é "falsy" (null, false, 0, "", "0", []). `$x = $valor ?: 'padrão'` usa padrão se `$valor` for qualquer falsy.

**3. Quando usar `match` ao invés de `switch`?**
Quando cada caso retorna um valor, quando comparação estrita é necessária (match usa `===`), e quando não há fall-through desejado. `match` é expressão, pode ser usado em atribuição direta.

**4. Como arrow functions simplificam callbacks?**
`fn($x) => $x * 2` é mais conciso que `function($x) { return $x * 2; }`. Captura automaticamente variáveis do escopo externo sem `use`.

**5. O que `fn()` resolve?**
Sintaxe curta para closures de expressão única. Captura variáveis externas por valor automaticamente, sem declarar `use`.

**6. Quando collections funcionais melhoram legibilidade?**
Quando uma série de `array_map`, `array_filter`, `array_reduce` encadeados ficaria ilegível. Bibliotecas como `tightenco/collect` oferecem API fluente.

**7. O que `enum` resolve no PHP moderno?**
Define tipo com conjunto fechado de valores válidos. Substitui constantes mágicas e strings arbitrárias, com type safety e métodos próprios.

**8. Como `readonly` ajuda imutabilidade?**
Propriedades `readonly` só podem ser atribuídas uma vez (no construtor). Qualquer tentativa de reatribuição gera `Error`, garantindo que o estado inicial não muda.

**9. O que promoted properties resolvem no construtor?**
Eliminam a repetição de declarar propriedade na classe e atribuir no construtor. `public function __construct(private string $nome)` declara e atribui em um passo.

**10. Como attributes substituem annotations?**
Attributes (`#[Atributo]`) são nativos do PHP 8, checados em tempo de compilação, sem necessidade de parsing de docblock. Substituem `@anotacoes` do Doctrine/PHPDoc.

---

## 9. Orientação a Objetos (POO)

**1. O que diferencia objeto de classe?**
Classe é o molde (definição de atributos e métodos). Objeto é uma instância da classe — uma entidade real em memória com estado próprio.

**2. Como modelar entidades do mundo real sem copiar literalmente a realidade?**
Modelar apenas o que o sistema precisa. Um `Usuário` real tem altura, peso, cor de cabelo — mas o sistema de e-commerce só precisa de nome, email e endereço. Modelar o contexto, não a realidade completa.

**3. Como saber se estou usando POO ou apenas organizando funções dentro de classes?**
Se os objetos só têm getters/setters e toda lógica fica em services estáticos, é código procedural com classes. POO real: objetos têm comportamento (`$pedido->aprovar()`, `$usuario->podeComprar()`).

**4. Por que esconder detalhes internos de uma classe?**
Protege o estado interno de modificações externas indevidas. Permite mudar a implementação sem afetar quem usa a classe.

**5. O que é encapsulamento na prática além da definição teórica?**
Garantir que um objeto só pode ser modificado através de seus próprios métodos. `$conta->sacar(100)` verifica saldo; `$conta->saldo -= 100` direto viola encapsulamento.

**6. Quando faz sentido deixar atributos privados?**
Quando o atributo representa estado interno que não deve ser alterado livremente de fora, especialmente quando há regras de negócio envolvidas na mudança.

**7. O que pode acontecer se qualquer parte do sistema puder alterar estados livremente?**
Invariantes quebradas: saldo negativo, pedido em estado inválido, estoque abaixo de zero. Bugs difíceis de rastrear porque a modificação acontece longe da regra.

**8. Por que getters/setters excessivos podem indicar problema?**
Se cada atributo tem getter e setter público, o encapsulamento é ilusório. O objeto vira um saco de dados (anêmico) e a lógica migra para fora dele.

**9. Essa classe tem uma única responsabilidade?**
Teste: descreva o que a classe faz em uma frase. Se usar "e" ou "também", provavelmente tem mais de uma responsabilidade.

**10. O que faz um método ter responsabilidade demais?**
Faz múltiplas coisas não relacionadas, tem mais de ~20 linhas, tem muitos parâmetros, ou seu nome não consegue descrever tudo que faz.

**11. Como evitar objetos "faz-tudo"?**
SRP: cada classe tem um motivo para mudar. Quando perceber que adicionar feature X exige mexer na classe Y (que já faz Z), é sinal de separação necessária.

**12. Como identificar baixa coesão?**
Métodos da classe não se relacionam entre si, atributos são usados apenas por metade dos métodos, ou a classe poderia ser dividida em duas partes sem comunicação entre elas.

**13. Se eu mudar uma regra de negócio, quantas classes precisam mudar junto?**
O ideal é uma. Se mudar a regra de aprovação de pedido exige mudar Controller, Service, DAO e View ao mesmo tempo, o sistema está muito acoplado.

**14. O que indica que uma classe está muito acoplada?**
Muitas dependências (muitos `use`/`import`), instancia outras classes diretamente no construtor, acessa banco diretamente, conhece detalhes de implementação de outras classes.

**15. Qual o custo de um alto acoplamento no crescimento do sistema?**
Cada mudança cria efeito cascata. Adicionar feature exige modificar múltiplos lugares. Testes unitários são impossíveis sem subir todo o sistema. Risco de regressão aumenta.

**16. Como separar regra de negócio de infraestrutura?**
Domínio (regras) não deve importar classes de banco, HTTP ou framework. A infraestrutura implementa interfaces definidas no domínio. Inversão de dependência.

**17. Por que "instanciar tudo dentro da classe" dificulta evolução?**
A classe fica acoplada à implementação concreta. Não é possível passar um mock em testes nem trocar a implementação sem modificar a classe.

**18. Por que criar uma interface ao invés de usar a classe diretamente?**
A interface define o contrato (o quê), não a implementação (como). O consumidor depende do contrato, não de uma implementação específica, permitindo troca sem impacto.

**19. Como interfaces ajudam a diminuir acoplamento?**
`PagamentoGateway` como interface permite ter `PagamentoStripe` e `PagamentoPagarMe` sem o service de pedido saber qual está sendo usado.

**20. Qual a diferença entre "o que um objeto faz" e "como ele faz"?**
Interface define "o que": `EnviaEmail::enviar($destinatario, $mensagem)`. A implementação define "como": SMTP, API SendGrid, fila, etc.

**21. Como saber se uma abstração faz sentido ou está sendo criada "porque sim"?**
A abstração tem pelo menos duas implementações reais ou uma implementação real e uma de teste (mock)? Se não, provavelmente é prematura.

**22. Quando usar classe abstrata ao invés de interface?**
Quando há comportamento comum (código) compartilhado entre subclasses. Interface define apenas contrato; classe abstrata pode implementar partes comuns e deixar outras como obrigatórias nas subclasses.

**23. "É um" ou "tem um"? (herança vs composição)**
Herança: `Cachorro é um Animal`. Composição: `Carro tem um Motor`. Antes de herdar, perguntar se a relação é realmente "é um". Composição é mais flexível na maioria dos casos.

**24. Quando composição é melhor que herança?**
Quando o comportamento pode variar independentemente, quando a hierarquia ficaria muito profunda, quando o objeto precisa de múltiplos "comportamentos" que herança simples não suporta.

**25. Por que herança excessiva pode virar problema?**
Herança profunda cria forte acoplamento entre classes. Mudança em classe base afeta todas as subclasses. Subclasse herda tudo, mesmo o que não precisa.

**26. O que o polimorfismo resolve além de "sobrescrever métodos"?**
Permite tratar objetos de tipos diferentes de forma uniforme. `$notificador->notificar($usuario)` funciona para `EmailNotificador`, `SMSNotificador`, `PushNotificador` sem `if/else`.

**27. Como o polimorfismo ajuda a evitar vários `if/else`?**
Em vez de `if ($tipo === 'email') ... elseif ($tipo === 'sms') ...`, cada classe implementa o mesmo método e o chamador não precisa saber qual é qual.

**28. O que acontece quando subclasses quebram comportamento esperado?**
Violação do LSP (Liskov). Quem usa a classe base espera certo comportamento; se a subclasse quebra isso, código que funciona com a base falha com a subclasse.

**29. Como injeção de dependência reduz acoplamento?**
Em vez de instanciar `new MySQL()` dentro do serviço, receber `DatabaseInterface` no construtor. Quem usa não sabe qual implementação está recebendo.

**30. O que torna um código difícil de testar?**
Dependências instanciadas internamente (new), acesso direto a banco/API/arquivo, variáveis globais, métodos estáticos, efeitos colaterais ocultos.

**31. Por que mocks normalmente trabalham com interfaces?**
Mock precisa substituir a implementação real. Com interface, o mock implementa o mesmo contrato. Com classe concreta, precisaria de herança e sobrescrita, mais complexo e frágil.

**32. Se eu precisar trocar a tecnologia amanhã, quanto código precisaria mudar?**
Com dependências abstraídas: apenas a implementação concreta. Sem abstração: todos os lugares que instanciam ou chamam a tecnologia diretamente.

**33. Quando uma regra deve ficar na entidade e não no service?**
Quando a regra é intrínseca ao objeto e protege sua própria integridade. `$conta->sacar()` verifica saldo internamente — isso não deve ficar em um service externo.

**34. O que diferencia objeto rico de objeto anêmico?**
Objeto rico tem comportamento: `$pedido->aprovar()`, `$conta->sacar(100)`. Objeto anêmico só tem dados: getters e setters, sem lógica. A lógica fica dispersa em services.

**35. Qual a diferença entre entidade e value object?**
Entidade tem identidade (ID): dois `Usuario` com mesmo nome são diferentes se tiverem IDs diferentes. Value Object é definido pelos seus valores: dois `Dinheiro(100, 'BRL')` são iguais independente de instância.

**36. O que caracteriza um bom domínio orientado a objetos?**
Entidades ricas com comportamento, Value Objects imutáveis, regras de negócio dentro dos objetos que as possuem, nomes que refletem a linguagem do negócio (ubiquitous language).

**37. Como POO ajuda manutenção em sistemas grandes?**
Encapsulamento limita o impacto de mudanças. Polimorfismo permite extensão sem modificação. Interfaces permitem trocar implementações. Coesão mantém mudanças localizadas.

**38. Como eventos e contratos ajudam desacoplamento?**
Eventos permitem que objetos se comuniquem sem conhecer uns aos outros. Contratos (interfaces) permitem depender de abstrações. Ambos reduzem dependências diretas.

**39. O que torna uma arquitetura flexível de verdade?**
Dependências apontando para abstrações, não implementações. Domínio sem dependência de infraestrutura. Componentes com responsabilidades bem definidas e coesas.

**40. O design atual facilita mudança ou só funciona "por enquanto"?**
Teste real: tente trocar o banco de dados, o gateway de pagamento ou o provedor de e-mail. Se exigir mudanças em muitos lugares não relacionados, o design não facilita mudança.

---

## 10. SOLID

**1. Qual problema os princípios SOLID tentam resolver?**
Código que se torna difícil de manter, estender e testar à medida que cresce. SOLID guia para designs que absorvem mudanças sem grandes refatorações.

**2. O que significa uma classe ter apenas um motivo para mudar? (SRP)**
Se a classe muda quando a regra de negócio muda E também quando a forma de persistência muda E também quando a apresentação muda, ela tem mais de um motivo. Cada motivo de mudança deveria pertencer a uma classe diferente.

**3. O que significa "aberto para extensão e fechado para modificação"? (OCP)**
Adicionar nova funcionalidade sem alterar código existente. Usando interfaces e polimorfismo: adicionar novo tipo de desconto é criar nova classe, não modificar a existente.

**4. O que acontece quando subclasses quebram comportamento esperado? (LSP)**
Violação do LSP. Código que funciona com a classe base quebra ao receber a subclasse. Herança deve garantir que a subclasse honra o contrato da base.

**5. Por que interfaces muito grandes são ruins? (ISP)**
Implementadores são forçados a implementar métodos que não usam, gerando código vazio ou lançamento de exceção. Dividir em interfaces menores e específicas.

**6. Por que módulos de alto nível não deveriam depender dos de baixo nível? (DIP)**
Porque módulos de alto nível (regras de negócio) mudariam ao trocar tecnologia de baixo nível (banco, framework). Ambos devem depender de abstração (interface).

**7. Por que depender de abstrações facilita testes?**
É possível injetar implementações de teste (mocks/stubs) que simulam o comportamento real sem infraestrutura. O código de negócio é testável isoladamente.

**8. Como cada princípio SOLID se relaciona com manutenção?**
SRP: mudanças são localizadas. OCP: extensões não quebram código existente. LSP: substituições são seguras. ISP: implementações são focadas. DIP: infraestrutura é trocável.

---

## 11. Objetos Imutáveis

**1. O que significa um objeto ser imutável?**
Uma vez criado, seu estado não pode ser alterado. Todos os atributos são definidos na construção e não existem métodos que os modifiquem.

**2. Por que impedir alteração de estado depois da criação?**
Garante consistência: o objeto está sempre no estado em que foi criado. Elimina bugs causados por modificações inesperadas em partes distantes do código.

**3. Quais problemas objetos imutáveis tentam resolver?**
Efeitos colaterais inesperados, bugs de compartilhamento de estado, dificuldade de rastrear onde o estado foi modificado, problemas de concorrência.

**4. O que muda quando um objeto não possui setters?**
A única forma de "alterar" é criar um novo objeto. Isso torna o histórico de estados rastreável e elimina modificações silenciosas.

**5. Como o construtor ajuda a garantir consistência?**
O objeto valida seus próprios dados na criação. Se a validação falha, o objeto não é criado. Não existe objeto em estado inválido.

**6. O objeto nasce válido ou pode existir inválido?**
Deve nascer válido. Um `CPF` inválido não deveria ser instanciado. Um `Dinheiro` com valor negativo não faz sentido. A validação no construtor garante isso.

**7. Faz sentido permitir alteração desse objeto depois de criado?**
Depende da natureza. CPF, Email, Dinheiro: não fazem sentido mutáveis. Pedido tem estados que evoluem (aberto → aprovado → enviado), mas a transição deve ser controlada.

**8. O estado desse objeto representa valor ou identidade?**
Valor (Value Object): definido pelos seus dados, imutável, sem ID. Identidade (Entidade): tem ID, pode ter estado que evolui com controle.

**9. Quando retornar um novo objeto é melhor que alterar o atual?**
Sempre que o objeto representa um valor. `$preco->comDesconto(10)` retorna novo `Preco`, não modifica o original. Permite usar o valor original depois da operação.

**10. Como imutabilidade reduz efeitos colaterais?**
Não há como modificar acidentalmente. Passar o objeto para qualquer método é seguro: ele não pode ser alterado de dentro.

**11. Como objetos imutáveis ajudam previsibilidade?**
O estado do objeto em qualquer ponto do código é o mesmo de quando foi criado. Não é necessário rastrear quem o modificou.

**12. Como imutabilidade facilita testes?**
Não é necessário verificar se o objeto foi modificado como efeito colateral. O resultado é sempre um novo objeto verificável.

**13. Como imutabilidade ajuda concorrência?**
Objetos imutáveis podem ser compartilhados entre threads/processos sem sincronização. Não há condição de corrida quando o estado não muda.

**14. Por que objetos compartilhados mutáveis podem gerar bugs?**
Se duas partes do sistema têm referência ao mesmo objeto mutável, uma parte pode alterá-lo sem a outra saber, gerando estado inconsistente.

**15. Como identificar um objeto que deveria ser imutável?**
Representa um valor do mundo real (dinheiro, CPF, data, coordenada), não tem identidade própria (não precisa de ID), e sua "alteração" na verdade cria um valor diferente.

**16. Value Objects deveriam ser imutáveis?**
Sim, sempre. Value Objects são definidos pelos seus valores. Dois `Email("joao@exemplo.com")` são equivalentes. Imutabilidade é parte da definição de Value Object.

**17. Faz sentido um CPF mudar depois de criado?**
Não. CPF representa um documento imutável. Se a pessoa tem um novo CPF, é um novo objeto `CPF`, não o mesmo modificado.

**18. Faz sentido um objeto Dinheiro ser mutável?**
Não. `Dinheiro(100, 'BRL')` é um valor. Operações como adição retornam novo `Dinheiro`, não modificam o existente, assim como `1 + 2` retorna `3` sem modificar o `1`.

**19. Qual diferença entre entidade mutável e Value Object imutável?**
Entidade (`Pedido`, `Usuario`) tem ID e estado que evolui de forma controlada. Value Object (`Email`, `CPF`, `Dinheiro`) é definido pelos valores, não tem ID, é imutável.

**20. O que acontece quando muitas partes do sistema alteram o mesmo objeto?**
Rastrear a origem de um estado inválido torna-se muito difícil. Qualquer parte pode ter causado a corrupção.

**21. Como objetos imutáveis ajudam encapsulamento?**
O estado é protegido por definição. Não há forma de burlar o encapsulamento com setters porque eles não existem.

**22. O que muda ao usar `readonly` no PHP?**
Propriedades `readonly` só podem ser atribuídas uma vez, no construtor. Tentativas de reatribuição geram erro. PHP 8.2+ suporta classes `readonly` inteiras.

**23. `readonly` sozinho torna um objeto totalmente imutável?**
Para propriedades de tipos primitivos, sim. Para propriedades que são objetos mutáveis, `readonly` impede a reatribuição da referência, mas o objeto apontado ainda pode ser modificado.

**24. Como criar métodos que "alteram" sem modificar estado?**
Retornar nova instância com o valor modificado: `public function comNome(string $nome): self { return new self($nome, $this->email); }`.

**25. Qual diferença entre `$usuario->setNome("Maria")` e `$usuario->comNome("Maria")`?**
`setNome` modifica o objeto existente (mutável). `comNome` retorna novo objeto com o nome alterado (imutável). O original permanece intacto.

**26. Por que retornar nova instância pode ser mais seguro?**
O chamador pode usar o original e o novo ao mesmo tempo. Não há risco de outra parte do código ser afetada pela mudança.

**27. Como evitar estados inválidos em objetos?**
Validar todas as regras no construtor e lançar exception se inválido. Não fornecer métodos que levem a estado inválido. Usar tipos específicos (Value Objects) em vez de primitivos.

**28. O construtor deveria validar regras?**
Sim, para garantir que o objeto nasce válido. Um `Email("")` não deveria ser criado. A validação no construtor é a última linha de defesa.

**29. O que acontece quando permitimos `$produto->setPreco(-10)`?**
O produto existe em estado inválido. Regras de negócio em outros lugares terão que verificar se o preço é positivo repetidamente, espalhando a validação.

**30. Objetos vazios fazem sentido nesse domínio?**
Depende. Um `Carrinho` vazio faz sentido (ainda não tem itens). Um `Email` vazio não faz sentido. A resposta guia quais validações o construtor deve ter.

**31. Qual diferença entre objeto vazio e objeto inválido?**
Objeto vazio: estado válido porém sem conteúdo (`Carrinho` sem itens). Objeto inválido: estado que viola regras de negócio (`Produto` sem nome, `Conta` com saldo impossível).

**32. Como Null Object se relaciona com imutabilidade?**
Null Object implementa a mesma interface que o objeto real, mas com comportamento neutro (não faz nada ou retorna valores padrão). É imutável por natureza, elimina verificações de `null`.

**33. Qual problema `null` excessivo pode causar?**
Verificações de `null` espalhadas pelo código. `NullPointerException` em tempo de execução. Código defensivo repetitivo. Null Object ou Optional são alternativas mais expressivas.

**34. Como objetos imutáveis ajudam modelagem de domínio?**
Forçam pensar em valores vs identidades. CPF, Dinheiro, Coordenada são valores imutáveis claros. Isso reflete a realidade do domínio mais fielmente.

**35. O que caracteriza um objeto previsível?**
Dado o mesmo estado inicial, sempre se comporta da mesma forma. Sem efeitos colaterais ocultos. Imutabilidade contribui diretamente para previsibilidade.

**36. Como imutabilidade reduz acoplamento?**
Objetos imutáveis podem ser passados sem preocupação de que serão modificados. Não é necessário copiar defensivamente antes de passar para outros métodos.

**37. Qual impacto da imutabilidade na memória?**
Cada "modificação" cria nova instância, aumentando uso de memória. Para objetos frequentemente "modificados", o custo pode ser significativo. PHP tem garbage collector para lidar com isso.

**38. Quando imutabilidade pode complicar demais?**
Objetos com muitos campos que mudam frequentemente tornam o código verboso (recriando instâncias). Entidades com estado que evolui naturalmente (Pedido, Workflow) são melhores como mutáveis controladas.

**39. Como equilibrar simplicidade e imutabilidade?**
Value Objects: sempre imutáveis. Entidades: mutáveis com transições de estado controladas por métodos de domínio. Não forçar imutabilidade onde não faz sentido semântico.

**40. Toda classe deveria ser imutável?**
Não. Entidades representam objetos com identidade e estado que evolui. A imutabilidade é adequada para Value Objects. Para entidades, controlar as transições de estado é o caminho.

**41. Como saber quando imutabilidade vale a pena?**
O objeto representa um valor (não uma entidade com identidade)? Mudanças nele criam um valor diferente, não o mesmo objeto em estado diferente? Se sim, imutabilidade vale.

**42. O domínio precisa de mudança de estado real ou apenas novos estados?**
Se `Pedido` vai de `pendente` para `aprovado`, é mudança de estado real (entidade mutável controlada). Se `Dinheiro(100)` se torna `Dinheiro(110)` após juros, é novo valor (imutável).

**43. Meu objeto protege suas próprias regras ou qualquer parte do sistema pode quebrá-las?**
Teste: tente colocar o objeto em estado inválido de fora. Se conseguir via setter ou acesso direto, as regras não estão protegidas. Encapsulamento + imutabilidade resolvem isso.

---

## 12. Namespaces

**1. Qual problema namespaces tentam resolver?**
Colisão de nomes: duas bibliotecas com classe `User` ou `Config`. Namespaces criam espaços isolados onde cada classe tem nome único completo.

**2. O que acontece quando duas classes possuem o mesmo nome?**
Sem namespaces, fatal error por redeclaração. Com namespaces, `Lib1\User` e `Lib2\User` coexistem sem conflito.

**3. Como namespaces ajudam organização do sistema?**
Agrupam classes por contexto, domínio ou responsabilidade. `App\Domain\Order` e `App\Infrastructure\Persistence` comunicam onde cada coisa pertence.

**4. Namespace é estrutura de pasta?**
Não obrigatoriamente. Namespace é lógico. A relação com pastas é convenção (PSR-4) para que o autoload funcione, não uma regra da linguagem.

**5. O que diferencia namespace lógico de diretório físico?**
Namespace é declarado no arquivo com `namespace App\Domain`. O diretório físico é onde o arquivo está. PSR-4 mapeia um para o outro, mas são conceitos separados.

**6. Como o autoload se relaciona com namespaces?**
PSR-4 define que `App\Domain\Order` deve estar em `src/Domain/Order.php` (dado mapeamento `App => src`). O autoloader usa o namespace para encontrar o arquivo sem `require` manual.

**7. O que o PSR-4 resolve?**
Define padrão para mapeamento de namespace para diretório. Permite que qualquer autoloader (Composer) carregue classes automaticamente sem configuração específica por classe.

**8. Por que namespaces ajudam projetos grandes?**
Organização clara, sem colisões, autoload automático, facilidade de encontrar onde uma classe está apenas pelo seu namespace.

**9. Quando faz sentido criar subnamespaces?**
Quando um contexto tem subdivisões claras: `App\Domain\Order`, `App\Domain\Payment`, `App\Domain\Customer`. Evitar criar subnamespaces por tecnologia (`App\Domain\Traits`, `App\Domain\Interfaces`).

**10. Como evitar namespaces gigantes?**
Se `App\Services` tem 50 classes sem subdivisão, revisar agrupamento por contexto de domínio. Cada namespace deveria ter coesão interna.

**11. O namespace deveria refletir domínio ou tecnologia?**
Domínio. `App\Order`, `App\Payment` são melhores que `App\Models`, `App\Controllers`. A tecnologia é detalhe de implementação.

**12. Qual diferença entre `App\Services` e `App\Domain`?**
`App\Services` é organização técnica (onde ficam os services). `App\Domain` é organização por responsabilidade conceitual. O segundo comunica melhor a intenção.

**13. Como saber se um namespace está mal organizado?**
Classes sem relação clara no mesmo namespace. Namespace com nome genérico (`Utils`, `Helpers`, `Common`) que aceita qualquer coisa. Necessidade de procurar onde uma classe está em vez de inferir pelo nome.

**14. O que indica excesso de profundidade em namespaces?**
`App\Domain\Order\Services\Validators\Rules\Specific\OrderAmountRule` — profundidade desnecessária. 3–4 níveis geralmente é o máximo razoável.

**15. Quando um namespace começa a virar "pasta aleatória"?**
Quando qualquer classe que não tem lugar óbvio vai para ele. `App\Utils` com classes de datas, strings, arrays, PDF, email — sem coesão.

**16. Como namespaces ajudam desacoplamento?**
Separar `App\Domain` de `App\Infrastructure` torna explícito o que pertence ao núcleo do negócio e o que é detalhe técnico. Verificar imports revela se domínio está acoplado à infraestrutura.

**17. O namespace deveria conhecer infraestrutura?**
`App\Domain` não deveria importar `App\Infrastructure`. Se uma classe de domínio tem `use App\Infrastructure\MySQL\UserRepository`, há acoplamento indevido.

**18. Como organizar namespaces em arquitetura limpa?**
`App\Domain` (entidades, value objects, interfaces de repositório). `App\Application` (use cases, services de aplicação). `App\Infrastructure` (implementações concretas: DB, HTTP, Email).

**19. O que acontece quando tudo fica dentro de `App\Utils`?**
Dumping ground: sem coesão, difícil de encontrar, classes acumulam sem critério. O namespace perde significado.

**20. Qual o impacto de classes utilitárias genéricas no projeto?**
Tendem a acumular responsabilidades não relacionadas. Criam acoplamento implícito (tudo usa `Utils`). Muitas vezes escondem que a lógica deveria estar em um objeto de domínio.

**21. Como namespaces ajudam leitura de contexto?**
`App\Domain\Order\OrderApprovalService` — sem abrir o arquivo, já é possível inferir que é um serviço de aprovação de pedido no domínio. O namespace é documentação.

**22. O que `use` realmente faz?**
Cria alias no arquivo atual: `use App\Domain\User` permite escrever `User` em vez do nome completo. Não importa código (o autoload faz isso), apenas cria atalho de nome.

**23. `use` importa código ou apenas cria alias?**
Apenas cria alias. O código é carregado pelo autoload quando a classe é instanciada. `use` é resolvido em tempo de compilação (parsing), não em execução.

**24. Qual diferença entre `use App\Services\UserService` e `new \App\Services\UserService()`?**
`use` + `UserService` cria alias para usar o nome curto. `new \App\Services\UserService()` usa o nome completo com `\` indicando namespace absoluto. Ambos referenciam a mesma classe.

**25. Quando usar alias com `as`?**
Quando dois namespaces têm classe com mesmo nome: `use App\Domain\User as DomainUser; use App\DTO\User as UserDTO;`. Ou para nomes muito longos.

**26. Como evitar conflitos de nomes?**
Namespaces resolvem o problema. Se mesmo com namespaces há conflito no mesmo arquivo, usar alias com `as`.

**27. O que significa referência totalmente qualificada?**
Nome completo da classe incluindo namespace: `\App\Domain\Order\Order`. A barra `\` inicial indica que começa do namespace global raiz.

**28. Quando usar `\` no início do namespace?**
Para referenciar o namespace global dentro de um arquivo com namespace definido. Ex: `\Exception`, `\DateTime` — garantem que é a classe global, não uma do namespace atual.

**29. Como funções globais se comportam dentro de namespaces?**
PHP procura a função no namespace atual primeiro. Se não encontrar, vai para o namespace global. Para garantir uso da função global, usar `\strlen()`, `\array_map()`.

**30. O que acontece quando uma função não é encontrada no namespace atual?**
PHP faz fallback para o namespace global automaticamente (apenas para funções e constantes, não para classes).

**31. Qual diferença entre classe global e classe namespaced?**
Classe global está no namespace raiz (`\Exception`). Classe namespaced tem prefixo (`App\Domain\Order`). Dentro de um namespace, classes globais precisam de `\` ou `use`.

**32. Como namespaces ajudam modularização?**
Cada módulo tem seu namespace. Dependências entre módulos ficam explícitas nos `use`. É possível mover um módulo para pacote separado apenas ajustando o namespace raiz.

**33. Como dividir bounded contexts usando namespaces?**
`App\Order`, `App\Customer`, `App\Payment` — cada contexto tem seu namespace raiz. Classes internas de um contexto não deveriam cruzar para outro diretamente.

**34. O que diferencia namespace técnico de namespace de domínio?**
Técnico: agrupa por tipo de componente (`Controllers`, `Models`, `Services`). Domínio: agrupa por contexto de negócio (`Order`, `Customer`, `Payment`). Domínio comunica intenção melhor.

**35. Como organizar namespaces em DDD?**
Por bounded context e camada: `Order\Domain`, `Order\Application`, `Order\Infrastructure`. Ou: `Domain\Order`, `Application\Order`, `Infrastructure\Order`. O importante é separar domínio de infraestrutura.

**36. Faz sentido separar `Domain`, `Application` e `Infrastructure`?**
Sim. Reflete a separação de responsabilidades da arquitetura limpa/hexagonal. Torna explícito o que é regra de negócio, o que é caso de uso e o que é detalhe técnico.

**37. Como namespaces ajudam testabilidade?**
Classes de domínio sem dependência de infraestrutura (verificável pelos imports) são testáveis sem infraestrutura. Namespaces tornam essa separação visual e auditável.

**38. Como namespaces ajudam manutenção?**
Encontrar onde alterar algo é mais rápido. Impacto de mudança é mais previsível. Organização por domínio alinha código com linguagem do negócio.

**39. O que acontece quando classes de domínio dependem diretamente de Framework, ORM ou HTTP?**
O domínio fica acoplado à infraestrutura. Trocar o framework exige reescrever o domínio. Testar sem infraestrutura fica impossível.

**40. Como namespaces podem revelar acoplamento arquitetural?**
Analisar `use` statements: se `App\Domain\Order\OrderService` importa `Doctrine\ORM\EntityManager`, o domínio está acoplado ao ORM — problema arquitetural visível no namespace.

**41. Qual relação entre Composer e namespaces?**
Composer configura o autoloader no `composer.json`: `"autoload": {"psr-4": {"App\\": "src/"}}`. Isso mapeia o namespace `App` para o diretório `src`, permitindo autoload automático.

**42. O que o autoload do Composer faz?**
Registra um autoloader que, quando uma classe é usada, mapeia o namespace para o caminho de arquivo (via PSR-4) e carrega o arquivo automaticamente.

**43. Por que evitar `require_once` manual em projetos modernos?**
É frágil (depende de caminhos relativos), não escala (um `require` por arquivo), não gerencia dependências transitivas. O autoload do Composer resolve tudo isso.

**44. Como namespaces melhoram reutilização?**
Um módulo com namespace bem definido pode ser movido para outro projeto ou extraído como pacote Composer sem conflito de nomes.

**45. O que caracteriza um namespace saudável?**
Nome que comunica o contexto, classes coesas internamente, sem dependência de infraestrutura no domínio, profundidade razoável, cada classe fácil de localizar pelo nome.

**46. Se eu mover uma classe de lugar, quantos arquivos quebram?**
Com PSR-4 e Composer, mover e atualizar o namespace + executar `composer dump-autoload`. Mas todos os `use` que referenciam o namespace antigo precisam ser atualizados — ferramentas como PHPStorm e Rector automatizam isso.

**47. Meu namespace comunica intenção ou apenas localização física?**
`App\Http\Controllers\UserController` comunica localização técnica. `App\User\RegisterUserController` comunica intenção (registro de usuário) com contexto. Preferir intenção quando possível.

---

## 13. Traits

**1. Qual problema traits tentam resolver?**
Reutilização de código em classes que não compartilham hierarquia de herança. PHP não suporta herança múltipla; traits permitem incluir blocos de código em múltiplas classes.

**2. Quando usar trait ao invés de herança?**
Quando o comportamento a ser reutilizado não caracteriza uma relação "é um". `Timestampable`, `SoftDeletable` são comportamentos que diversas entidades podem ter sem herdar uma da outra.

**3. Traits ajudam reutilização ou escondem problema de design?**
Podem fazer ambos. Traits de comportamento claro e limitado (`Timestampable`) são adequados. Traits que acumulam lógica de negócio geralmente escondem que o design precisa ser repensado.

**4. Como evitar excesso de traits?**
Preferir composição (injetar objeto com o comportamento). Traits são difíceis de testar isoladamente e geram acoplamento implícito. Usar com moderação para comportamentos técnicos transversais.

**5. Qual diferença entre trait e composição?**
Trait injeta código na classe como se fosse dela (acoplamento forte, estado compartilhado). Composição delega para objeto separado (baixo acoplamento, testável independentemente).

**6. Quando trait começa a gerar acoplamento?**
Quando acessa propriedades ou métodos da classe que o usa (`$this->id`), quando assume estado da classe hospedeira, ou quando duas traits têm conflito de nomes de método.

---

## 14. Exceções

**1. Qual diferença entre erro e exceção?**
Erro (PHP 7+: `Error`): problema do ambiente ou do código (syntax error, tipo errado, divisão por zero). Exceção (`Exception`): situação excepcional prevista pelo código que pode ser tratada.

**2. Quando lançar exceptions?**
Quando uma situação impede a continuação do fluxo normal e quem chamou precisa ser notificado: dados inválidos, recurso não encontrado, regra de negócio violada.

**3. Quando NÃO usar exceptions?**
Para fluxo normal de controle (não use exception para "retornar" resultado alternativo), para validações de formulário onde o retorno esperado é lista de erros.

**4. Como exceptions ajudam fluxo de erro?**
Interrompem o fluxo imediatamente e propagam para o primeiro `catch` capaz de tratá-las. Eliminam verificações de retorno em cada chamada.

**5. O que torna um try/catch excessivo?**
`try/catch` em cada chamada de método, capturar `Exception` genérica e ignorar, usar exceptions para fluxo normal. Exceptions deveriam ser tratadas no nível que sabe como reagir.

**6. Como criar exceptions de domínio?**
Estender `RuntimeException` ou `DomainException` com nome específico: `EstoqueInsuficienteException`, `PedidoJaAprovadoException`. O nome comunica o problema de negócio.

**7. Exceptions substituem validação?**
Não. Validação de dados de entrada (formulário, API) deve retornar lista de erros. Exception é para situação excepcional no fluxo de execução, não para listar campos inválidos.

---

## 15. Manipulação de Bancos de Dados com PDO

**1. Qual problema PDO resolve?**
Abstração do acesso a banco: mesma API para MySQL, PostgreSQL, SQLite. Fornece prepared statements, controle de transações e tratamento de erros uniforme.

**2. Qual diferença entre PDO e mysqli?**
PDO suporta múltiplos SGBDs (MySQL, PostgreSQL, SQLite, etc.). mysqli é específico para MySQL. PDO tem API orientada a objetos mais consistente e suporta named parameters em prepared statements.

**3. O que são prepared statements?**
Queries parametrizadas: a estrutura SQL é separada dos dados. `SELECT * FROM users WHERE id = ?` — o `?` é substituído de forma segura pelo PDO, sem risco de injeção.

**4. Como prepared statements evitam SQL Injection?**
Os dados são enviados separadamente da query. O banco trata parâmetros como dados, nunca como código SQL. Não importa o que o usuário enviar, não será interpretado como SQL.

**5. Por que `addslashes()` não resolve SQL Injection?**
Depende do encoding. Em codificações multibyte, aspas podem fazer parte de caracteres legítimos. Prepared statements é a única solução robusta.

**6. Como conectar múltiplos bancos com PDO?**
Criar múltiplas instâncias de `PDO` com DSNs diferentes. Cada instância representa uma conexão independente.

**7. O que `fetch()` retorna?**
Uma linha do resultado como array, objeto ou outro formato dependendo do `FETCH_MODE`. Avança o ponteiro para a próxima linha. Retorna `false` quando não há mais linhas.

**8. Qual diferença entre `fetch()` e `fetchAll()`?**
`fetch()` retorna uma linha por vez (streaming). `fetchAll()` retorna todas as linhas de uma vez como array. Para grandes resultados, `fetch()` em loop usa menos memória.

**9. Quando usar transações?**
Quando múltiplas operações devem ser atômicas: ou todas acontecem ou nenhuma. Transferência bancária, criação de pedido com itens, qualquer operação multi-step interdependente.

**10. Como tratar erros de banco corretamente?**
Configurar `PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION` para que erros lancen `PDOException`. Capturar a exception no nível adequado e tomar ação (log, rollback, resposta de erro).

**11. Como evitar SQL espalhado pelo sistema?**
Centralizar queries em DAOs ou Repositories. Controller e domínio não deveriam conter SQL. Facilita manutenção, refatoração e troca de banco.

---

## 16. Transações e ACID

**1. O que é uma transação?**
Unidade atômica de trabalho: conjunto de operações que são executadas como um todo. Ou todas têm sucesso (commit) ou todas são desfeitas (rollback).

**2. Qual problema transações resolvem?**
Consistência em operações multi-step. Sem transação, uma falha no meio deixa o banco em estado inconsistente (dinheiro debitado mas não creditado, por exemplo).

**3. O que significa ACID?**
**A**tomicidade: tudo ou nada. **C**onsistência: o banco vai de um estado válido para outro. **I**solamento: transações concorrentes não se interferem. **D**urabilidade: após commit, dados persistem mesmo com falha.

**4. Quando usar commit e rollback?**
`commit()` ao final de operações bem-sucedidas para persistir. `rollback()` em caso de exceção ou falha para desfazer todas as operações da transação.

**5. O que acontece se uma operação falhar no meio?**
Sem transação: dados ficam inconsistentes. Com transação e `rollback` no catch: todas as operações são desfeitas, banco volta ao estado anterior.

**6. Como garantir consistência dos dados?**
Usar transações para operações interdependentes, constraints no banco (foreign keys, unique, not null), validações no domínio antes de persistir.

**7. Qual relação entre transações e concorrência?**
Transações com isolamento adequado evitam que leituras sujas ou escritas simultâneas gerem inconsistência. O nível de isolamento (`READ COMMITTED`, `SERIALIZABLE`) define o trade-off entre consistência e performance.

**8. Quando transações longas viram problema?**
Travam recursos (locks) por mais tempo, aumentam risco de deadlock, reduzem throughput. Transações devem ser curtas e focadas.

**9. Como evitar deadlocks?**
Acessar recursos sempre na mesma ordem em transações diferentes. Manter transações curtas. Usar timeout de lock. Detectar e retry quando necessário.

**10. O domínio deveria controlar transação?**
Geralmente não. A transação é preocupação de infraestrutura/aplicação. O domínio define o que precisa ser atômico; a camada de aplicação (use case) decide quando abrir e fechar a transação.

---

## 17. HTTP e Manipulação de Conteúdos via HTTP

**1. O que é HTTP?**
Hypertext Transfer Protocol: protocolo de comunicação cliente-servidor sobre TCP/IP. Define formato de requisições e respostas para transferência de dados na web.

**2. O que significa requisição e resposta?**
Requisição: mensagem do cliente ao servidor (método, URL, headers, body). Resposta: mensagem do servidor ao cliente (status code, headers, body).

**3. Qual diferença entre GET e POST?**
GET: leitura, parâmetros na URL, sem body, idempotente, cacheável. POST: envio de dados, parâmetros no body, não idempotente, não cacheável por padrão.

**4. Quando usar PUT, PATCH e DELETE?**
PUT: substituição completa de recurso. PATCH: atualização parcial. DELETE: remoção. Todos são semânticos — comunicam claramente a intenção.

**5. O que são headers?**
Metadados da requisição/resposta: `Content-Type`, `Authorization`, `Accept`, `Cache-Control`. Transmitidos antes do body, descrevem como interpretar a mensagem.

**6. O que é body da requisição?**
Dados enviados junto com a requisição (POST, PUT, PATCH). Pode ser JSON, form-data, XML, binário. O `Content-Type` indica o formato.

**7. Como funciona status code?**
Código numérico na resposta que indica o resultado. 1xx: informativo. 2xx: sucesso. 3xx: redirecionamento. 4xx: erro do cliente. 5xx: erro do servidor.

**8. Qual diferença entre 200, 201, 400, 401, 403, 404 e 500?**
200: OK. 201: criado com sucesso. 400: requisição inválida (erro do cliente). 401: não autenticado. 403: autenticado mas sem permissão. 404: não encontrado. 500: erro interno do servidor.

**9. O que é Content-Type?**
Header que informa o formato do body: `application/json`, `text/html`, `multipart/form-data`. Necessário para que o receptor saiba como interpretar os dados.

**10. Como enviar JSON em PHP?**
`header('Content-Type: application/json'); echo json_encode($dados);`. Em frameworks, usar o objeto de resposta adequado.

**11. Como consumir APIs HTTP?**
Com `cURL` (nativo) ou bibliotecas como Guzzle. Enviar requisição com método, URL, headers e body. Processar a resposta (status code, body).

**12. O que é stateless?**
HTTP não mantém estado entre requisições. Cada requisição é independente. O servidor não "lembra" da requisição anterior. Estado do cliente (autenticação, preferências) deve ser enviado a cada requisição.

**13. Como sessões funcionam no PHP?**
`session_start()` cria/retoma sessão. Dados ficam em `$_SESSION` (array). Um cookie com o ID da sessão é enviado ao cliente. O servidor armazena os dados (arquivo, banco, Redis).

**14. Qual diferença entre cookie e sessão?**
Cookie: dados armazenados no cliente (browser), enviados em cada requisição, visíveis ao cliente. Sessão: dados armazenados no servidor, o cliente só recebe o ID (sessionid), mais seguro para dados sensíveis.

---

## 18. APIs RESTful

**1. O que torna uma API RESTful?**
Recursos identificados por URLs, manipulados por verbos HTTP, stateless, respostas com status codes adequados, representação dos recursos (JSON/XML).

**2. O que é recurso em REST?**
Entidade do domínio exposta via URL: `/users`, `/orders`, `/products`. O recurso é o substantivo; o verbo HTTP define a ação.

**3. Como modelar rotas corretamente?**
Substantivos no plural para coleções: `GET /users`. ID para recurso específico: `GET /users/42`. Relacionamento: `GET /users/42/orders`. Sem verbos na URL.

**4. Qual diferença entre `/users` e `/getUsers`?**
`/users` é RESTful: o verbo GET + substantivo plural definem a ação. `/getUsers` é RPC: o verbo está na URL, ignorando a semântica HTTP.

**5. Quando usar verbo HTTP corretamente?**
GET: leitura. POST: criação. PUT: substituição completa. PATCH: atualização parcial. DELETE: remoção. O verbo comunica que será feito.

**6. O que torna uma API previsível?**
Padrões consistentes: mesma estrutura de URL para todos os recursos, status codes corretos, erros com formato padrão, versionamento claro.

**7. Como status codes ajudam clientes?**
Clientes sabem o que aconteceu sem parsear o body. 404 → recurso não existe. 422 → dados inválidos. 201 → criado. Permite tratamento genérico por categoria (4xx = erro do cliente).

**8. O que significa idempotência?**
Executar a mesma operação múltiplas vezes produz o mesmo resultado. GET, PUT, DELETE são idempotentes. POST não é (cada chamada cria novo recurso).

**9. O que é versionamento de API?**
Estratégia para evoluir a API sem quebrar clientes existentes. Via URL (`/v1/users`, `/v2/users`), header (`Accept: application/vnd.api+json; version=2`) ou subdomínio (`v2.api.exemplo.com`).

**10. Como evitar acoplamento entre front e API?**
API bem definida com contratos estáveis, versionamento para mudanças breaking, documentação clara. Front depende do contrato (documentação), não da implementação.

**11. O que diferencia REST de RPC?**
REST é orientado a recursos com verbos HTTP semânticos. RPC é orientado a ações: `/createUser`, `/deleteOrder`. REST favorece previsibilidade; RPC pode ser mais intuitivo para operações complexas.

**12. Como documentar APIs?**
OpenAPI/Swagger: especificação padrão que gera documentação interativa. Postman Collections, Blueprint. A documentação deve descrever endpoints, parâmetros, respostas e exemplos.

---

## 19. MVC

**1. Qual problema o MVC tenta resolver?**
Separação de responsabilidades: lógica de apresentação (View), lógica de negócio (Model) e coordenação do fluxo (Controller) em camadas distintas.

**2. Por que separar Model, View e Controller?**
Mudanças na apresentação não afetam regras de negócio. Regras de negócio podem ser testadas sem interface. Controller pode ser substituído (web, CLI, API) sem mudar o Model.

**3. O controller deveria conter regra de negócio?**
Não. Controller orquestra: recebe request, chama serviços/models, devolve resposta. Regra de negócio no Controller impede reuso e dificulta testes.

**4. O que acontece quando o controller começa a fazer "tudo"?**
Fat controller: difícil de testar, impossível de reusar lógica, qualquer mudança de negócio exige mexer no controller. A lógica fica presa ao contexto HTTP.

**5. Qual a responsabilidade real da View?**
Apresentar dados ao usuário. Não deve conter lógica de negócio, acessar banco ou tomar decisões. Recebe dados prontos e os renderiza.

**6. A View deveria conhecer banco de dados?**
Não. É uma das violações mais comuns: queries diretas na view. Acopla apresentação à persistência, impossibilita cache e reuso.

**7. O Model representa dados ou comportamento?**
Comportamento rico. Um Model anêmico (só dados) leva à lógica espalhada em controllers/services. O Model deveria encapsular as regras do domínio.

**8. Como evitar controllers gigantes?**
Mover regras para Services ou entidades de domínio. Controller com 10–20 linhas por action é sinal saudável. Se precisar de mais, a lógica pertence a outra camada.

**9. O que diferencia MVC de arquitetura em camadas?**
MVC é pattern de apresentação. Arquitetura em camadas adiciona Domain, Application, Infrastructure. MVC sozinho não define onde ficam repositórios, validações, integrações externas.

**10. Onde a validação deveria acontecer?**
Validação de formato (input HTTP): no Controller ou Request object. Validação de regra de negócio: no Model/entidade ou Service de domínio. Validação de persistência: no banco (constraints).

**11. Qual a diferença entre fluxo HTTP e regra de negócio?**
Fluxo HTTP: receber request, autenticar, rotear, serializar response. Regra de negócio: "pedido só pode ser aprovado se o pagamento foi confirmado". O Controller lida com o primeiro; o domínio com o segundo.

**12. O controller deveria saber qual banco está sendo usado?**
Não. O Controller recebe dependências (repositórios, services) via injeção. Não instancia `new MySQLUserRepository()` diretamente.

**13. O que acontece quando misturamos HTML, SQL e regra de negócio no mesmo lugar?**
Código impossível de testar, impossível de reusar, difícil de entender. Qualquer mudança tem risco de quebrar tudo. É o anti-pattern clássico do PHP antigo.

**14. Como MVC melhora manutenção?**
Mudanças ficam localizadas na camada certa. Redesign da interface não toca o Model. Mudança de banco não toca a View. Cada camada é independente.

**15. Como MVC ajuda testabilidade?**
Model pode ser testado sem HTTP. Controller pode receber dependências mockadas. View pode ser verificada separadamente.

**16. O que indica que o MVC virou apenas "organização de pastas"?**
Controller com toda a lógica de negócio. Model só com getters/setters. View com queries SQL. A estrutura existe, mas as responsabilidades estão erradas.

**17. Como saber se minha camada Model está anêmica?**
Se o Model só tem propriedades e getters/setters, e toda lógica de negócio está em services externos, o Model é anêmico. Lógica que pertence ao objeto (`$pedido->aprovar()`) está no lugar errado.

**18. O que deveria acontecer no Controller antes de chamar a regra de negócio?**
Autenticação/autorização, validação de formato do input, deserialização, e depois delegar para o serviço ou entidade de domínio.

**19. MVC resolve arquitetura sozinho?**
Não. Define separação de apresentação, mas não diz como organizar domínio, persistência, integrações. Para sistemas complexos, MVC é o ponto de entrada, não a arquitetura completa.

**20. Como evitar acoplamento entre camadas?**
Controller não instancia repositórios diretamente (injeção de dependência). Model não importa classes HTTP. View não acessa banco. Cada camada conhece apenas a próxima via interface.

---

## 20. DAO (Data Access Object)

**1. Qual problema o DAO tenta resolver?**
Centralizar e isolar o acesso a dados. Evita SQL espalhado pelo sistema e desacopla a lógica de negócio da persistência.

**2. Por que isolar acesso a banco em uma camada?**
Se o banco mudar, apenas o DAO muda. A lógica de negócio não sabe como os dados são persistidos. Facilita troca de tecnologia e testes com banco em memória.

**3. O que acontece quando SQL fica espalhado pelo sistema?**
Manutenção difícil (SQL em controllers, views, models). Repetição de código. Dificulta refatoração de schema. Impossível testar lógica sem banco real.

**4. DAO deveria conter regra de negócio?**
Não. DAO é responsável por operações CRUD. Regra de negócio (`pedido só aprovável se pago`) pertence ao domínio.

**5. Qual a responsabilidade real de um DAO?**
Traduzir entre objetos PHP e tabelas do banco: `insert`, `update`, `delete`, `findById`, `findAll`, queries específicas. Sem lógica de negócio.

**6. DAO deveria conhecer HTTP ou Controller?**
Não. DAO é camada de persistência. Não tem dependência de Request, Response, Session ou qualquer conceito HTTP.

**7. O que diferencia DAO de Service?**
DAO: acessa banco (persistência). Service: orquestra regras de negócio, pode usar múltiplos DAOs, coordena fluxo da aplicação.

**8. Como DAO ajuda manutenção?**
Mudanças de schema, queries otimizadas ou troca de banco ficam confinadas ao DAO. O resto do sistema não percebe.

**9. Como DAO reduz repetição de código?**
Queries comuns (buscar por ID, listar todos) ficam em um lugar. Services e controllers reutilizam sem reescrever SQL.

**10. Qual o problema de acessar banco diretamente no Controller?**
Acoplamento entre HTTP e persistência. Controller não pode ser testado sem banco. Mudança de query exige mexer no controller. Viola separação de responsabilidades.

**11. Como DAO ajuda troca de banco de dados?**
Trocar MySQL por PostgreSQL exige mudar apenas os DAOs. Services e domínio permanecem intactos — desde que o DAO retorne os mesmos tipos.

**12. O DAO deveria retornar entidades ou arrays?**
Preferencialmente entidades (objetos do domínio). Arrays são estruturas sem comportamento; entidades carregam lógica de negócio. Retornar entidades mantém o modelo rico.

**13. O que acontece quando um DAO começa a fazer "tudo"?**
DAO com validação de negócio, envio de e-mail e lógica de apresentação. Viola SRP, torna-se difícil de testar e manter. Sinal de que responsabilidades devem ser extraídas.

**14. Como saber se um DAO está muito acoplado?**
Se o DAO importa classes de Controller, HTTP, ou lógica de domínio além da persistência, está acoplado demais.

**15. Um DAO deveria abrir e fechar conexão sozinho?**
Depende da estratégia. Geralmente a conexão é injetada no DAO (Dependency Injection). O gerenciamento da conexão (pool, transação) fica em camada superior.

**16. Como testar código que depende de DAO?**
Definir interface para o DAO e injetar mock/stub nos testes. Ou usar banco em memória (SQLite) para testes de integração.

**17. Qual o impacto de SQL hardcoded espalhado no sistema?**
Refatoração de schema quebra múltiplos arquivos. Otimização de query exige busca em todo o código. Impossível aplicar mudanças consistentes.

**18. DAO é responsável por regras de negócio ou persistência?**
Apenas persistência. "Não pode ter dois usuários com o mesmo email" é regra de negócio (ou constraint de banco) — não lógica do DAO.

**19. Como DAO ajuda organização do sistema?**
Cria fronteira clara entre lógica de negócio e acesso a dados. Facilita leitura: quem quer saber como dados são persistidos, olha o DAO.

**20. Qual diferença entre CRUD e regra de domínio dentro do DAO?**
CRUD: `inserir`, `atualizar`, `deletar`, `buscar`. Regra de domínio: "só pode deletar se não houver pedidos associados". A regra pertence ao domínio ou service, não ao DAO.

---

## 21. Repository Pattern

**1. Qual problema o Repository tenta resolver?**
Criar uma abstração que representa uma coleção de objetos de domínio. O domínio não sabe como os objetos são persistidos — trabalha como se tivesse uma coleção em memória.

**2. O que diferencia Repository de DAO?**
DAO é mais próximo do banco (opera em tabelas, retorna dados). Repository é mais próximo do domínio (opera em entidades, esconde completamente a persistência). Repository pode usar vários DAOs internamente.

**3. Repository representa tabela ou coleção de objetos?**
Coleção de objetos de domínio. `UserRepository` não é abstração de tabela `users`; é abstração de "todos os usuários do sistema", como se fossem uma coleção em memória.

**4. O Repository deveria conhecer regra de negócio?**
Não. Repository persiste e recupera entidades. Regra de negócio fica nas entidades ou services de domínio.

**5. O domínio deveria saber qual banco está sendo usado?**
Não. O domínio define a interface `UserRepository`. A infraestrutura implementa `MySQLUserRepository`. Inversão de dependência.

**6. Por que o Repository normalmente trabalha com entidades?**
Para manter o modelo rico. Entidades têm comportamento. Repository retorna entidades prontas para o domínio usar, não arrays ou DTOs genéricos.

**7. Repository é mais próximo do domínio ou da infraestrutura?**
A interface do Repository pertence ao domínio. A implementação pertence à infraestrutura. Essa separação é o ponto central do pattern.

**8. Como Repository ajuda DDD?**
Permite que o domínio trabalhe com coleções de entidades sem saber de banco. Linguagem ubíqua: `$usuarios->comEmailVerificado()` faz sentido no domínio.

**9. O que significa "persistência transparente"?**
O domínio não percebe que há persistência. Trabalha com entidades como se fossem objetos em memória. A persistência é detalhe de implementação invisível para o domínio.

**10. O Repository deveria expor SQL?**
Não. SQL é detalhe de implementação da infraestrutura. A interface do Repository usa linguagem de domínio: `findByEmail`, `findActiveUsers`, não `SELECT * FROM users WHERE...`.

**11. Como Repository ajuda desacoplamento?**
Domínio depende da interface (abstração). Infraestrutura implementa. Trocar MySQL por MongoDB exige nova implementação da interface, sem tocar no domínio.

**12. O que muda ao trocar MySQL por MongoDB usando Repository?**
Apenas a implementação concreta do Repository. A interface e o domínio permanecem inalterados. Esse é o benefício central do pattern.

**13. Qual a diferença entre Repository e Active Record?**
Active Record: o objeto de domínio sabe se persistir (`$usuario->save()`). Repository: persistência é separada do objeto de domínio. Active Record acopla domínio à persistência.

**14. O Repository deveria retornar entidades válidas?**
Sim. Entidades retornadas devem estar em estado válido e consistente. O Repository é responsável por reconstruir corretamente o estado da entidade a partir dos dados persistidos.

**15. Como saber se um Repository ficou genérico demais?**
Se ele tem apenas `findById`, `findAll`, `save`, `delete` e nada específico do domínio. Um Repository rico tem métodos que falam a linguagem do negócio.

**16. Faz sentido criar Repository para tudo?**
Não. Repository faz sentido para agregados do domínio. Entidades sem identidade própria (Value Objects) não precisam de Repository.

**17. Repository substitui Service?**
Não. Repository: persistência de entidades. Service: orquestração de regras de negócio, coordena entidades e repositórios para realizar casos de uso.

**18. Repository deveria conter regras complexas?**
Não. Queries complexas podem existir, mas regra de negócio (`aprovar pedido`) fica no domínio. Repository pode ter `findPedidosAprovados`, não `aprovarPedido`.

**19. Como Repository melhora testabilidade?**
Criar `InMemoryUserRepository` que implementa a mesma interface. Testes unitários usam a implementação em memória, sem banco, rápidos e confiáveis.

**20. O que diferencia um Repository rico de um simples CRUD?**
Métodos que falam a linguagem de negócio: `findUsersWithExpiredSubscription`, `findTopSellingProducts`, `findOrdersReadyToShip`. Não apenas `findById` e `findAll`.

---

## 22. Repository em Banco Relacional

**1. Como mapear entidades para tabelas relacionais?**
ORM (Doctrine, Eloquent) mapeia automaticamente via anotações/atributos. Sem ORM: o Repository faz o mapeamento manual, lendo colunas e criando objetos.

**2. O Repository deveria conhecer joins?**
A implementação concreta sim, a interface não. O Repository pode usar joins internamente para reconstruir uma entidade, mas o domínio não sabe disso.

**3. Como evitar SQL complexo vazando para o domínio?**
Todo SQL fica na implementação do Repository (infraestrutura). A interface do domínio usa métodos semânticos. Nunca passar query como parâmetro para o domínio.

**4. O que muda ao trabalhar Repository com ORM?**
O ORM cuida do mapeamento objeto-relacional. O Repository delega para o ORM a persistência e recuperação. Mais conveniente, mas pode gerar acoplamento ao ORM se não for bem abstraído.

**5. Como lidar com relacionamentos 1:N e N:N?**
No Repository: decidir o que carregar junto. Para 1:N, carregar via join ou consulta separada. Para N:N, usar tabela de associação. ORM facilita com mapeamentos.

**6. O Repository deveria carregar tudo automaticamente?**
Não necessariamente. Carregar relacionamentos desnecessários tem custo. Decidir o que carregar baseado no caso de uso (eager vs lazy).

**7. O que é eager loading e lazy loading?**
Eager: carrega relacionamentos junto com a entidade principal (JOIN). Lazy: carrega relacionamentos somente quando acessados. Lazy pode gerar N+1 queries.

**8. Como evitar problema de N+1 queries?**
Usar eager loading para relacionamentos que sempre serão usados. `findUsersWithOrders()` carrega usuários e pedidos em uma query, não uma query por usuário.

**9. Qual o impacto de um Repository muito genérico em banco relacional?**
Queries ineficientes (carrega tudo quando precisa de pouco), N+1 queries, sem índices adequados para os acessos específicos do domínio.

**10. Como manter entidades desacopladas do banco?**
Entidade não tem anotações de ORM direto nelas (ou usa atributos nativos PHP sem importar o ORM no domínio). O mapeamento fica em arquivos de configuração separados ou na implementação do Repository.

**11. Quando usar query builder ao invés de ORM?**
Para queries complexas com múltiplos joins, agregações ou performance crítica onde o ORM gera SQL subótimo.

**12. Como Repository ajuda na troca de ORM?**
A interface do domínio não muda. Criar nova implementação usando o novo ORM. O domínio não percebe a troca.

**13. Como tratar transações dentro do Repository?**
A transação geralmente é controlada pela camada de aplicação (use case). O Repository opera dentro da transação fornecida, sem controlá-la diretamente.

**14. O Repository deveria controlar commit e rollback?**
Não idealmente. A unidade de trabalho (transaction) deve ser controlada pelo use case que sabe quais operações são atômicas. Repository faz as operações; quem chama controla a transação.

**15. Como evitar acoplamento entre entidade e estrutura da tabela?**
Mapeamento em camada separada. A entidade tem atributos de domínio. O Repository traduz entre os dois. Se o schema muda, só o mapeamento no Repository muda.

**16. Faz sentido um Repository por agregado?**
Sim, em DDD. Agregados são unidades de consistência. O Repository carrega e persiste o agregado inteiro. Não há repositório para entidades internas do agregado.

**17. Como modelagem relacional impacta o domínio?**
Pode criar pressão para fazer o domínio refletir o schema (antipadrão). O domínio deve ser guiado pelo negócio, não pela estrutura do banco. O Repository isola essa tensão.

**18. Como evitar que Repository vire apenas um "DAO gourmet"?**
Garantir que os métodos falam linguagem de domínio, que o Repository trabalha com entidades ricas (não arrays ou DTOs genéricos), e que o domínio realmente não conhece a persistência.

**19. O que diferencia persistência de modelagem de domínio?**
Modelagem de domínio: como as entidades e regras de negócio são estruturadas. Persistência: como esses dados são armazenados. São preocupações distintas que não deveriam se misturar.

**20. O domínio deveria saber que existe um banco relacional?**
Não. "Banco relacional", "tabelas", "joins", "foreign keys" são conceitos de infraestrutura. O domínio conhece "coleções de entidades" e "buscas semânticas".

---

## 23. Composer

**1. Qual problema o Composer tenta resolver?**
Gerenciamento de dependências PHP: instalar, atualizar, remover bibliotecas e resolver conflitos de versão automaticamente. Também fornece autoloading automático.

**2. Como projetos PHP funcionavam antes do Composer?**
Download manual de bibliotecas, includes/requires espalhados, sem controle de versão, conflitos resolvidos manualmente, sem padrão de autoload.

**3. O que muda ao usar gerenciamento de dependências?**
Dependências declaradas em arquivo versionado (`composer.json`). Instalação reproduzível. Resolução automática de conflitos. Autoload sem `require` manual.

**4. O que é uma dependência na prática?**
Biblioteca externa que o projeto precisa para funcionar. PHPUnit para testes, Monolog para logs, Guzzle para HTTP. O Composer baixa e gerencia essas bibliotecas.

**5. Por que evitar baixar bibliotecas manualmente?**
Sem rastreamento de versão, sem updates controlados, sem resolução de subdependências, difícil reproduzir o ambiente em outra máquina.

**6. Como o Composer ajuda organização do projeto?**
Dependências em `vendor/` separadas do código do projeto. Autoload configurado. `composer.json` documenta as dependências e suas versões.

**7. O que acontece quando diferentes bibliotecas precisam de versões diferentes?**
Composer tenta resolver o conflito encontrando versão compatível. Se impossível, informa o conflito e não instala. Previne "dependency hell" silencioso.

**8. Como o Composer resolve conflitos de dependência?**
Analisa os requisitos de todas as dependências e encontra versões que satisfazem todos os constraints. Usa algoritmo SAT (satisfatibilidade).

**9. O que o arquivo `composer.json` representa?**
Manifesto do projeto: nome, descrição, dependências requeridas (`require`), dependências de desenvolvimento (`require-dev`), configuração de autoload, scripts.

**10. Qual diferença entre `composer.json` e `composer.lock`?**
`composer.json`: declara constraints de versão (`^2.0` = qualquer 2.x). `composer.lock`: registra as versões exatas instaladas (`2.3.1`). Lock garante que todos usem exatamente as mesmas versões.

**11. Por que o `composer.lock` é importante em equipe?**
Garante que todos os desenvolvedores e o servidor usem as mesmas versões exatas. Sem o lock, `composer install` pode instalar versões diferentes para cada pessoa.

**12. Quando commitar o `composer.lock`?**
Sempre, em projetos de aplicação. Garante reprodutibilidade. Em bibliotecas publicadas, o lock não é commitado (cada projeto que usa a biblioteca define suas próprias versões).

**13. O que acontece se apagar a pasta `vendor/`?**
`composer install` a recria exatamente como estava (usando `composer.lock`). A pasta `vendor/` não deve ser versionada — o `.gitignore` deve ignorá-la.

**14. O que o comando `composer install` faz?**
Lê o `composer.lock` (ou `composer.json` se não houver lock) e instala exatamente as versões especificadas. Não atualiza versões.

**15. Qual diferença entre `composer install` e `composer update`?**
`install`: instala versões do `composer.lock`. `update`: resolve novamente o `composer.json`, instala versões mais recentes dentro dos constraints e atualiza o `composer.lock`.

**16. Quando usar `update` pode ser perigoso?**
`update` pode instalar versões mais novas com breaking changes (se os constraints forem amplos). Em produção, preferir `install`. Rodar `update` em ambiente de desenvolvimento e testar antes de atualizar o lock.

**17. O que significa versionamento semântico?**
`MAJOR.MINOR.PATCH`: MAJOR muda para breaking changes. MINOR para novas features compatíveis. PATCH para correções. Ex: `2.3.1`.

**18. Qual diferença entre `^`, `~` e `*` nas versões?**
`^2.3.1`: aceita `>=2.3.1 <3.0.0` (minor e patch). `~2.3.1`: aceita `>=2.3.1 <2.4.0` (apenas patch). `*`: qualquer versão (perigoso).

**19. Como escolher versões de dependências corretamente?**
Usar `^` para a maioria dos casos (aceita minors com features novas). Usar `~` quando se quer apenas patches. Evitar `*`. Testar após updates.

**20. O que significa uma biblioteca ser compatível?**
Implementa a versão do PHP usada, não conflita com outras dependências em versões, e sua API pública não mudou de forma breaking.

**21. Como evitar "dependency hell"?**
Ser seletivo com dependências, usar versões estáveis, evitar bibliotecas com muitas subdependências, manter dependências atualizadas regularmente em pequenos incrementos.

**22. O que o autoload do Composer resolve?**
Carregamento automático de classes sem `require` manual. Configura o autoloader que, dado o namespace de uma classe, encontra e inclui o arquivo correto.

**23. Como o Composer carrega classes automaticamente?**
Gera arquivo `vendor/autoload.php`. Ao incluí-lo, registra autoloader. Quando uma classe é usada, o autoloader mapeia namespace → caminho de arquivo e inclui automaticamente.

**24. Qual relação entre Composer e PSR-4?**
PSR-4 define o padrão de mapeamento namespace → diretório. Composer implementa esse padrão no autoloader, seguindo a configuração do `composer.json`.

**25. O que acontece quando namespaces não seguem PSR-4?**
O autoloader não encontra os arquivos. Classes não são carregadas. É necessário adicionar `classmap` ou rodar `composer dump-autoload` para mapear manualmente.

**26. Como configurar autoload no `composer.json`?**
```json
"autoload": {
  "psr-4": {
    "App\\": "src/"
  }
}
```
Mapeia namespace `App` para diretório `src/`.

**27. Qual diferença entre `autoload` e `autoload-dev`?**
`autoload`: classes do projeto (produção e desenvolvimento). `autoload-dev`: classes apenas para desenvolvimento (testes, fixtures). Em produção, `--no-dev` ignora `autoload-dev`.

**28. O que o comando `composer dump-autoload` faz?**
Regenera os arquivos de autoload em `vendor/autoload.php`. Necessário após adicionar novas classes, mover arquivos ou alterar configuração de autoload.

**29. Quando usar otimização de autoload?**
`composer dump-autoload --optimize` em produção. Gera classmap completo evitando resolução de PSR-4 em runtime. Melhora performance em projetos com muitas classes.

**30. Como o Composer ajuda modularização?**
Cada módulo pode ser um pacote Composer independente. O projeto principal declara dependências nos módulos. Facilita separação de responsabilidades e reutilização.

**31. O que significa publicar um pacote?**
Registrar no Packagist (repositório central do Composer) para que outros projetos possam instalar via `composer require vendor/pacote`.

**32. Como criar uma biblioteca PHP reutilizável?**
Criar `composer.json` com nome, descrição, autoload e dependências. Publicar no Packagist (via GitHub). Versionar com tags seguindo semver.

**33. O que é o Packagist?**
Repositório central de pacotes PHP para o Composer. `packagist.org` lista e indexa pacotes publicados. `composer require` busca pacotes no Packagist por padrão.

**34. Como instalar dependências de desenvolvimento?**
`composer require --dev vendor/pacote`. Fica em `require-dev` no `composer.json`. Instalado por `composer install`, ignorado com `composer install --no-dev` (produção).

**35. Qual diferença entre `require` e `require-dev`?**
`require`: dependências necessárias em produção. `require-dev`: dependências apenas para desenvolvimento (PHPUnit, PHPStan, Faker, etc.). Em produção, usar `--no-dev` para não instalar devDependencies.

**36. PHPUnit deveria ficar em `require-dev`?**
Sim, sempre. PHPUnit é ferramenta de teste, não necessária em produção. Mantê-la em `require-dev` reduz tamanho do deployment e superfície de ataque.

**37. Como Composer ajuda testes?**
Autoload de classes de teste. PHPUnit instalado via `require-dev`. `autoload-dev` mapeia namespace de testes. Scripts no `composer.json` para `composer test`.

**38. Como Composer melhora manutenção?**
Dependências documentadas e versionadas. Updates controlados. Auditoria de segurança com `composer audit`. Fácil adicionar ou remover dependências.

**39. O que acontece quando um projeto depende fortemente de muitas bibliotecas?**
Atualizações de segurança se tornam frequentes. Conflitos de versão aumentam. Vulnerabilidades em dependências transitivas afetam o projeto. Avaliar se cada dependência vale o custo.

**40. Como avaliar se vale a pena instalar uma dependência?**
A biblioteca resolve problema real e complexo? É mantida ativamente? Tem boa cobertura de testes? Quantas subdependências traz? Poderíamos implementar a funcionalidade necessária simplesmente?

**41. Quando uma dependência pequena demais pode ser exagero?**
Se a dependência faz algo que 5 linhas de código resolveriam, não vale o overhead. O caso famoso do `is-odd` npm ensinou que dependências triviais criam fragilidade.

**42. Como evitar acoplamento excessivo a frameworks?**
Domínio sem import de classes do framework. Usar interfaces para pontos de integração. O framework é detalhe de entrega, não o núcleo da aplicação.

**43. O domínio da aplicação deveria depender diretamente do framework?**
Não. O domínio deveria ser independente. Usar o framework nas bordas (controllers, middleware, configuração). Isso permite testar o domínio sem subir o framework.

**44. Como Composer ajuda arquitetura limpa?**
Permite organizar o projeto em pacotes por camada. `require` explicita quais camadas dependem de quais. Facilita verificar se o domínio está importando infraestrutura indevidamente.

**45. O que caracteriza um projeto PHP moderno?**
Composer para dependências, PSR-4 para autoload, tipagem estrita, PHPUnit para testes, análise estática (PHPStan/Psalm), CI/CD, Docker para ambiente reproduzível.

**46. Faz sentido usar Composer em projetos pequenos?**
Sim. O custo de configurar é baixo e os benefícios (autoload, dependências gerenciadas) valem mesmo em projetos pequenos.

**47. Como Composer facilita integração contínua?**
`composer install --no-dev` no pipeline. `composer.lock` garante versões reproduzíveis. `composer audit` detecta vulnerabilidades automaticamente.

**48. O que significa um projeto ser reproduzível?**
Qualquer desenvolvedor ou servidor pode recriar exatamente o mesmo ambiente com os mesmos passos. `composer.lock` + `docker-compose` + variáveis de ambiente documentadas garantem isso.

**49. Como garantir que todos da equipe usem as mesmas versões?**
Commitar `composer.lock`. Usar `composer install` (não `update`) ao clonar o projeto. CI roda `composer install` com o mesmo lock.

**50. O que acontece quando o ambiente local difere do servidor?**
Bugs que só aparecem em produção. Versões diferentes de PHP ou extensões. Resolver com Docker e `composer.lock` versionado.

**51. Como Composer ajuda deploy?**
`composer install --no-dev --optimize-autoload` em produção: instala apenas dependências necessárias com autoload otimizado. Processo reproduzível e documentado.

**52. Como identificar dependências abandonadas?**
Verificar data do último commit no GitHub, número de issues abertas sem resposta, se há versão compatível com PHP atual. `composer outdated` lista dependências desatualizadas.

**53. O que significa uma biblioteca ser mantida ativamente?**
Commits recentes, issues respondidas, releases regulares, compatível com versões recentes do PHP, documentação atualizada.

**54. Como segurança se relaciona com dependências?**
Vulnerabilidades em dependências afetam o projeto. `composer audit` verifica o advisory database. Manter dependências atualizadas reduz exposição a CVEs conhecidos.

**55. O que acontece quando uma dependência possui vulnerabilidade?**
O projeto fica exposto à vulnerabilidade. `composer audit` avisa. Atualizar para versão corrigida ou buscar alternativa.

**56. Como Composer ajuda atualização segura?**
`composer outdated` lista o que pode ser atualizado. `composer update vendor/pacote` atualiza apenas um pacote. Permite atualização incremental e controlada, com testes a cada passo.

**57. O projeto realmente precisa dessa dependência ou estamos terceirizando lógica simples demais?**
Avaliar: a lógica é suficientemente complexa para justificar dependência externa? O custo de manutenção, atualização e vulnerabilidades é menor que o custo de implementar? Dependências têm custo — só instalar quando o benefício é claro.