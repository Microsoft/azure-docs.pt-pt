---
title: Constantes SQL no Azure Cosmos DB
description: Saiba mais sobre as constantes SQL no Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: ed337c8330eb8b3e4ad97c92cca1d0c5977f9588
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342676"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Constantes de consulta do Azure Cosmos DB SQL  

 Uma constante, também conhecido como um literal ou um valor escalar, é um símbolo que representa um valor de dados específicos. O formato de uma constante depende do valor representa o tipo de dados.  
  
 **Tipos de dados escalares suportados:**  
  
|**Tipo**|**Ordem de valores**|  
|-|-|  
|**Não definido**|Único valor: **indefinido**|  
|**Nulo**|Único valor: **nulo**|  
|**valor booleano**|Valores: **false**, **verdadeiro**.|  
|**Número**|Um precisão dupla número de vírgula flutuante, padrão IEEE 754.|  
|**Cadeia de caracteres**|Uma seqüência de caracteres Unicode de zero ou mais. Cadeias de caracteres devem estar entre aspas simples ou duplas.|  
|**Matriz**|Uma seqüência de elementos de zero ou mais. Cada elemento pode ser um valor de qualquer tipo de dados escalares, exceto **indefinido**.|  
|**Objeto**|Um conjunto fora de ordem de zero ou mais pares de nome/valor. Nome é uma cadeia de caracteres Unicode, o valor pode ser de qualquer tipo de dados escalares, exceto **indefinido**.|  
  
## <a name="bk_syntax"></a>Sintaxe
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
##  <a name="bk_arguments"></a> argumentos
  
* `<undefined_constant>; Undefined`  
  
  Valor de representa não definida do tipo indefinido.  
  
* `<null_constant>; null`  
  
  Representa **nulo** valor do tipo **nulo**.  
  
* `<boolean_constant>`  
  
  Representa a constante de tipo Booleano.  
  
* `false`  
  
  Representa **false** valor do tipo Booleano.  
  
* `true`  
  
  Representa **true** valor do tipo Booleano.  
  
* `<number_constant>`  
  
  Representa uma constante.  
  
* `decimal_literal`  
  
  Literais decimais são números representados através de notação decimal ou notação científica.  
  
* `hexadecimal_literal`  
  
  Hexadecimal literais são números apresentados com o prefixo "0x" seguido de um ou mais dígitos hexadecimais.  
  
* `<string_constant>`  
  
  Representa uma constante do tipo cadeia.  
  
* `string _literal`  
  
  Literais de cadeia de caracteres são cadeias de caracteres Unicode representadas por uma seqüência de zero ou mais carateres Unicode ou seqüências de escape. Literais de cadeia de caracteres são inseridos entre aspas (apóstrofe: ") ou as aspas duplas (aspas de fecho:").  
  
  São permitidas os seguintes sequências de escape:  
  
|**Sequência de escape**|**Descrição**|**Caráter Unicode**|  
|-|-|-|  
|\\'|apóstrofe (')|U+0027|  
|\\"|aspas (")|U+0022|  
|\\\ |solidus inversa (\\)|U+0B95 + 005C|  
|\\/|solidus (/)|U+0B95 + 002F|  
|\b|RETROCESSO|U+0008|  
|\f|formulário de feed|U+000C|  
|\n|avanço de linha|U+000A|  
|\r|retorno de carro|U+0B95 + 000D|  
|\t|tabulação|U+0009|  
|\uXXXX|Um caráter Unicode definido por 4 dígitos hexadecimais.|U+XXXX|  

## <a name="next-steps"></a>Passos Seguintes

- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Dados de documento do modelo](modeling-data.md)
