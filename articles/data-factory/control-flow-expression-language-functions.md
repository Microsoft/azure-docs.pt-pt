---
title: Expressão e funções no Azure Data Factory | Documentos da Microsoft
description: Este artigo fornece informações sobre expressões e funções que podem ser usadas na criação de entidades do data factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 4c51974498539a0305312d6501bcfa9ebc3b2e88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64573543"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Expressões e funções no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory, que está a utilizar:"]
> * [Versão 1](v1/data-factory-functions-variables.md)
> * [Versão atual](control-flow-expression-language-functions.md)

Este artigo fornece detalhes sobre expressões e funções do Azure Data Factory suportadas. 

## <a name="introduction"></a>Introdução
Valores JSON na definição podem ser literal ou expressões que são avaliadas no tempo de execução. Por exemplo:  
  
```json
"name": "value"
```

 (ou)  
  
```json
"name": "@pipeline().parameters.password"
```

## <a name="expressions"></a>Expressões  
Expressões podem aparecer em qualquer local num valor de cadeia de caracteres do JSON e sempre resulta em outro valor JSON. Se um valor JSON é uma expressão, o corpo da expressão é extraído, removendo no início de sessão (\@). Se necessitar de uma cadeia literal que começa com \@, devem ser escrito usando \@ \@. Os exemplos seguintes mostram como as expressões são avaliadas.  
  
|Valor JSON|Resultado|  
|----------------|------------|  
|"parameters"|Os carateres 'parameters' são devolvidos.|  
|"parâmetros [1]"|Os carateres 'parameters [1]' são devolvidos.|  
|"\@\@"|Uma cadeia de 1 caráter que contém '\@"é devolvido.|  
|" \@"|Uma cadeia de caracteres de 2 que contém ' \@"é devolvido.|  
  
 As expressões também podem aparecer dentro de cadeias de caracteres, usando um recurso chamado *interpolação de cadeias de caracteres* onde as expressões são encapsuladas em `@{ ... }`. Por exemplo: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Utilizar a interpolação de cadeia de caracteres, o resultado é sempre uma cadeia de caracteres. Digamos que eu defini `myNumber` como `42` e `myString` como `foo`:  
  
|Valor JSON|Resultado|  
|----------------|------------|  
|"\@pipeline().parameters.myString"| Devolve `foo` como uma cadeia de caracteres.|  
|"\@{pipeline().parameters.myString}"| Devolve `foo` como uma cadeia de caracteres.|  
|"\@pipeline().parameters.myNumber"| Devolve `42` como uma *número*.|  
|"\@{pipeline().parameters.myNumber}"| Devolve `42` como uma *cadeia*.|  
|"Resposta é: @{pipeline ().parameters.myNumber}"| Devolve a cadeia de caracteres `Answer is: 42`.|  
|"\@concat (' resposta é:", string(pipeline().parameters.myNumber)) "| Devolve a cadeia de caracteres `Answer is: 42`|  
|"Resposta é: \@ \@{pipeline ().parameters.myNumber}"| Devolve a cadeia de caracteres `Answer is: @{pipeline().parameters.myNumber}`.|  
  
### <a name="examples"></a>Exemplos

#### <a name="a-dataset-with-a-parameter"></a>Um conjunto de dados com um parâmetro
No exemplo a seguir, o BlobDataset assume um parâmetro denominado **caminho**. Seu valor é usado para definir um valor para o **folderPath** propriedade através da expressão: `dataset().path`. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

#### <a name="a-pipeline-with-a-parameter"></a>Um pipeline com um parâmetro
No exemplo a seguir, o pipeline utiliza **inputPath** e **outputPath** parâmetros. O **caminho** para o blob parametrizado conjunto de dados é definido com valores destes parâmetros. A sintaxe usada aqui é: `pipeline().parameters.parametername`. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
  
## <a name="functions"></a>Funções  
 Pode chamar funções nas expressões. As secções seguintes fornecem informações sobre as funções que pode ser utilizada uma expressão.  

## <a name="string-functions"></a>Funções de cadeia  
 As funções seguintes aplicam-se apenas às cadeias de caracteres. Também pode utilizar um número de funções a coleção em cadeias de caracteres.  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|concat|Combina qualquer número de cadeias de caracteres em conjunto. Por exemplo, se parâmetro1 for `foo,` retornaria a seguinte expressão `somevalue-foo-somevalue`:  `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Número de parâmetro**: 1 ... *n*<br /><br /> **Nome**: Cadeia de caracteres *n*<br /><br /> **Descrição**: Necessário. As cadeias de caracteres para combinar numa única cadeia de caracteres.|  
|subcadeia|Devolve um subconjunto de carateres de uma cadeia de caracteres. Por exemplo, a seguinte expressão:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> Devolve:<br /><br /> `foo`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrição**: Necessário. A cadeia a partir do qual a subcadeia é retirada.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Índice de início<br /><br /> **Descrição**: Necessário. O índice da qual a subcadeia começa no parâmetro 1.<br /><br /> **Número de parâmetro**: 3<br /><br /> **Nome**: Comprimento<br /><br /> **Descrição**: Necessário. O comprimento da subcadeia.|  
|substituir|Substitui uma cadeia de caracteres com uma determinada seqüência de caracteres. Por exemplo, a expressão:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> Devolve:<br /><br /> `the new string`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: cadeia<br /><br /> **Descrição**: Necessário.  Se o parâmetro 2 é encontrado no parâmetro 1, a cadeia que é procurada para o parâmetro 2 e atualizada com o parâmetro 3.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Cadeia de caracteres antiga<br /><br /> **Descrição**: Necessário. A cadeia a substituir pelo parâmetro 3 quando é encontrada uma correspondência no parâmetro 1<br /><br /> **Número de parâmetro**: 3<br /><br /> **Nome**: Nova cadeia de caracteres<br /><br /> **Descrição**: Necessário. A cadeia é utilizada para substituir a cadeia no parâmetro 2 quando é encontrada uma correspondência no parâmetro 1.|  
|GUID| Gera uma cadeia de caracteres exclusiva global (também conhecido como. GUID). Por exemplo, foi possível gerar o resultado seguinte `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Formato<br /><br /> **Descrição**: Opcional. Um especificador de formato único que indica [como formatar o valor deste GUID](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). O parâmetro de formato pode ser "N", "D", "B", "P" ou "X". Se o formato não for fornecido, "D" é utilizado.|  
|toLower|Converte uma cadeia de caracteres em minúsculas. Por exemplo, o seguinte devolve `two by two is four`:  `toLower('Two by Two is Four')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrição**: Necessário. A cadeia a converter em maiúsculas e minúsculas inferior. Se um caráter na cadeia não tiver um equivalente em minúsculas, ele é incluído inalterado na cadeia devolvida.|  
|toUpper|Converte uma cadeia de caracteres em maiúsculas. Por exemplo, a seguinte expressão retorna `TWO BY TWO IS FOUR`:  `toUpper('Two by Two is Four')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrição**: Necessário. A cadeia a converter em maiúsculas e minúsculas superior. Se um caráter na cadeia não tiver um equivalente em maiúsculas, ele é incluído inalterado na cadeia devolvida.|  
|indexof|Localize o índice de um valor dentro de um caso de cadeia de caracteres insensitively. Por exemplo, a seguinte expressão retorna `7`: `indexof('hello, world.', 'world')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrição**: Necessário. A cadeia de caracteres que pode conter o valor.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: String<br /><br /> **Descrição**: Necessário. O valor a pesquisar o índice de.|  
|lastindexof|Encontre o último índice de um valor dentro de um caso de cadeia de caracteres insensitively. Por exemplo, a seguinte expressão retorna `3`: `lastindexof('foofoo', 'foo')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrição**: Necessário. A cadeia de caracteres que pode conter o valor.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: String<br /><br /> **Descrição**: Necessário. O valor a pesquisar o índice de.|  
|startswith|Verifica se a cadeia começa com um caso de valor insensitively. Por exemplo, a seguinte expressão retorna `true`: `startswith('hello, world', 'hello')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrição**: Necessário. A cadeia de caracteres que pode conter o valor.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: String<br /><br /> **Descrição**: Necessário. O valor a cadeia poderá começar.|  
|endswith|Verifica se a cadeia termina com um caso de valor insensitively. Por exemplo, a seguinte expressão retorna `true`: `endswith('hello, world', 'world')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrição**: Necessário. A cadeia de caracteres que pode conter o valor.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: String<br /><br /> **Descrição**: Necessário. O valor a cadeia poderá terminar.|  
|dividir|Divide a cadeia de caracteres com um separador. Por exemplo, a seguinte expressão retorna `["a", "b", "c"]`: `split('a;b;c',';')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrição**: Necessário. A cadeia de caracteres que é dividida.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: String<br /><br /> **Descrição**: Necessário. O separador.|  
  
  
## <a name="collection-functions"></a>Funções de coleção  
 Estas funções funcionam através de coleções, como matrizes, cadeias de caracteres e, às vezes, dicionários.  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|Contém|Devolve VERDADEIRO se o dicionário contém uma lista de chaves, contém o valor ou cadeia de caracteres contém subcadeia. Por exemplo, retorna a seguinte expressão `true:``contains('abacaba','aca')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Dentro de coleção<br /><br /> **Descrição**: Necessário. A coleção em que procurar.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Localizar o objeto<br /><br /> **Descrição**: Necessário. O objeto a encontrar os **dentro de coleção**.|  
|length|Devolve o número de elementos de uma matriz ou cadeia de caracteres. Por exemplo, a seguinte expressão retorna `3`:  `length('abc')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Coleção<br /><br /> **Descrição**: Necessário. A coleção para obter o comprimento de.|  
|vazio|Devolve VERDADEIRO se o objeto, matriz ou cadeia de caracteres está vazia. Por exemplo, a seguinte expressão retorna `true`:<br /><br /> `empty('')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Coleção<br /><br /> **Descrição**: Necessário. A coleção para verificar se está vazia.|  
|intersecção|Devolve uma matriz simples ou o objeto com os elementos comuns entre as matrizes ou objetos passados para ele. Por exemplo, esta função devolve `[1, 2]`:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> Os parâmetros para a função podem ser um conjunto de objetos ou um conjunto de matrizes (não uma mistura de ambos). Se existirem dois objetos com o mesmo nome, o último objeto com esse nome aparece no objeto final.<br /><br /> **Número de parâmetro**: 1 ... *n*<br /><br /> **Nome**: Coleção *n*<br /><br /> **Descrição**: Necessário. As coleções a avaliar. Tem de ser um objeto em todas as coleções transmitidas para aparecer no resultado.|  
|União|Devolve uma matriz simples ou o objeto com todos os elementos que estão na matriz ou no objeto passado a ele. Por exemplo, esta função devolve `[1, 2, 3, 10, 101]:`<br /><br /> :  `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> Os parâmetros para a função podem ser um conjunto de objetos ou um conjunto de matrizes (não uma mistura de ambos). Se existirem dois objetos com o mesmo nome no resultado final, o último objeto com esse nome aparece no objeto final.<br /><br /> **Número de parâmetro**: 1 ... *n*<br /><br /> **Nome**: Coleção *n*<br /><br /> **Descrição**: Necessário. As coleções a avaliar. Um objeto que apareça em qualquer uma das coleções é apresentada no resultado.|  
|primeiro|Devolve o primeiro elemento da matriz ou cadeia transmitida. Por exemplo, esta função devolve `0`:<br /><br /> `first([0,2,3])`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Coleção<br /><br /> **Descrição**: Necessário. A coleção da qual o primeiro objeto será retirado.|  
|última|Devolve o último elemento na matriz ou cadeia transmitida. Por exemplo, esta função devolve `3`:<br /><br /> `last('0123')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Coleção<br /><br /> **Descrição**: Necessário. A coleção para tirar o último objeto.|  
|tirar|Devolve o primeiro **contagem** elementos da matriz ou cadeia transmitida, por exemplo esta função devolve `[1, 2]`:  `take([1, 2, 3, 4], 2)`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Coleção<br /><br /> **Descrição**: Necessário. A coleção da qual a primeira **contagem** objetos do.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Count<br /><br /> **Descrição**: Necessário. O número de objetos a retirar dos **coleção**. Tem de ser um número inteiro positivo.|  
|Ignorar|Devolve os elementos da matriz começando no índice **contagem**, por exemplo esta função devolve `[3, 4]`:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Coleção<br /><br /> **Descrição**: Necessário. A coleção para ignorar a primeira **contagem** objetos do.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Count<br /><br /> **Descrição**: Necessário. O número de objetos a remover da frente **coleção**. Tem de ser um número inteiro positivo.|  
  
## <a name="logical-functions"></a>Funções lógicas  
 Estas funções são úteis no interior de condições, eles podem ser utilizados para avaliar a qualquer tipo de lógica.  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|é igual a|Devolve VERDADEIRO se dois valores forem iguais. Por exemplo, se parâmetro1 for foo, a seguinte expressão retornaria `true`: `equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Objeto 1<br /><br /> **Descrição**: Necessário. O objeto a comparar ao **objeto 2**.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Objeto 2<br /><br /> **Descrição**: Necessário. O objeto a comparar ao **objeto 1**.|  
|menos|Devolve VERDADEIRO se o primeiro argumento for menor que o segundo. Tenha em atenção de que valores só podem ser do tipo número inteiro, vírgula flutuante ou de cadeia de caracteres. Por exemplo, a seguinte expressão retorna `true`:  `less(10,100)`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Objeto 1<br /><br /> **Descrição**: Necessário. O objeto a verificar se é inferior a **objeto 2**.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Objeto 2<br /><br /> **Descrição**: Necessário. O objeto a verificar se é superior a **objeto 1**.|  
|lessOrEquals|Devolve VERDADEIRO se o primeiro argumento for menor que ou igual ao segundo. Tenha em atenção de que valores só podem ser do tipo número inteiro, vírgula flutuante ou de cadeia de caracteres. Por exemplo, a seguinte expressão retorna `true`:  `lessOrEquals(10,10)`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Objeto 1<br /><br /> **Descrição**: Necessário. O objeto a verificar se é inferior ou igual a **objeto 2**.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Objeto 2<br /><br /> **Descrição**: Necessário. O objeto a verificar se é maior que ou igual a **objeto 1**.|  
|maior|Devolve VERDADEIRO se o primeiro argumento for maior do que o segundo. Tenha em atenção de que valores só podem ser do tipo número inteiro, vírgula flutuante ou de cadeia de caracteres. Por exemplo, a seguinte expressão retorna `false`:  `greater(10,10)`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Objeto 1<br /><br /> **Descrição**: Necessário. O objeto a verificar se é superior a **objeto 2**.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Objeto 2<br /><br /> **Descrição**: Necessário. O objeto a verificar se é inferior a **objeto 1**.|  
|greaterOrEquals|Devolve VERDADEIRO se o primeiro argumento for maior que ou igual ao segundo. Tenha em atenção de que valores só podem ser do tipo número inteiro, vírgula flutuante ou de cadeia de caracteres. Por exemplo, a seguinte expressão retorna `false`:  `greaterOrEquals(10,100)`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Objeto 1<br /><br /> **Descrição**: Necessário. O objeto a verificar se é maior que ou igual a **objeto 2**.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Objeto 2<br /><br /> **Descrição**: Necessário. O objeto a verificar se é inferior ou igual a **objeto 1**.|  
|e|Devolve VERDADEIRO se ambos os parâmetros forem verdadeiros. Ambos os argumentos precisam ser booleanos. A seguinte devolve `false`:  `and(greater(1,10),equals(0,0))`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: 1 booleano<br /><br /> **Descrição**: Necessário. O primeiro argumento tem de ser `true`.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: 2 booleano<br /><br /> **Descrição**: Necessário. O segundo argumento tem de ser `true`.|  
|ou|Devolve VERDADEIRO se qualquer um dos parâmetros forem VERDADEIRO. Ambos os argumentos precisam ser booleanos. A seguinte devolve `true`:  `or(greater(1,10),equals(0,0))`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: 1 booleano<br /><br /> **Descrição**: Necessário. O primeiro argumento que pode ser `true`.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: 2 booleano<br /><br /> **Descrição**: Necessário. O segundo argumento pode ser `true`.|  
|não|Devolve VERDADEIRO se o parâmetro for `false`. Ambos os argumentos precisam ser booleanos. A seguinte devolve `true`:  `not(contains('200 Success','Fail'))`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Boolean<br /><br /> **Descrição**: Devolve VERDADEIRO se o parâmetro for `false`. Ambos os argumentos precisam ser booleanos. A seguinte devolve `true`:  `not(contains('200 Success','Fail'))`|  
|IF|Devolve um valor especificado com base em se os resultados de expressão fornecida no `true` ou `false`.  Por exemplo, o seguinte devolve `"yes"`: `if(equals(1, 1), 'yes', 'no')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: expressão<br /><br /> **Descrição**: Necessário. Um valor booleano que determina o valor que é devolvido pela expressão.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Verdadeiro<br /><br /> **Descrição**: Necessário. O valor a devolver se a expressão for `true`.<br /><br /> **Número de parâmetro**: 3<br /><br /> **Nome**: Falso<br /><br /> **Descrição**: Necessário. O valor a devolver se a expressão for `false`.|  
  
## <a name="conversion-functions"></a>Funções de conversão  
 Estas funções são utilizadas para converter entre cada um dos tipos nativos no idioma:  
  
-   string  
  
-   inteiro  
  
-   float  
  
-   boolean  
  
-   matrizes  
  
-   dicionários  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|int|Converta o parâmetro para um número inteiro. Por exemplo, a seguinte expressão retorna 100 como um número, em vez de uma cadeia de caracteres:  `int('100')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrição**: Necessário. O valor que é convertido para um número inteiro.|  
|string|Converta o parâmetro para uma cadeia de caracteres. Por exemplo, a seguinte expressão retorna `'10'`:  `string(10)` Também pode converter um objeto numa cadeia de caracteres, por exemplo, se o **foo** parâmetro é um objeto com uma propriedade `bar : baz`, em seguida, o seguinte irá devolver `{"bar" : "baz"}` `string(pipeline().parameters.foo)`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrição**: Necessário. O valor que é convertido numa cadeia.|  
|json|Converta o parâmetro para um valor de tipo JSON. É o oposto de String (). Por exemplo, a seguinte expressão retorna `[1,2,3]` como uma matriz, em vez de uma cadeia de caracteres:<br /><br /> `json('[1,2,3]')`<br /><br /> Da mesma forma, pode converter uma cadeia de caracteres a um objeto. Por exemplo, `json('{"bar" : "baz"}')` devolve:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrição**: Necessário. A cadeia de caracteres que é convertida para um valor de tipo nativo.<br /><br /> A função de json suporta também a entrada de xml. Por exemplo, o valor do parâmetro de:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> é convertido para o seguinte json:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|Converta o argumento do parâmetro para um número de vírgula flutuante. Por exemplo, a seguinte expressão retorna `10.333`:  `float('10.333')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrição**: Necessário. O valor que é convertido para um número de vírgula flutuante.|  
|bool|Converta o parâmetro para um valor booleano. Por exemplo, a seguinte expressão retorna `false`:  `bool(0)`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrição**: Necessário. O valor que é convertido para booleano.|  
|Coalesce|Devolve o primeiro objeto não nulo nos argumentos passados. Nota: uma cadeia vazia não é nula. Por exemplo, se os parâmetros 1 e 2 não são definidos, esta ação devolve `fallback`:  `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Número de parâmetro**: 1 ... *n*<br /><br /> **Nome**: Object*n*<br /><br /> **Descrição**: Necessário. Os objetos para procurar `null`.|  
|base64|Devolve a representação base64 de cadeia de entrada. Por exemplo, a seguinte expressão retorna `c29tZSBzdHJpbmc=`:  `base64('some string')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: A cadeia de caracteres 1<br /><br /> **Descrição**: Necessário. A cadeia a codificar numa representação base64.|  
|base64ToBinary|Devolve uma representação binária de uma cadeia de caracteres codificada em base64. Por exemplo, a expressão a seguir devolve a representação binária da alguma cadeia de caracteres: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrição**: Necessário. A cadeia de caracteres codificada em base64.|  
|base64ToString|Devolve uma representação de cadeia de caracteres de uma cadeia based64 codificado. Por exemplo, a seguinte expressão retorna alguma cadeia de caracteres: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrição**: Necessário. A cadeia de caracteres codificada em base64.|  
|Binário|Devolve uma representação binária de um valor.  Por exemplo, a expressão a seguir retorna uma representação binária de alguma cadeia de caracteres: `binary(‘some string’).`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrição**: Necessário. O valor que é convertido num binário.|  
|dataUriToBinary|Devolve uma representação binária de um URI de dados. Por exemplo, a seguinte expressão devolve a representação binária de alguma cadeia de caracteres: `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrição**: Necessário. O URI a converter em representação binária de dados.|  
|dataUriToString|Devolve uma representação de cadeia de caracteres de um URI de dados. Por exemplo, a seguinte expressão retorna alguma cadeia de caracteres: `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: String<br /><br />**Descrição**: Necessário. O URI a converter em representação de cadeia de caracteres de dados.|  
|dataUri|Devolve um URI de um valor de dados. Por exemplo, a seguinte expressão retorna dados: `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Número de parâmetro**: 1<br /><br />**Nome**: Value<br /><br />**Descrição**: Necessário. O valor a converter em URI de dados.|  
|decodeBase64|Devolve uma representação de cadeia de caracteres de uma cadeia based64 de entrada. Por exemplo, a seguinte expressão retorna `some string`:  `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrição**: Devolve uma representação de cadeia de caracteres de uma cadeia based64 de entrada.|  
|encodeUriComponent|URL escapa a cadeia de caracteres que é passada. Por exemplo, a seguinte expressão retorna `You+Are%3ACool%2FAwesome`:  `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrição**: Necessário. A cadeia de carateres não seguros de URL de escape.|  
|decodeUriComponent|Anular-URL-escapa a cadeia de caracteres que é passada. Por exemplo, a seguinte expressão retorna `You Are:Cool/Awesome`:  `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrição**: Necessário. A cadeia a descodificar os carateres não seguros de URL de.|  
|decodeDataUri|Devolve uma representação binária de uma cadeia URI os dados de entrada. Por exemplo, a expressão a seguir devolve a representação binária da `some string`:  `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: String<br /><br /> **Descrição**: Necessário. O URI de dados a descodificar para uma representação binária.|  
|uriComponent|Devolve um URI codificado representação de um valor. Por exemplo, retorna a seguinte expressão `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Detalhes de parâmetro: Número: 1, nome: Cadeia de caracteres, Descrição: Necessário. A cadeia a ser codificada por URI.|  
|uriComponentToBinary|Devolve que uma representação binária de um URI de cadeia codificada. Por exemplo, a expressão a seguir retorna uma representação binária de `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: String<br /><br />**Descrição**: Necessário. A cadeia de caracteres codificada por URI.|  
|uriComponentToString|Devolve que uma representação de cadeia de caracteres de um URI cadeia codificada. Por exemplo, a seguinte expressão retorna `You Are:Cool/Awesome`: `uriComponentToString('You+Are%3ACool%2FAwesome')`<br /><br /> **Número de parâmetro**: 1<br /><br />**Nome**: String<br /><br />**Descrição**: Necessário. A cadeia de caracteres codificada por URI.|  
|xml|Devolve uma representação xml do valor. Por exemplo, a seguinte expressão retorna um conteúdo de xml representado pelo `'\<name>Alan\</name>'`: `xml('\<name>Alan\</name>')`. A função de xml suporta também entrada de objeto em JSON. Por exemplo, o parâmetro `{ "abc": "xyz" }` é convertido para um conteúdo de xml `\<abc>xyz\</abc>`<br /><br /> **Número de parâmetro**: 1<br /><br />**Nome**: Value<br /><br />**Descrição**: Necessário. O valor a converter em XML.|  
|xpath|Retornar uma matriz de nós de xml que correspondem a expressão xpath de um valor que a expressão xpath é avaliada como.<br /><br />  **Exemplo 1**<br /><br /> Suponha que o valor do parâmetro 'p1' é uma representação de cadeia de caracteres do seguinte XML:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. Este código: `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> retornaria<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> ao passo que<br /><br /> 2. Este código: `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> retornaria<br /><br /> `13`<br /><br /> <br /><br /> **Exemplo 2**<br /><br /> Tendo em conta o seguinte conteúdo XML:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1.  Este código: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> ou<br /><br /> 2. Este código: `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> Devolve<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> e<br /><br /> 3. Este código: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> Devolve<br /><br /> ``bar``<br /><br /> **Número de parâmetro**: 1<br /><br />**Nome**: Xml<br /><br />**Descrição**: Necessário. O XML no qual pretende avaliar a expressão XPath.<br /><br /> **Número de parâmetro**: 2<br /><br />**Nome**: XPath<br /><br />**Descrição**: Necessário. A expressão de XPath a avaliar.|  
|array|Converta o parâmetro numa matriz.  Por exemplo, a seguinte expressão retorna `["abc"]`: `array('abc')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrição**: Necessário. O valor que é convertido numa matriz.|
|createArray|Cria uma matriz dos parâmetros.  Por exemplo, a seguinte expressão retorna `["a", "c"]`: `createArray('a', 'c')`<br /><br /> **Número de parâmetro**: 1... n<br /><br /> **Nome**: Qualquer n<br /><br /> **Descrição**: Necessário. Os valores a combinar numa matriz.|

## <a name="math-functions"></a>Funções matemáticas  
 Estas funções podem ser utilizadas para qualquer um dos tipos de números: **números inteiros** e **flutua**.  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|adicionar|Devolve o resultado da adição dos dois números. Por exemplo, esta função devolve `20.333`:  `add(10,10.333)`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Parcela 1<br /><br /> **Descrição**: Necessário. O número a adicionar à **parcela 2**.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Parcela 2<br /><br /> **Descrição**: Necessário. O número a adicionar à **parcela 1**.|  
|sub|Devolve o resultado da subtração dos dois números. Por exemplo, esta função devolve: `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: minuendo<br /><br /> **Descrição**: Necessário. O número que **Subtraendo** é removido.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Subtrahend<br /><br /> **Descrição**: Necessário. O número para remover o **Minuendo**.|  
|mul|Devolve o resultado da multiplicação dos dois números. Por exemplo, o seguinte devolve `103.33`:<br /><br /> `mul(10,10.333)`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Multiplicando 1<br /><br /> **Descrição**: Necessário. O número multiplica **multiplicando 2** com.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Multiplicando 2<br /><br /> **Descrição**: Necessário. O número multiplica **multiplicando 1** com.|  
|div|Devolve o resultado da divisão dos dois números. Por exemplo, o seguinte devolve `1.0333`:<br /><br /> `div(10.333,10)`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: dividendo<br /><br /> **Descrição**: Necessário. O número pelo qual dividir o **Divisor**.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Divisor<br /><br /> **Descrição**: Necessário. O número a dividir os **dividendo** pelo.|  
|mod|Devolve o resultado do restante após a divisão dos dois números (módulo). Por exemplo, a seguinte expressão retorna `2`:<br /><br /> `mod(10,4)`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: dividendo<br /><br /> **Descrição**: Necessário. O número pelo qual dividir o **Divisor**.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Divisor<br /><br /> **Descrição**: Necessário. O número a dividir os **dividendo** pelo. Após a divisão, o resto é retirado.|  
|min.|Existem dois padrões diferentes para chamar essa função: `min([0,1,2])` Aqui, min terá uma matriz. Esta expressão devolve `0`. Em alternativa, esta função pode ter uma lista separada por vírgulas de valores:  `min(0,1,2)` Esta função também devolve 0. Tenha em atenção de que todos os valores têm de ser números, portanto, se o parâmetro é uma matriz tem de ter apenas os números na mesma.<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Coleção ou valor<br /><br /> **Descrição**: Necessário. Ele pode ser uma matriz de valores para encontrar o valor mínimo ou o primeiro valor de um conjunto.<br /><br /> **Número de parâmetro**: 2 ... *n*<br /><br /> **Nome**: Value *n*<br /><br /> **Descrição**: Opcional. Se o primeiro parâmetro é um valor, em seguida, pode passar valores adicionais e o mínimo de todos os valores transmitidos são devolvidos.|  
|max|Existem dois padrões diferentes para chamar essa função:  `max([0,1,2])`<br /><br /> Aqui max assume uma matriz. Esta expressão devolve `2`. Em alternativa, esta função pode ter uma lista separada por vírgulas de valores:  `max(0,1,2)` Esta função devolve 2. Tenha em atenção de que todos os valores têm de ser números, portanto, se o parâmetro é uma matriz tem de ter apenas os números na mesma.<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Coleção ou valor<br /><br /> **Descrição**: Necessário. Ele pode ser uma matriz de valores para encontrar o valor máximo ou o primeiro valor de um conjunto.<br /><br /> **Número de parâmetro**: 2 ... *n*<br /><br /> **Nome**: Value *n*<br /><br /> **Descrição**: Opcional. Se o primeiro parâmetro é um valor, em seguida, pode passar valores adicionais e o número máximo de todos os valores transmitidos são devolvidos.|  
|Intervalo| Gera uma matriz de inteiros a partir de um determinado número, e definir o comprimento da matriz retornada. Por exemplo, esta função devolve `[3,4,5,6]`:<br /><br /> `range(3,4)`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Índice de início<br /><br /> **Descrição**: Necessário. É o primeiro número inteiro na matriz.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Contagem<br /><br /> **Descrição**: Necessário. Número de números inteiros que estão na matriz.|  
|rand| Gera um número inteiro aleatório dentro do intervalo especificado (inclusive em ambas as extremidades. Por exemplo, isso pode devolver `42`:<br /><br /> `rand(-1000,1000)`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Mínimo<br /><br /> **Descrição**: Necessário. O menor número inteiro que pode ser devolvido.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Máximo<br /><br /> **Descrição**: Necessário. O número inteiro maior que pode ser devolvido.|  
  
## <a name="date-functions"></a>Funções de data  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|utcnow|Devolve o timestamp atual como uma cadeia de caracteres. Por exemplo `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Formato<br /><br /> **Descrição**: Opcional. De qualquer um [único caráter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou uma [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor deste timestamp. Se o formato não for fornecido, é utilizado o formato ISO 8601 ("o").|  
|addseconds|Adiciona um número inteiro de segundos para um carimbo de cadeia transmitido. O número de segundos pode ser positivo ou negativo. O resultado é uma cadeia de caracteres no formato ISO 8601 ("o") por predefinição, a menos que é fornecido um especificador de formato. Por exemplo `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Carimbo de data/hora<br /><br /> **Descrição**: Necessário. Uma cadeia de caracteres que contém a hora.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Segundos<br /><br /> **Descrição**: Necessário. O número de segundos a adicionar. Pode ser negativo para subtrair segundos.<br /><br /> **Número de parâmetro**: 3<br /><br /> **Nome**: Formato<br /><br /> **Descrição**: Opcional. De qualquer um [único caráter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou uma [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor deste timestamp. Se o formato não for fornecido, é utilizado o formato ISO 8601 ("o").|  
|addminutes|Adiciona um número inteiro de minutos para um carimbo de cadeia transmitido. O número de minutos pode ser positivo ou negativo. O resultado é uma cadeia de caracteres no formato ISO 8601 ("o") por predefinição, a menos que é fornecido um especificador de formato. Por exemplo, `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Carimbo de data/hora<br /><br /> **Descrição**: Necessário. Uma cadeia de caracteres que contém a hora.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Minutos<br /><br /> **Descrição**: Necessário. O número de minutos a adicionar. Pode ser negativo para subtrair minutos.<br /><br /> **Número de parâmetro**: 3<br /><br /> **Nome**: Formato<br /><br /> **Descrição**: Opcional. De qualquer um [único caráter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou uma [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor deste timestamp. Se o formato não for fornecido, é utilizado o formato ISO 8601 ("o").|  
|addhours|Adiciona um número inteiro de horas para um carimbo de cadeia transmitido. O número de horas pode ser positivo ou negativo. O resultado é uma cadeia de caracteres no formato ISO 8601 ("o") por predefinição, a menos que é fornecido um especificador de formato. Por exemplo `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Carimbo de data/hora<br /><br /> **Descrição**: Necessário. Uma cadeia de caracteres que contém a hora.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Horas<br /><br /> **Descrição**: Necessário. O número de horas a adicionar. Pode ser negativo para subtrair horas.<br /><br /> **Número de parâmetro**: 3<br /><br /> **Nome**: Formato<br /><br /> **Descrição**: Opcional. De qualquer um [único caráter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou uma [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor deste timestamp. Se o formato não for fornecido, é utilizado o formato ISO 8601 ("o").|  
|adddays|Adiciona um número inteiro de dias a um carimbo de cadeia transmitido. O número de dias pode ser positivo ou negativo. O resultado é uma cadeia de caracteres no formato ISO 8601 ("o") por predefinição, a menos que é fornecido um especificador de formato. Por exemplo `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Carimbo de data/hora<br /><br /> **Descrição**: Necessário. Uma cadeia de caracteres que contém a hora.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Dias<br /><br /> **Descrição**: Necessário. O número de dias a adicionar. Pode ser negativo para subtrair dias.<br /><br /> **Número de parâmetro**: 3<br /><br /> **Nome**: Formato<br /><br /> **Descrição**: Opcional. De qualquer um [único caráter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou uma [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor deste timestamp. Se o formato não for fornecido, é utilizado o formato ISO 8601 ("o").|  
|formatDateTime|Devolve uma cadeia de caracteres no formato de data. O resultado é uma cadeia de caracteres no formato ISO 8601 ("o") por predefinição, a menos que é fornecido um especificador de formato. Por exemplo `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br /> **Número de parâmetro**: 1<br /><br /> **Nome**: Date<br /><br /> **Descrição**: Necessário. Uma cadeia que contém a data.<br /><br /> **Número de parâmetro**: 2<br /><br /> **Nome**: Formato<br /><br /> **Descrição**: Opcional. De qualquer um [único caráter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou uma [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor deste timestamp. Se o formato não for fornecido, é utilizado o formato ISO 8601 ("o").|  

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista de variáveis de sistema, pode usar em expressões, consulte [variáveis do sistema](control-flow-system-variables.md).
