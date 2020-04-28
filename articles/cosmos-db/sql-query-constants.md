---
title: SQL constantes em Azure Cosmos DB
description: Saiba como as constantes de consulta SQL em Azure Cosmos DB são usadas para representar um valor específico de dados
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: cca62c358037dbe99fd16746ee081b1540161df2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74873425"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Azure Cosmos DB SQL constantes de consulta  

 Uma constante, também conhecida como literal ou um valor escalar, é um símbolo que representa um valor específico de dados. O formato de uma constante depende do tipo de dados do valor que representa.  
  
 **Tipos de dados scalar suportados:**  
  
|**Tipo**|**Ordem de valores**|  
|-|-|  
|**Indefinido**|Valor único: **indefinido**|  
|**Nulo**|Valor único: **nulo**|  
|**Booleano**|**Valores: falso,** **verdadeiro.**|  
|**Número**|Um número de ponto flutuante de dupla precisão, padrão IEEE 754.|  
|**String**|Uma sequência de zero ou mais caracteres Unicode. As cordas devem ser fechadas em cotações simples ou duplas.|  
|**Matriz**|Uma sequência de zero ou mais elementos. Cada elemento pode ser um valor de qualquer tipo de dados escalar, exceto **Indefinido**.|  
|**Objeto**|Um conjunto de pares de zero ou mais nomes/valor não ordenados. O nome é uma cadeia Unicode, o valor pode ser de qualquer tipo de dados escalar, exceto **Indefinido**.|  
  
## <a name="syntax"></a><a name="bk_syntax"></a>Sintaxe
  
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
  
##  <a name="arguments"></a><a name="bk_arguments"></a>Argumentos
  
* `<undefined_constant>; Undefined`  
  
  Representa um valor indefinido do tipo Indefinido.  
  
* `<null_constant>; null`  
  
  Representa **um** valor nulo do tipo **Nulo.**  
  
* `<boolean_constant>`  
  
  Representa constante de tipo Boolean.  
  
* `false`  
  
  Representa **um** valor falso do tipo Boolean.  
  
* `true`  
  
  Representa **o verdadeiro** valor do tipo Boolean.  
  
* `<number_constant>`  
  
  Representa uma constante.  
  
* `decimal_literal`  
  
  Os literais decimais são números representados usando notação decimal ou notação científica.  
  
* `hexadecimal_literal`  
  
  Os literais hexadecimais são números representados usando o prefixo '0x' seguido de um ou mais dígitos hexadecimais.  
  
* `<string_constant>`  
  
  Representa uma constante de tipo String.  
  
* `string _literal`  
  
  Os literais de cordas são cordas Unicode representadas por uma sequência de caracteres Zero ou mais Unicode ou sequências de fuga. Os literais de cordas são incluídos em citações únicas (apóstrofe: ' ) ou citações duplas (marca de citação: ").  
  
  São permitidas sequências de fuga seguintes:  
  
|**Sequência de fuga**|**Descrição**|**Personagem unicódigo**|  
|-|-|-|  
|\\'|apóstrofo (')|U+0027|  
|\\"|marca de citação (")|U+0022|  
|\\\ |inverso sólido\\()|U+005C|  
|\\/|solidus (/)|U+002F|  
|\b|backspace|U+0008|  
|\f|alimentação de formulário|U+000C|  
|\n|feed de linha|U+000A|  
|\r|retorno de transporte|U+000D|  
|\t|separador|U+0009|  
|\uXXXX|Um caracteres Unicode definidopor 4 dígitos hexadecimais.|U+XXXX|  

## <a name="next-steps"></a>Passos seguintes

- [Amostras Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dados de documento do modelo](modeling-data.md)
