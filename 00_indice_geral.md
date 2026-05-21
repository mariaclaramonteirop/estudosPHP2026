# 🎓 ÍNDICE GERAL — Provas de PHP + MySQL API
> Série de avaliações progressivas para desenvolvimento de APIs REST em PHP puro

---

## 📋 Provas Disponíveis

| # | Arquivo | Tema | Nível | Tempo | Pontuação |
|---|---|---|---|---|---|
| 01 | `prova_01_todo_basico.md` | ✅ Sistema de Tarefas (TODO) | Iniciante | 3h | 100 pts |
| 02 | `prova_02_pets_intermediario.md` | 🐱 Clínica/Adoção de Pets | Intermediário | 4h | 110 pts |
| 03 | `prova_03_carros_avancado.md` | 🚗 Concessionária de Veículos | Avançado | 5h | 115 pts |
| 04 | `prova_04_ecommerce_expert.md` | 🛒 E-commerce Completo | Expert | 6h | 120 pts |
| — | `gabarito_referencia.md` | 📚 Gabarito e Referência | — | — | — |

---

## 🧩 O que cada prova exige

### Conhecimentos cobertos em todas as provas:

| Conceito | Prova 01 | Prova 02 | Prova 03 | Prova 04 |
|---|:---:|:---:|:---:|:---:|
| SQL básico (CREATE, INSERT) | ✅ | ✅ | ✅ | ✅ |
| SQL com JOINs | ✅ | ✅ | ✅ | ✅ |
| SQL com GROUP BY / agregações | — | ✅ | ✅ | ✅ |
| Triggers | — | — | — | ✅ |
| Procedures / Views | — | — | ✅ | ✅ |
| Transações (BEGIN/COMMIT/ROLLBACK) | — | — | ✅ | ✅ |
| PDO com prepared statements | ✅ | ✅ | ✅ | ✅ |
| Singleton pattern | ✅ | ✅ | ✅ | ✅ |
| MVC básico | ✅ | ✅ | ✅ | ✅ |
| DAO pattern | ✅ | ✅ | ✅ | ✅ |
| Service layer | ✅ | ✅ | ✅ | ✅ |
| Rotas em PHP puro | ✅ | ✅ | ✅ | ✅ |
| Regex para rotas com parâmetros | — | ✅ | ✅ | ✅ |
| Paginação | — | ✅ (bônus) | ✅ | ✅ |
| Exceções customizadas | — | ✅ | ✅ | ✅ |
| Observer/Events | — | — | — | ✅ |
| JWT Authentication | — | — | — | ✅ (bônus) |
| Rate Limiting | — | — | — | ✅ (bônus) |
| **Manipulação de arrays** | ✅ | ✅ | ✅ | ✅ |
| snake_case ↔ camelCase | ✅ | ✅ | ✅ | ✅ |
| array_map / array_filter / usort | ✅ | ✅ | ✅ | ✅ |
| array_reduce (groupBy) | — | ✅ | ✅ | ✅ |
| Recursão em arrays aninhados | — | — | ✅ | ✅ |

---

## 🗺️ Trilha de Aprendizado Sugerida

```
Prova 01 (TODO)
    ↓ domina CRUD simples + rotas + DAO
Prova 02 (Pets)
    ↓ domina JOINs + regras de negócio + roteador com regex
Prova 03 (Carros)
    ↓ domina transações + filtros + paginação + arrays recursivos
Prova 04 (E-commerce)
    ↓ domina triggers + observer pattern + JWT + testes
```

---

## 📐 Estrutura padrão adotada em todas as provas

```
projeto/
├── config/
│   ├── Database.php     ← Singleton PDO
│   └── App.php          ← constantes globais
├── models/              ← entidades com getters/setters/toArray/fromArray
├── dao/
│   ├── interfaces/      ← contratos DAO
│   └── *DAO.php         ← implementações com PDO
├── services/            ← regras de negócio
├── controllers/         ← recebem HTTP, chamam services, devolvem JSON
├── middlewares/         ← auth, rate limit, cors
├── helpers/             ← funções utilitárias (strings, arrays, formatação)
├── exceptions/          ← exceções customizadas
├── routes/
│   └── api.php          ← definição de rotas
└── public/
    ├── index.php        ← entry point
    └── .htaccess        ← redireciona para index.php
```

---

## 🔑 Funções de Array mais cobradas

```php
// Todas as provas exigem domínio dessas funções:

array_map()       // transformar elementos
array_filter()    // filtrar por condição
array_reduce()    // reduzir/acumular (ex: groupBy)
usort()           // ordenar com critério customizado
array_column()    // extrair coluna específica
array_merge()     // unir arrays
array_keys()      // extrair chaves
array_values()    // extrair valores
in_array()        // verificar existência
array_unique()    // remover duplicatas
array_flip()      // inverter chaves/valores
array_combine()   // criar array de chave+valor
array_chunk()     // dividir em pedaços (paginação manual)
array_slice()     // fatiar array
array_search()    // buscar valor e retornar chave
array_walk()      // aplicar função em cada elemento (modifica in-place)
```

---

## 💡 Dicas Gerais

1. **Sempre use prepared statements** — nunca concatene valores em SQL
2. **Sempre feche com `exit`** após `json_encode` para evitar output extra
3. **Defina Content-Type** antes de qualquer output: `header('Content-Type: application/json')`
4. **Valide antes de persistir** — coloque as validações no Service, não no DAO
5. **Nomeie propriedades em camelCase** nos Models e converta para snake_case no `toArray()`
6. **Use transações** sempre que uma operação envolve múltiplas tabelas
7. **Capture PDOException** em todo método do DAO
8. **Retorne arrays** dos Services, não objetos Model — facilita serialização
