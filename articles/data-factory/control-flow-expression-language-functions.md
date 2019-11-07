---
title: Expressão e funções no Azure Data Factory
description: Este artigo fornece informações sobre expressões e funções que você pode usar ao criar data factory entidades.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 75441a398e654893601cb7375ad3674d2b8aff29
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679905"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Expressões e funções no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-functions-variables.md)
> * [Versão atual](control-flow-expression-language-functions.md)

Este artigo fornece detalhes sobre expressões e funções com suporte pelo Azure Data Factory. 

## <a name="introduction"></a>Introdução
Os valores JSON na definição podem ser literais ou expressões que são avaliadas em tempo de execução. Por exemplo:  
  
```json
"name": "value"
```

 or  
  
```json
"name": "@pipeline().parameters.password"
```

## <a name="expressions"></a>Expressões  
As expressões podem aparecer em qualquer lugar em um valor de cadeia de caracteres JSON e sempre resultar em outro valor JSON. Se um valor JSON for uma expressão, o corpo da expressão será extraído removendo o sinal de arroba (\@). Se for necessária uma cadeia de caracteres literal que comece com \@, ela deverá ser substituída usando \@\@. Os exemplos a seguir mostram como as expressões são avaliadas.  
  
|Valor JSON|Resultado|  
|----------------|------------|  
|parâmetro|Os caracteres ' Parameters ' são retornados.|  
|"parâmetros [1]"|Os caracteres ' parameters [1] ' são retornados.|  
|"\@\@"|Uma cadeia de caracteres de 1 caractere que contém '\@' é retornada.|  
|"\@"|Uma cadeia de caracteres de 2 caracteres que contém ' \@' é retornada.|  
  
 As expressões também podem aparecer dentro de cadeias de caracteres, usando um recurso chamado *interpolação de cadeia de caracteres* onde as expressões são encapsuladas em `@{ ... }`. Por exemplo: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Usando a interpolação de cadeia de caracteres, o resultado é sempre uma cadeia de caracteres. Digamos que defini `myNumber` como `42` e `myString` como `foo`:  
  
|Valor JSON|Resultado|  
|----------------|------------|  
|"\@pipeline (). Parameters. myString"| Retorna `foo` como uma cadeia de caracteres.|  
|"\@{pipeline (). Parameters. myString}"| Retorna `foo` como uma cadeia de caracteres.|  
|"\@pipeline (). Parameters. MyNumber"| Retorna `42` como um *número*.|  
|"\@{pipeline (). Parameters. MyNumber}"| Retorna `42` como uma *cadeia de caracteres*.|  
|"A resposta é: @ {pipeline (). Parameters. MyNumber}"| Retorna a cadeia de caracteres `Answer is: 42`.|  
|"\@Concat (' a resposta é: ', String (pipeline (). Parameters. MyNumber))"| Retorna a cadeia de caracteres `Answer is: 42`|  
|"A resposta é: \@\@{pipeline (). Parameters. MyNumber}"| Retorna a cadeia de caracteres `Answer is: @{pipeline().parameters.myNumber}`.|  
  
### <a name="examples"></a>Exemplos

#### <a name="a-dataset-with-a-parameter"></a>Um DataSet com um parâmetro
No exemplo a seguir, o BlobDataset usa um parâmetro chamado **path**. Seu valor é usado para definir um valor para a propriedade **FolderPath** usando a expressão: `dataset().path`. 

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
No exemplo a seguir, o pipeline usa os parâmetros **inputPath** e **outputPath** . O **caminho** para o conjunto de valores de blob com parâmetros é definido usando valores desses parâmetros. A sintaxe usada aqui é: `pipeline().parameters.parametername`. 

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
#### <a name="tutorial"></a>Tutorial
Este [tutorial](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) orienta você sobre como passar parâmetros entre um pipeline e uma atividade, bem como entre as atividades.

  
## <a name="functions"></a>Funções  
 Você pode chamar funções dentro de expressões. As seções a seguir fornecem informações sobre as funções que podem ser usadas em uma expressão.  

## <a name="string-functions"></a>Funções de cadeia  
 As funções a seguir se aplicam somente a cadeias de caracteres. Você também pode usar um número de funções de coleção em cadeias de caracteres.  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|Concat|Combina qualquer número de cadeias de caracteres juntas. Por exemplo, se o parâmetro1 for `foo,` a expressão a seguir retornará `somevalue-foo-somevalue`: `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Número do parâmetro**: 1... *n*<br /><br /> **Nome**: cadeia de caracteres *n*<br /><br /> **Descrição**: obrigatório. As cadeias de caracteres a serem combinadas em uma única cadeia.|  
|Subcadeia|Retorna um subconjunto de caracteres de uma cadeia. Por exemplo, a expressão a seguir:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> apresenta<br /><br /> `foo`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: obrigatório. A cadeia de caracteres da qual a subcadeia de caracteres é tirada.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: índice de início<br /><br /> **Descrição**: obrigatório. O índice de onde a subcadeia de caracteres começa no parâmetro 1. O índice de início é baseado em zero. <br /><br /> **Número do parâmetro**: 3<br /><br /> **Nome**: comprimento<br /><br /> **Descrição**: obrigatório. O comprimento da subcadeia de caracteres.|  
|Substitua|Substitui uma cadeia de caracteres por uma determinada cadeia de caracteres. Por exemplo, a expressão:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> apresenta<br /><br /> `the new string`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: obrigatório.  Se o parâmetro 2 for encontrado no parâmetro 1, a cadeia de caracteres pesquisada para o parâmetro 2 e atualizada com o parâmetro 3.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: cadeia de caracteres antiga<br /><br /> **Descrição**: obrigatório. A cadeia de caracteres a ser substituída pelo parâmetro 3 quando uma correspondência for encontrada no parâmetro 1<br /><br /> **Número do parâmetro**: 3<br /><br /> **Nome**: nova cadeia de caracteres<br /><br /> **Descrição**: obrigatório. A cadeia de caracteres usada para substituir a cadeia de caracteres no parâmetro 2 quando uma correspondência é encontrada no parâmetro 1.|  
|guid| Gera uma cadeia de caracteres globalmente exclusiva (também conhecida como. GUID). Por exemplo, a saída a seguir pode ser gerada `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: formato<br /><br /> **Descrição**: opcional. Um único especificador de formato que indica [como formatar o valor desse GUID](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). O parâmetro de formato pode ser "N", "D", "B", "P" ou "X". Se o formato não for fornecido, "D" será usado.|  
|toLower|Converte uma cadeia de caracteres em minúsculas. Por exemplo, o seguinte retorna `two by two is four`: `toLower('Two by Two is Four')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: obrigatório. A cadeia de caracteres a ser convertida em minúsculas. Se um caractere na cadeia de caracteres não tiver um equivalente em minúsculas, ele será incluído inalterado na cadeia de caracteres retornada.|  
|toUpper|Converte uma cadeia de caracteres em maiúsculas. Por exemplo, a expressão a seguir retorna `TWO BY TWO IS FOUR`: `toUpper('Two by Two is Four')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: obrigatório. A cadeia de caracteres a ser convertida em letras maiúsculas. Se um caractere na cadeia de caracteres não tiver um equivalente em maiúsculas, ele será incluído inalterado na cadeia de caracteres retornada.|  
|IndexOf|Localize o índice de um valor dentro de um caso de cadeia de caracteres sem distinção. O índice é baseado em zero. Por exemplo, a expressão a seguir retorna `7`: `indexof('hello, world.', 'world')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: obrigatório. A cadeia de caracteres que pode conter o valor.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: obrigatório. O valor para pesquisar o índice.|  
|LastIndexOf|Localize o último índice de um valor dentro de um caso de cadeia de caracteres sem distinção. O índice é baseado em zero. Por exemplo, a expressão a seguir retorna `3`: `lastindexof('foofoo', 'foo')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: obrigatório. A cadeia de caracteres que pode conter o valor.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: obrigatório. O valor para pesquisar o índice.|  
|StartsWith|Verifica se a cadeia de caracteres começa com um valor de maiúsculas e minúsculas. Por exemplo, a expressão a seguir retorna `true`: `startswith('hello, world', 'hello')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: obrigatório. A cadeia de caracteres que pode conter o valor.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: obrigatório. O valor com o qual a cadeia de caracteres pode começar.|  
|EndsWith|Verifica se a cadeia de caracteres termina com um valor de maiúsculas e minúsculas. Por exemplo, a expressão a seguir retorna `true`: `endswith('hello, world', 'world')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: obrigatório. A cadeia de caracteres que pode conter o valor.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: obrigatório. O valor com o qual a cadeia de caracteres pode terminar.|  
|dividir|Divide a cadeia de caracteres usando um separador. Por exemplo, a expressão a seguir retorna `["a", "b", "c"]`: `split('a;b;c',';')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: obrigatório. A cadeia de caracteres dividida.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: obrigatório. O separador.|  
  
  
## <a name="collection-functions"></a>Funções de coleção  
 Essas funções operam em coleções como matrizes, cadeias de caracteres e, às vezes, dicionários.  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|terá|Retorna true se o dicionário contém uma chave, a lista contém um valor ou uma cadeia de caracteres contém subcadeia de caracteres. Por exemplo, a expressão a seguir retorna `true:``contains('abacaba','aca')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: dentro da coleção<br /><br /> **Descrição**: obrigatório. A coleção na qual Pesquisar.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: localizar objeto<br /><br /> **Descrição**: obrigatório. O objeto a ser localizado dentro da **coleção dentro**.|  
|muito|Retorna o número de elementos em uma matriz ou cadeia de caracteres. Por exemplo, a expressão a seguir retorna `3`: `length('abc')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: coleção<br /><br /> **Descrição**: obrigatório. A coleção para obter o comprimento de.|  
|esvaziá|Retornará true se o objeto, a matriz ou a cadeia de caracteres estiver vazia. Por exemplo, a expressão a seguir retorna `true`:<br /><br /> `empty('')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: coleção<br /><br /> **Descrição**: obrigatório. A coleção para verificar se ela está vazia.|  
|interseção|Retorna uma única matriz ou objeto com os elementos comuns entre as matrizes ou os objetos passados a ele. Por exemplo, essa função retorna `[1, 2]`:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> Os parâmetros para a função podem ser um conjunto de objetos ou um conjunto de matrizes (não uma combinação deles). Se houver dois objetos com o mesmo nome, o último objeto com esse nome aparecerá no objeto final.<br /><br /> **Número do parâmetro**: 1... *n*<br /><br /> **Nome**: coleção *n*<br /><br /> **Descrição**: obrigatório. As coleções a serem avaliadas. Um objeto deve estar em todas as coleções passadas para aparecer no resultado.|  
|union|Retorna uma única matriz ou objeto com todos os elementos que estão em uma matriz ou objeto passado para ele. Por exemplo, essa função retorna `[1, 2, 3, 10, 101]:`<br /><br /> : `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> Os parâmetros para a função podem ser um conjunto de objetos ou um conjunto de matrizes (não uma combinação deles). Se houver dois objetos com o mesmo nome na saída final, o último objeto com esse nome aparecerá no objeto final.<br /><br /> **Número do parâmetro**: 1... *n*<br /><br /> **Nome**: coleção *n*<br /><br /> **Descrição**: obrigatório. As coleções a serem avaliadas. Um objeto que aparece em qualquer uma das coleções aparece no resultado.|  
|primeiro|Retorna o primeiro elemento na matriz ou na cadeia de caracteres passada. Por exemplo, essa função retorna `0`:<br /><br /> `first([0,2,3])`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: coleção<br /><br /> **Descrição**: obrigatório. A coleção da qual obter o primeiro objeto.|  
|última|Retorna o último elemento na matriz ou na cadeia de caracteres passada. Por exemplo, essa função retorna `3`:<br /><br /> `last('0123')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: coleção<br /><br /> **Descrição**: obrigatório. A coleção da qual o último objeto será levado.|  
|Ter|Retorna os primeiros elementos de **contagem** da matriz ou da cadeia de caracteres passada, por exemplo, essa função retorna `[1, 2]`: `take([1, 2, 3, 4], 2)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: coleção<br /><br /> **Descrição**: obrigatório. A coleção da qual obter os primeiros objetos de **contagem** .<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: contagem<br /><br /> **Descrição**: obrigatório. O número de objetos a serem tirados da **coleção**. Deve ser um inteiro positivo.|  
|saltar|Retorna os elementos na matriz começando na **contagem**de índice, por exemplo, essa função retorna `[3, 4]`:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: coleção<br /><br /> **Descrição**: obrigatório. A coleção da qual ignorar os primeiros objetos de **contagem** .<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: contagem<br /><br /> **Descrição**: obrigatório. O número de objetos a serem removidos da frente da **coleção**. Deve ser um inteiro positivo.|  
  
## <a name="logical-functions"></a>Funções lógicas  
 Essas funções são úteis dentro das condições, elas podem ser usadas para avaliar qualquer tipo de lógica.  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|seja|Retornará true se dois valores forem iguais. Por exemplo, se o parâmetro1 for foo, a expressão a seguir retornará `true`: `equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: objeto 1<br /><br /> **Descrição**: obrigatório. O objeto a ser comparado com o **objeto 2**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: objeto 2<br /><br /> **Descrição**: obrigatório. O objeto a ser comparado com o **objeto 1**.|  
|inferiores|Retornará true se o primeiro argumento for menor que o segundo. Observe que os valores só podem ser do tipo inteiro, flutuante ou cadeia de caracteres. Por exemplo, a expressão a seguir retorna `true`: `less(10,100)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: objeto 1<br /><br /> **Descrição**: obrigatório. O objeto para verificar se ele é menor que o **objeto 2**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: objeto 2<br /><br /> **Descrição**: obrigatório. O objeto para verificar se ele é maior que o **objeto 1**.|  
|lessOrEquals|Retornará true se o primeiro argumento for menor ou igual ao segundo. Observe que os valores só podem ser do tipo inteiro, flutuante ou cadeia de caracteres. Por exemplo, a expressão a seguir retorna `true`: `lessOrEquals(10,10)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: objeto 1<br /><br /> **Descrição**: obrigatório. O objeto para verificar se ele é menor ou igual ao **objeto 2**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: objeto 2<br /><br /> **Descrição**: obrigatório. O objeto para verificar se ele é maior ou igual ao **objeto 1**.|  
|grande|Retornará true se o primeiro argumento for maior que o segundo. Observe que os valores só podem ser do tipo inteiro, flutuante ou cadeia de caracteres. Por exemplo, a expressão a seguir retorna `false`: `greater(10,10)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: objeto 1<br /><br /> **Descrição**: obrigatório. O objeto para verificar se ele é maior que o **objeto 2**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: objeto 2<br /><br /> **Descrição**: obrigatório. O objeto para verificar se ele é menor que o **objeto 1**.|  
|greaterOrEquals|Retornará true se o primeiro argumento for maior ou igual ao segundo. Observe que os valores só podem ser do tipo inteiro, flutuante ou cadeia de caracteres. Por exemplo, a expressão a seguir retorna `false`: `greaterOrEquals(10,100)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: objeto 1<br /><br /> **Descrição**: obrigatório. O objeto para verificar se ele é maior ou igual ao **objeto 2**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: objeto 2<br /><br /> **Descrição**: obrigatório. O objeto para verificar se ele é menor ou igual ao **objeto 1**.|  
|e|Retornará true se ambos os parâmetros forem verdadeiros. Ambos os argumentos precisam ser boolianos. O seguinte retorna `false`: `and(greater(1,10),equals(0,0))`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: booliano 1<br /><br /> **Descrição**: obrigatório. O primeiro argumento que deve ser `true`.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: booliano 2<br /><br /> **Descrição**: obrigatório. O segundo argumento deve ser `true`.|  
|ou|Retornará true se um dos parâmetros for true. Ambos os argumentos precisam ser boolianos. O seguinte retorna `true`: `or(greater(1,10),equals(0,0))`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: booliano 1<br /><br /> **Descrição**: obrigatório. O primeiro argumento que pode ser `true`.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: booliano 2<br /><br /> **Descrição**: obrigatório. O segundo argumento pode ser `true`.|  
|válido|Retornará true se o parâmetro for `false`. Ambos os argumentos precisam ser boolianos. O seguinte retorna `true`: `not(contains('200 Success','Fail'))`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: booliano<br /><br /> **Descrição**: retornará true se o parâmetro for `false`. Ambos os argumentos precisam ser boolianos. O seguinte retorna `true`: `not(contains('200 Success','Fail'))`|  
|que|Retorna um valor especificado com base em se a expressão fornecida resultar em `true` ou `false`.  Por exemplo, o seguinte retorna `"yes"`: `if(equals(1, 1), 'yes', 'no')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: expressão<br /><br /> **Descrição**: obrigatório. Um valor booliano que determina qual valor é retornado pela expressão.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: verdadeiro<br /><br /> **Descrição**: obrigatório. O valor a ser retornado se a expressão for `true`.<br /><br /> **Número do parâmetro**: 3<br /><br /> **Nome**: falso<br /><br /> **Descrição**: obrigatório. O valor a ser retornado se a expressão for `false`.|  
  
## <a name="conversion-functions"></a>Funções de conversão  
 Essas funções são usadas para converter entre cada um dos tipos nativos no idioma:  
  
-   string  
  
-   número inteiro  
  
-   float  
  
-   boolean  
  
-   Storage  
  
-   OldValues  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|int|Converta o parâmetro em um inteiro. Por exemplo, a expressão a seguir retorna 100 como um número, em vez de uma cadeia de caracteres: `int('100')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: valor<br /><br /> **Descrição**: obrigatório. O valor que é convertido em um número inteiro.|  
|string|Converta o parâmetro em uma cadeia de caracteres. Por exemplo, a expressão a seguir retorna `'10'`: `string(10)` você também pode converter um objeto em uma cadeia de caracteres, por exemplo, se o parâmetro **foo** for um objeto com uma propriedade `bar : baz`, o seguinte retornará `{"bar" : "baz"}` `string(pipeline().parameters.foo)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: valor<br /><br /> **Descrição**: obrigatório. O valor que é convertido em uma cadeia de caracteres.|  
|JSON|Converta o parâmetro em um valor de tipo JSON. É o oposto da cadeia de caracteres (). Por exemplo, a expressão a seguir retorna `[1,2,3]` como uma matriz, em vez de uma cadeia de caracteres:<br /><br /> `json('[1,2,3]')`<br /><br /> Da mesma forma, você pode converter uma cadeia de caracteres em um objeto. Por exemplo, `json('{"bar" : "baz"}')` retorna:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: obrigatório. A cadeia de caracteres que é convertida em um valor de tipo nativo.<br /><br /> A função JSON também dá suporte à entrada XML. Por exemplo, o valor do parâmetro de:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> é convertido para o JSON a seguir:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|Converta o argumento de parâmetro em um número de ponto flutuante. Por exemplo, a expressão a seguir retorna `10.333`: `float('10.333')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: valor<br /><br /> **Descrição**: obrigatório. O valor que é convertido em um número de ponto flutuante.|  
|booleano|Converta o parâmetro em um booliano. Por exemplo, a expressão a seguir retorna `false`: `bool(0)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: valor<br /><br /> **Descrição**: obrigatório. O valor que é convertido em um booliano.|  
|COALESCE|Retorna o primeiro objeto não nulo nos argumentos passados. Observação: uma cadeia de caracteres vazia não é nula. Por exemplo, se os parâmetros 1 e 2 não estiverem definidos, isso retornará `fallback`: `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Número do parâmetro**: 1... *n*<br /><br /> **Nome**: objeto*n*<br /><br /> **Descrição**: obrigatório. Os objetos a serem verificados `null`.|  
|Base64|Retorna a representação Base64 da cadeia de caracteres de entrada. Por exemplo, a expressão a seguir retorna `c29tZSBzdHJpbmc=`: `base64('some string')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres 1<br /><br /> **Descrição**: obrigatório. A cadeia de caracteres a ser codificada na representação base64.|  
|base64ToBinary|Retorna uma representação binária de uma cadeia de caracteres codificada em base64. Por exemplo, a expressão a seguir retorna a representação binária de alguma cadeia de caracteres: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: obrigatório. A cadeia de caracteres codificada em base64.|  
|base64ToString|Retorna uma representação de cadeia de caracteres de uma cadeia de caracteres codificada caracteres com. Por exemplo, a expressão a seguir retorna alguma cadeia de caracteres: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: obrigatório. A cadeia de caracteres codificada em base64.|  
|binário|Retorna uma representação binária de um valor.  Por exemplo, a expressão a seguir retorna uma representação binária de alguma cadeia de caracteres: `binary(‘some string’).`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: valor<br /><br /> **Descrição**: obrigatório. O valor que é convertido em binário.|  
|dataUriToBinary|Retorna uma representação binária de um URI de dados. Por exemplo, a expressão a seguir retorna a representação binária de alguma cadeia de caracteres: `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: obrigatório. O URI de dados a ser convertido em representação binária.|  
|dataUriToString|Retorna uma representação de cadeia de caracteres de um URI de dados. Por exemplo, a expressão a seguir retorna alguma cadeia de caracteres: `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres<br /><br />**Descrição**: obrigatório. O URI de dados a ser convertido em representação de cadeia de caracteres.|  
|dataUri|Retorna um URI de dados de um valor. Por exemplo, a expressão a seguir retorna dados: `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Número do parâmetro**: 1<br /><br />**Nome**: valor<br /><br />**Descrição**: obrigatório. O valor a ser convertido em URI de dados.|  
|decodeBase64|Retorna uma representação de cadeia de caracteres de uma cadeia de caracteres caracteres com de entrada. Por exemplo, a expressão a seguir retorna `some string`: `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: retorna uma representação de cadeia de caracteres de uma cadeia de caracteres caracteres com de entrada.|  
|encodeUriComponent|URL-escapa a cadeia de caracteres que é passada. Por exemplo, a expressão a seguir retorna `You+Are%3ACool%2FAwesome`: `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: obrigatório. A cadeia de caracteres com a qual não é segurada a URL de personagens.|  
|decodeUriComponent|Não URL-sai da cadeia de caracteres que é passada. Por exemplo, a expressão a seguir retorna `You Are:Cool/Awesome`: `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: obrigatório. A cadeia de caracteres a ser decodificada de um caractere não seguro de URL.|  
|decodeDataUri|Retorna uma representação binária de uma cadeia de caracteres de URI de dados de entrada. Por exemplo, a expressão a seguir retorna a representação binária de `some string`: `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: obrigatório. O dataURI a ser decodificado em uma representação binária.|  
|uriComponent|Retorna uma representação codificada de URI de um valor. Por exemplo, a expressão a seguir retorna `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Detalhes do parâmetro: número: 1, nome: cadeia de caracteres, descrição: obrigatório. A cadeia de caracteres a ser codificada em URI.|  
|uriComponentToBinary|Retorna uma representação binária de uma cadeia de caracteres codificada em URI. Por exemplo, a expressão a seguir retorna uma representação binária de `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres<br /><br />**Descrição**: obrigatório. A cadeia de caracteres codificada em URI.|  
|uriComponentToString|Retorna uma representação de cadeia de caracteres de uma cadeia de caracteres codificada em URI. Por exemplo, a expressão a seguir retorna `You Are:Cool/Awesome`: `uriComponentToString('You+Are%3ACool%2FAwesome')`<br /><br /> **Número do parâmetro**: 1<br /><br />**Nome**: cadeia de caracteres<br /><br />**Descrição**: obrigatório. A cadeia de caracteres codificada em URI.|  
|xml|Retornar uma representação XML do valor. Por exemplo, a expressão a seguir retorna um conteúdo XML representado por `'\<name>Alan\</name>'`: `xml('\<name>Alan\</name>')`. A função XML também dá suporte à entrada de objeto JSON. Por exemplo, o parâmetro `{ "abc": "xyz" }` é convertido em um conteúdo XML `\<abc>xyz\</abc>`<br /><br /> **Número do parâmetro**: 1<br /><br />**Nome**: valor<br /><br />**Descrição**: obrigatório. O valor a ser convertido em XML.|  
|XPath|Retornar uma matriz de nós XML que correspondem à expressão XPath de um valor que a expressão XPath avalia.<br /><br />  **Exemplo 1**<br /><br /> Suponha que o valor do parâmetro ' P1 ' seja uma representação de cadeia de caracteres do seguinte XML:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. este código: `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> retornaria<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> invés<br /><br /> 2. este código: `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> retornaria<br /><br /> `13`<br /><br /> <br /><br /> **Exemplo 2**<br /><br /> Dado o seguinte conteúdo XML:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1. este código: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> ou<br /><br /> 2. este código: `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> Apresenta<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> e<br /><br /> 3. este código: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> Apresenta<br /><br /> ``bar``<br /><br /> **Número do parâmetro**: 1<br /><br />**Nome**: XML<br /><br />**Descrição**: obrigatório. O XML no qual avaliar a expressão XPath.<br /><br /> **Número do parâmetro**: 2<br /><br />**Nome**: XPath<br /><br />**Descrição**: obrigatório. A expressão XPath a ser avaliada.|  
|array|Converta o parâmetro em uma matriz.  Por exemplo, a expressão a seguir retorna `["abc"]`: `array('abc')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: valor<br /><br /> **Descrição**: obrigatório. O valor que é convertido em uma matriz.|
|createArray|Cria uma matriz a partir dos parâmetros.  Por exemplo, a expressão a seguir retorna `["a", "c"]`: `createArray('a', 'c')`<br /><br /> **Número do parâmetro**: 1... p<br /><br /> **Nome**: qualquer n<br /><br /> **Descrição**: obrigatório. Os valores a serem combinados em uma matriz.|

## <a name="math-functions"></a>Funções matemáticas  
 Essas funções podem ser usadas para qualquer um dos tipos de números: **inteiros** e **floats**.  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|adicionar|Retorna o resultado da adição dos dois números. Por exemplo, essa função retorna `20.333`: `add(10,10.333)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: soma 1<br /><br /> **Descrição**: obrigatório. O número a ser adicionado ao **soma 2**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: soma 2<br /><br /> **Descrição**: obrigatório. O número a ser adicionado ao **soma 1**.|  
|projeto|Retorna o resultado da subtração dos dois números. Por exemplo, essa função retorna: `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: minuendo<br /><br /> **Descrição**: obrigatório. O número do qual o **subtraendo** é removido.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: subtraendo<br /><br /> **Descrição**: obrigatório. O número a ser removido do **minuendo**.|  
|Mul|Retorna o resultado da multiplicação dos dois números. Por exemplo, o seguinte retorna `103.33`:<br /><br /> `mul(10,10.333)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: multiplicando 1<br /><br /> **Descrição**: obrigatório. O número para multiplicar **multiplicando 2** .<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: multiplicando 2<br /><br /> **Descrição**: obrigatório. O número para multiplicar **multiplicando 1** por.|  
|marca|Retorna o resultado da divisão dos dois números. Por exemplo, o seguinte retorna `1.0333`:<br /><br /> `div(10.333,10)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: dividendo<br /><br /> **Descrição**: obrigatório. O número a ser dividido pelo **divisor**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: divisor<br /><br /> **Descrição**: obrigatório. O número pelo qual dividir o **dividendo** .|  
|resto|Retorna o resultado do resto após a divisão dos dois números (módulo). Por exemplo, a expressão a seguir retorna `2`:<br /><br /> `mod(10,4)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: dividendo<br /><br /> **Descrição**: obrigatório. O número a ser dividido pelo **divisor**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: divisor<br /><br /> **Descrição**: obrigatório. O número pelo qual dividir o **dividendo** . Após a divisão, o restante é tirado.|  
|min.|Há dois padrões diferentes para chamar essa função: `min([0,1,2])` aqui min usa uma matriz. Essa expressão retorna `0`. Como alternativa, essa função pode usar uma lista de valores separados por vírgulas: `min(0,1,2)` essa função também retorna 0. Observe que todos os valores devem ser números, portanto, se o parâmetro for uma matriz, ele terá que conter apenas números.<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: coleção ou valor<br /><br /> **Descrição**: obrigatório. Pode ser uma matriz de valores para localizar o valor mínimo ou o primeiro valor de um conjunto.<br /><br /> **Número do parâmetro**: 2... *n*<br /><br /> **Nome**: valor *n*<br /><br /> **Descrição**: opcional. Se o primeiro parâmetro for um valor, você poderá passar valores adicionais e o mínimo de todos os valores passados será retornado.|  
|maximizar|Há dois padrões diferentes para chamar essa função: `max([0,1,2])`<br /><br /> Aqui Max usa uma matriz. Essa expressão retorna `2`. Como alternativa, essa função pode usar uma lista de valores separados por vírgulas: `max(0,1,2)` essa função retorna 2. Observe que todos os valores devem ser números, portanto, se o parâmetro for uma matriz, ele terá que conter apenas números.<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: coleção ou valor<br /><br /> **Descrição**: obrigatório. Pode ser uma matriz de valores para localizar o valor máximo ou o primeiro valor de um conjunto.<br /><br /> **Número do parâmetro**: 2... *n*<br /><br /> **Nome**: valor *n*<br /><br /> **Descrição**: opcional. Se o primeiro parâmetro for um valor, você poderá passar valores adicionais e o máximo de todos os valores passados será retornado.|  
|amplitude| Gera uma matriz de inteiros a partir de um determinado número e você define o comprimento da matriz retornada. Por exemplo, essa função retorna `[3,4,5,6]`:<br /><br /> `range(3,4)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: índice de início<br /><br /> **Descrição**: obrigatório. É o primeiro inteiro na matriz.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: contagem<br /><br /> **Descrição**: obrigatório. Número de inteiros que estão na matriz.|  
|Rand| Gera um inteiro aleatório dentro do intervalo especificado (inclusive em ambas as extremidades. Por exemplo, isso pode retornar `42`:<br /><br /> `rand(-1000,1000)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: mínimo<br /><br /> **Descrição**: obrigatório. O menor inteiro que pode ser retornado.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: máximo<br /><br /> **Descrição**: obrigatório. O número inteiro mais alto que pode ser retornado.|  
  
## <a name="date-functions"></a>Funções de data  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|UtcNow|Retorna o carimbo de data/hora atual como uma cadeia de caracteres. Por exemplo `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: formato<br /><br /> **Descrição**: opcional. Um [único caractere especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|hipersegundos|Adiciona um número inteiro de segundos a um carimbo de data/hora de cadeia de caracteres passado. O número de segundos pode ser positivo ou negativo. O resultado é uma cadeia de caracteres no formato ISO 8601 ("o") por padrão, a menos que um especificador de formato seja fornecido. Por exemplo `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: carimbo de data/hora<br /><br /> **Descrição**: obrigatório. Uma cadeia de caracteres que contém a hora.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: segundos<br /><br /> **Descrição**: obrigatório. O número de segundos a serem adicionados. Pode ser negativo para subtrair segundos.<br /><br /> **Número do parâmetro**: 3<br /><br /> **Nome**: formato<br /><br /> **Descrição**: opcional. Um [único caractere especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|addminutos|Adiciona um número inteiro de minutos a um carimbo de data/hora de cadeia de caracteres passado. O número de minutos pode ser positivo ou negativo. O resultado é uma cadeia de caracteres no formato ISO 8601 ("o") por padrão, a menos que um especificador de formato seja fornecido. Por exemplo, `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: carimbo de data/hora<br /><br /> **Descrição**: obrigatório. Uma cadeia de caracteres que contém a hora.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: minutos<br /><br /> **Descrição**: obrigatório. O número de minutos a serem adicionados. Pode ser negativo para subtrair minutos.<br /><br /> **Número do parâmetro**: 3<br /><br /> **Nome**: formato<br /><br /> **Descrição**: opcional. Um [único caractere especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|AddHours|Adiciona um número inteiro de horas a um carimbo de data/hora de cadeia de caracteres passado. O número de horas pode ser positivo ou negativo. O resultado é uma cadeia de caracteres no formato ISO 8601 ("o") por padrão, a menos que um especificador de formato seja fornecido. Por exemplo `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: carimbo de data/hora<br /><br /> **Descrição**: obrigatório. Uma cadeia de caracteres que contém a hora.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: horas<br /><br /> **Descrição**: obrigatório. O número de horas a serem adicionadas. Pode ser negativo para subtrair horas.<br /><br /> **Número do parâmetro**: 3<br /><br /> **Nome**: formato<br /><br /> **Descrição**: opcional. Um [único caractere especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|AddDays|Adiciona um número inteiro de dias a um carimbo de data/hora de cadeia de caracteres passado. O número de dias pode ser positivo ou negativo. O resultado é uma cadeia de caracteres no formato ISO 8601 ("o") por padrão, a menos que um especificador de formato seja fornecido. Por exemplo `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: carimbo de data/hora<br /><br /> **Descrição**: obrigatório. Uma cadeia de caracteres que contém a hora.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: dias<br /><br /> **Descrição**: obrigatório. O número de dias a serem adicionados. Pode ser negativo para subtrair dias.<br /><br /> **Número do parâmetro**: 3<br /><br /> **Nome**: formato<br /><br /> **Descrição**: opcional. Um [único caractere especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|formatDateTime|Retorna uma cadeia de caracteres no formato de data. O resultado é uma cadeia de caracteres no formato ISO 8601 ("o") por padrão, a menos que um especificador de formato seja fornecido. Por exemplo `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br />Para formatar uma data em ' AAAA/MM/DD ', use formatDateTime (UtcNow (), ' AAAA/MM/DD ').</br>Para acrescentar um nome à data, use @concat(' foo-', '/', formatDateTime (UtcNow (), ' AAAA/MM/DD ')).<br><br> **Número do parâmetro**: 1<br /><br /> **Nome**: data<br /><br /> **Descrição**: obrigatório. Uma cadeia de caracteres que contém a data.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: formato<br /><br /> **Descrição**: opcional. Um [único caractere especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado. |  

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de variáveis de sistema que você pode usar em expressões, consulte [variáveis do sistema](control-flow-system-variables.md).
