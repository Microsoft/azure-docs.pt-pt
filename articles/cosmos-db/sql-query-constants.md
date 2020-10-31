---
title: Constantes SQL em Azure Cosmos DB
description: Saiba como as constantes de consulta SQL em Azure Cosmos DB são usadas para representar um valor de dados específico
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: ebc1f66e68d4cf37546e7d33a9b723385330395c
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100869"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Azure Cosmos DB SQL constantes de consulta  
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Uma constante, também conhecida como um valor literal ou escalão, é um símbolo que representa um valor de dados específico. O formato de uma constante depende do tipo de dados do valor que representa.  
  
 **Tipos de dados escalares suportados:**  
  
|**Tipo**|**Valores ordem**|  
|-|-|  
|**Indefinido**|Valor único: **indefinido**|  
|**Null**|Valor único: **nulo**|  
|**Rio Boolean**|Valores: **falso,** **verdadeiro** .|  
|**Number**|Um número de ponto flutuante de dupla precisão, padrão IEEE 754.|  
|**String**|Uma sequência de caracteres Unicode zero ou mais. As cordas devem ser fechadas em ações simples ou duplas.|  
|**Matriz**|Uma sequência de zero ou mais elementos. Cada elemento pode ser um valor de qualquer tipo de dados escalar, exceto **indefinido** .|  
|**Objeto**|Um conjunto não ordenado de pares de nomes/valor zero ou mais. O nome é uma cadeia Unicode, o valor pode ser de qualquer tipo de dados escalar, exceto **Indefined** .|  
  
## <a name="syntax"></a><a name="bk_syntax"></a>Syntax
  
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
  
##  <a name="arguments"></a><a name="bk_arguments"></a> Argumentos
  
* `<undefined_constant>; Undefined`  
  
  Representa um valor indefinido do tipo Indefined.  
  
* `<null_constant>; null`  
  
  Representa um valor **nulo** do tipo **Nulo** .  
  
* `<boolean_constant>`  
  
  Representa constante do tipo Boolean.  
  
* `false`  
  
  Representa um valor **falso** do tipo Boolean.  
  
* `true`  
  
  Representa **o verdadeiro** valor do tipo Boolean.  
  
* `<number_constant>`  
  
  Representa uma constante.  
  
* `decimal_literal`  
  
  Os literais decimais são números representados usando notação decimal, ou notação científica.  
  
* `hexadecimal_literal`  
  
  Os literais hexadecimais são números representados usando o prefixo '0x' seguidos por um ou mais dígitos hexadémicos.  
  
* `<string_constant>`  
  
  Representa uma constante de corda tipo.  
  
* `string _literal`  
  
  As cordas literais são cordas Unicode representadas por uma sequência de caracteres unicode zero ou mais ou sequências de fuga. As cordas literais são incluídas em cotações individuais (apóstrofo: ' ) ou citações duplas (marca de citação: ").  
  
  São permitidas sequências de fuga seguintes:  
  
|**Sequência de fuga**|**Descrição**|**Personagem unicódigo**|  
|-|-|-|  
|\\'|apóstrofo (')|U+0027|  
|\\"|marca de cotação (")|U+0022|  
|\\\ |reverse solidus \\ ()|U+005C|  
|\\/|solido (/)|U+002F|  
|\b|backspace|U+0008|  
|\f|forma de alimentação|U+000C|  
|\n|alimentação de linha|U+000A|  
|\r| símbolo de retorno|U+000D|  
|\t| tabulação|U+0009|  
|\uXXXX|Um caracteres Unicode definido por 4 dígitos hexadémicos.|U+XXXX|  

## <a name="next-steps"></a>Passos seguintes

- [Amostras de Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dados de documento do modelo](modeling-data.md)
