# 📚 ÍNDICE — Provas Básicas de PHP + MySQL
> 9 provas temáticas com GROUP BY, agregações, MVC + DAO + Service

---

## 📋 Provas Disponíveis

| # | Arquivo | Tema | Entidades principais |
|---|---|---|---|
| 01 | `basico_01_pets_adocao.md` | 🐱 Pets para Adoção | especies, abrigos, animais, fotos, caracteristicas |
| 02 | `basico_02_multas.md` | 🚗 Multas de Trânsito | tipos_infracao, condutores, veiculos, multas |
| 03 | `basico_03_receitas.md` | 🍕 Receitas Culinárias | chefs, receitas, ingredientes, passos, tags |
| 04 | `basico_04_restaurantes.md` | 🍽️ Restaurantes | tipos_culinaria, restaurantes, horarios, avaliacoes |
| 05 | `basico_05_cardapio.md` | 🧾 Cardápio Digital | cardapios, pratos, ingredientes, alergenos |
| 06 | `basico_06_times_futebol.md` | ⚽ Times de Futebol | times, estadios, titulos, elencos, competicoes |
| 07 | `basico_07_jogadores.md` | 🏃 Jogadores | posicoes, jogadores, contratos, estatisticas, premiacoes |
| 08 | `basico_08_filmes.md` | 🎬 Filmes | diretores, filmes, generos, avaliacoes, premios |
| 09 | `basico_09_series.md` | 📺 Séries | plataformas, series, temporadas, episodios, avaliacoes |

---

## 🧩 Estrutura comum a todas as provas

Cada prova contém **7 questões** com peso fixo:

| Questão | Conteúdo | Peso |
|---|---|---|
| Q1 | SQL com CREATE TABLE + 5 queries com GROUP BY/agregações | 25% |
| Q2 | Estrutura MVC de pastas + .htaccess | 5% |
| Q3 | Models com getters, toArray(), fromArray() e métodos formatadores | 10% |
| Q4 | DAO com CRUD + 4 métodos extras (incluindo GROUP BY no PHP) | 25% |
| Q5 | Service com regras de negócio | 15% |
| Q6 | Tabela de rotas + Controller com JSON padronizado | 15% |
| Q7 | 5 exercícios de manipulação de arrays | 5% |

---

## 📊 Agregações SQL cobradas em todas as provas

| Função | Onde aparece |
|---|---|
| `COUNT(*)` | Toda prova — contagem de registros por grupo |
| `AVG()` | Médias: nota, preço, duração, idade, peso |
| `SUM()` | Totais: valor, títulos, gols, bilheteria |
| `MAX()` / `MIN()` | Extremos: maior nota, menor preço |
| `GROUP BY` | Agrupamento por status, categoria, país, gênero |
| `HAVING` | Filtro pós-agrupamento: min avaliações, min títulos |
| `ORDER BY` com agregação | Rankings: ORDER BY COUNT(*) DESC |
| `ROUND()` | Arredondamento de médias |
| `MONTH()` / `YEAR()` | Agrupamento temporal (multas, séries) |
| `LEFT JOIN` + COUNT | Incluir registros sem relacionamento (ex: times sem títulos) |

---

## 🔡 Funções de array cobradas em todas as provas

Toda prova tem **5 exercícios** fixos de array:

1. **Title Case** em strings com `array_map` + `mb_convert_case`
2. **Agrupamento** por chave com `array_reduce`
3. **Ordenação** customizada com `usort`
4. **Transformação** com `array_map` (adicionar chave calculada ou formatar)
5. **snake_case → camelCase** em todas as chaves dos elementos

---

## 💡 Dica — Funções auxiliares reutilizáveis

Crie `helpers/StringHelper.php` em todos os projetos com:

```php
function snakeToCamel(string $s): string {
    return lcfirst(str_replace('_', '', ucwords($s, '_')));
}

function toTitleCase(string $s): string {
    return mb_convert_case($s, MB_CASE_TITLE, 'UTF-8');
}

function arrayKeysToCamel(array $arr): array {
    $result = [];
    foreach ($arr as $k => $v) {
        $result[is_string($k) ? snakeToCamel($k) : $k] = is_array($v) ? arrayKeysToCamel($v) : $v;
    }
    return $result;
}

function formatarMoeda(float $v): string {
    return 'R$ ' . number_format($v, 2, ',', '.');
}
```

