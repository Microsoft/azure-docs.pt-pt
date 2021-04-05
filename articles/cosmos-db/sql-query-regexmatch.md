---
title: RegexMatch em Azure Cosmos DB linguagem de consulta
description: Conheça a função do sistema RegexMatch SQL em Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 7f5b2831f45e902b312636e4133557a16ee7ec95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341642"
---
# <a name="regexmatch-azure-cosmos-db"></a>REGEXMATCH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Fornece capacidades de expressão regulares. Expressões regulares são uma notação concisa e flexível para encontrar padrões de texto. A Azure Cosmos DB utiliza [expressões regulares compatíveis com PERL (PCRE)](http://www.pcre.org/). 

## <a name="syntax"></a>Sintaxe
  
```sql
RegexMatch(<str_expr1>, <str_expr2>, [, <str_expr3>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É a expressão de corda a ser revistada.  
  
*str_expr2*  
   É a expressão regular.

*str_expr3*  
   É a cadeia de modificadores selecionados para usar com a expressão regular. Este valor de corda é opcional. Se quiser executar o RegexMatch sem modificadores, pode adicionar uma corda vazia ou omitir completamente. 

Pode aprender sobre [sintaxe para criar expressões regulares em Perl.](https://perldoc.perl.org/perlre) 

A Azure Cosmos DB suporta os seguintes quatro modificadores:

| Modificador | Description |
| ------ | ----------- |
| `m` | Trate a expressão da corda para ser procurada como múltiplas linhas. Sem esta opção, "^" e "$" combinarão no início ou no fim da corda e não em cada linha individual. |
| `s` | Permita que "." corresponda a qualquer personagem, incluindo um personagem newline. | 
| `i` | Ignore o caso quando o padrão corresponder. |
| `x` | Ignore todos os personagens do espaço branco. |

## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão booleana. Retorna indefinidamente se a expressão de corda a ser pesquisada, a expressão regular ou os modificadores selecionados forem inválidos.
  
## <a name="examples"></a>Exemplos
  
O exemplo simples regexMatch verifica a cadeia "abcd" para correspondência de expressão regular usando alguns modificadores diferentes.
  
```sql
SELECT RegexMatch ("abcd", "ABC", "") AS NoModifiers, 
RegexMatch ("abcd", "ABC", "i") AS CaseInsensitive, 
RegexMatch ("abcd", "ab.", "") AS WildcardCharacter,
RegexMatch ("abcd", "ab c", "x") AS IgnoreWhiteSpace, 
RegexMatch ("abcd", "aB c", "ix") AS CaseInsensitiveAndIgnoreWhiteSpace 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[
    {
        "NoModifiers": false,
        "CaseInsensitive": true,
        "WildcardCharacter": true,
        "IgnoreWhiteSpace": true,
        "CaseInsensitiveAndIgnoreWhiteSpace": true
    }
]
```

Com o RegexMatch, pode utilizar metacharacters para fazer pesquisas de cordas mais complexas que de outra forma não seriam possíveis com as funções do sistema StartsWith, EndsWith, Contains ou StringEquals. Aqui estão alguns exemplos adicionais, que você pode executar usando o conjunto de dados nutricionais disponíveis através do [Azure Cosmos DB Query Playground](https://www.documentdb.com/sql/demo). 

> [!NOTE] 
> Se precisar de utilizar um metacharacter numa expressão regular e não quiser que tenha um significado especial, deve escapar ao metacaracter usando `\` .

**Verifique itens que tenham uma descrição que contenha a palavra "sal" exatamente uma vez:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "salt{1}","")
```

**Verifique os itens que têm uma descrição que contenha um número entre 0 e 99:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "[0-99]","")
```

**Verifique itens que tenham uma descrição que contenha quatro letras começando com "S" ou "s":**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, " s... ","i")
```

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um índice de [intervalo](index-policy.md#includeexclude-strategy) se a expressão regular puder ser dividida em funções do sistema StartsWith, EndsWith, Contains ou StringEquals.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
