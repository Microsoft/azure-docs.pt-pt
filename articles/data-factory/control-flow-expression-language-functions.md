---
title: Expressão e funções no Azure Data Factory | Microsoft Docs
description: Este artigo fornece informações sobre expressões e funções que você pode usar ao criar data factory entidades.
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
ms.openlocfilehash: 533d0c7461530a00e6f640ee53c0c87d336e799d
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876319"
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
As expressões podem aparecer em qualquer lugar em um valor de cadeia de caracteres JSON e sempre resultar em outro valor JSON. Se um valor JSON for uma expressão, o corpo da expressão será extraído removendo o sinal de arroba (\@). Se for necessária uma cadeia de caracteres literal que \@começa com, ela deve ser substituída usando. \@ \@ Os exemplos a seguir mostram como as expressões são avaliadas.  
  
|Valor JSON|Resultado|  
|----------------|------------|  
|parâmetro|Os caracteres ' Parameters ' são retornados.|  
|"parâmetros [1]"|Os caracteres ' parameters [1] ' são retornados.|  
|"\@\@"|Uma cadeia de caracteres de 1 caractere\@que contém ' ' é retornada.|  
|" \@"|Uma cadeia de caracteres de 2 caracteres \@que contém ' ' é retornada.|  
  
 As expressões também podem aparecer dentro de cadeias de caracteres, usando um recurso chamado interpolação `@{ ... }`de *cadeia de caracteres* em que as expressões são encapsuladas. Por exemplo: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Usando a interpolação de cadeia de caracteres, o resultado é sempre uma cadeia de caracteres. Digamos que defini `myNumber` como `42` e `myString` como: `foo`  
  
|Valor JSON|Resultado|  
|----------------|------------|  
|"\@pipeline (). Parameters. MyString"| Retorna `foo` como uma cadeia de caracteres.|  
|"\@{pipeline (). Parameters. MyString}"| Retorna `foo` como uma cadeia de caracteres.|  
|"\@pipeline().parameters.myNumber"| Retorna `42` como um *número*.|  
|"\@{pipeline().parameters.myNumber}"| Retorna `42` como uma *cadeia de caracteres*.|  
|"A resposta é: @ {pipeline (). Parameters. MyNumber}"| Retorna a cadeia `Answer is: 42`de caracteres.|  
|"\@Concat (' a resposta é: ', String (pipeline (). Parameters. MyNumber))"| Retorna a cadeia de caracteres`Answer is: 42`|  
|"A resposta é \@: \@{pipeline (). Parameters. MyNumber}"| Retorna a cadeia `Answer is: @{pipeline().parameters.myNumber}`de caracteres.|  
  
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

  
## <a name="functions"></a>Functions  
 Você pode chamar funções dentro de expressões. As seções a seguir fornecem informações sobre as funções que podem ser usadas em uma expressão.  

## <a name="string-functions"></a>Funções de cadeia  
 As funções a seguir se aplicam somente a cadeias de caracteres. Você também pode usar um número de funções de coleção em cadeias de caracteres.  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|Concat|Combina qualquer número de cadeias de caracteres juntas. Por exemplo, se parâmetro1 for `foo,` a seguinte expressão retornaria `somevalue-foo-somevalue`:`concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Número do parâmetro**: 1... *n*<br /><br /> **Nome**: Cadeia de caracteres *n*<br /><br /> **Descrição**: Necessário. As cadeias de caracteres a serem combinadas em uma única cadeia.|  
|Subcadeia|Retorna um subconjunto de caracteres de uma cadeia. Por exemplo, a expressão a seguir:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> apresenta<br /><br /> `foo`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia<br /><br /> **Descrição**: Necessário. A cadeia de caracteres da qual a subcadeia de caracteres é tirada.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Índice de início<br /><br /> **Descrição**: Necessário. O índice de onde a subcadeia de caracteres começa no parâmetro 1. O índice de início é baseado em zero. <br /><br /> **Número do parâmetro**: 3<br /><br /> **Nome**: Comprimento<br /><br /> **Descrição**: Necessário. O comprimento da subcadeia de caracteres.|  
|Substitua|Substitui uma cadeia de caracteres por uma determinada cadeia de caracteres. Por exemplo, a expressão:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> apresenta<br /><br /> `the new string`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: Necessário.  Se o parâmetro 2 for encontrado no parâmetro 1, a cadeia de caracteres pesquisada para o parâmetro 2 e atualizada com o parâmetro 3.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Cadeia de caracteres antiga<br /><br /> **Descrição**: Necessário. A cadeia de caracteres a ser substituída pelo parâmetro 3 quando uma correspondência for encontrada no parâmetro 1<br /><br /> **Número do parâmetro**: 3<br /><br /> **Nome**: Nova cadeia de caracteres<br /><br /> **Descrição**: Necessário. A cadeia de caracteres usada para substituir a cadeia de caracteres no parâmetro 2 quando uma correspondência é encontrada no parâmetro 1.|  
|volume| Gera uma cadeia de caracteres globalmente exclusiva (também conhecida como. GUID). Por exemplo, a seguinte saída poderia ser gerada `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Formato<br /><br /> **Descrição**: Opcional. Um único especificador de formato que indica [como formatar o valor desse GUID](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). O parâmetro de formato pode ser "N", "D", "B", "P" ou "X". Se o formato não for fornecido, "D" será usado.|  
|toLower|Converte uma cadeia de caracteres em minúsculas. Por exemplo, o seguinte retorna `two by two is four`:`toLower('Two by Two is Four')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia<br /><br /> **Descrição**: Necessário. A cadeia de caracteres a ser convertida em minúsculas. Se um caractere na cadeia de caracteres não tiver um equivalente em minúsculas, ele será incluído inalterado na cadeia de caracteres retornada.|  
|toUpper|Converte uma cadeia de caracteres em maiúsculas. Por exemplo, a expressão a seguir `TWO BY TWO IS FOUR`retorna:`toUpper('Two by Two is Four')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia<br /><br /> **Descrição**: Necessário. A cadeia de caracteres a ser convertida em letras maiúsculas. Se um caractere na cadeia de caracteres não tiver um equivalente em maiúsculas, ele será incluído inalterado na cadeia de caracteres retornada.|  
|IndexOf|Localize o índice de um valor dentro de um caso de cadeia de caracteres sem distinção. O índice é baseado em zero. Por exemplo, a expressão a seguir `7`retorna:`indexof('hello, world.', 'world')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia<br /><br /> **Descrição**: Necessário. A cadeia de caracteres que pode conter o valor.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Cadeia<br /><br /> **Descrição**: Necessário. O valor para pesquisar o índice.|  
|lastindexof|Localize o último índice de um valor dentro de um caso de cadeia de caracteres sem distinção. O índice é baseado em zero. Por exemplo, a expressão a seguir `3`retorna:`lastindexof('foofoo', 'foo')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia<br /><br /> **Descrição**: Necessário. A cadeia de caracteres que pode conter o valor.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Cadeia<br /><br /> **Descrição**: Necessário. O valor para pesquisar o índice.|  
|StartsWith|Verifica se a cadeia de caracteres começa com um valor de maiúsculas e minúsculas. Por exemplo, a expressão a seguir `true`retorna:`startswith('hello, world', 'hello')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia<br /><br /> **Descrição**: Necessário. A cadeia de caracteres que pode conter o valor.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Cadeia<br /><br /> **Descrição**: Necessário. O valor com o qual a cadeia de caracteres pode começar.|  
|EndsWith|Verifica se a cadeia de caracteres termina com um valor de maiúsculas e minúsculas. Por exemplo, a expressão a seguir `true`retorna:`endswith('hello, world', 'world')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia<br /><br /> **Descrição**: Necessário. A cadeia de caracteres que pode conter o valor.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Cadeia<br /><br /> **Descrição**: Necessário. O valor com o qual a cadeia de caracteres pode terminar.|  
|split|Divide a cadeia de caracteres usando um separador. Por exemplo, a expressão a seguir `["a", "b", "c"]`retorna:`split('a;b;c',';')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia<br /><br /> **Descrição**: Necessário. A cadeia de caracteres dividida.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Cadeia<br /><br /> **Descrição**: Necessário. O separador.|  
  
  
## <a name="collection-functions"></a>Funções de coleção  
 Essas funções operam em coleções como matrizes, cadeias de caracteres e, às vezes, dicionários.  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|Contém|Retorna true se o dicionário contém uma chave, a lista contém um valor ou uma cadeia de caracteres contém subcadeia de caracteres. Por exemplo, a expressão a seguir retorna`true:``contains('abacaba','aca')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Dentro da coleção<br /><br /> **Descrição**: Necessário. A coleção na qual Pesquisar.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Localizar objeto<br /><br /> **Descrição**: Necessário. O objeto a ser localizado dentro da **coleção dentro**.|  
|length|Retorna o número de elementos em uma matriz ou cadeia de caracteres. Por exemplo, a expressão a seguir `3`retorna:`length('abc')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Collection<br /><br /> **Descrição**: Necessário. A coleção para obter o comprimento de.|  
|vazio|Retornará true se o objeto, a matriz ou a cadeia de caracteres estiver vazia. Por exemplo, a expressão a seguir `true`retorna:<br /><br /> `empty('')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Collection<br /><br /> **Descrição**: Necessário. A coleção para verificar se ela está vazia.|  
|interseção|Retorna uma única matriz ou objeto com os elementos comuns entre as matrizes ou os objetos passados a ele. Por exemplo, essa função retorna `[1, 2]`:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> Os parâmetros para a função podem ser um conjunto de objetos ou um conjunto de matrizes (não uma combinação deles). Se houver dois objetos com o mesmo nome, o último objeto com esse nome aparecerá no objeto final.<br /><br /> **Número do parâmetro**: 1... *n*<br /><br /> **Nome**: Coleção *n*<br /><br /> **Descrição**: Necessário. As coleções a serem avaliadas. Um objeto deve estar em todas as coleções passadas para aparecer no resultado.|  
|unida|Retorna uma única matriz ou objeto com todos os elementos que estão em uma matriz ou objeto passado para ele. Por exemplo, essa função retorna`[1, 2, 3, 10, 101]:`<br /><br /> :  `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> Os parâmetros para a função podem ser um conjunto de objetos ou um conjunto de matrizes (não uma combinação deles). Se houver dois objetos com o mesmo nome na saída final, o último objeto com esse nome aparecerá no objeto final.<br /><br /> **Número do parâmetro**: 1... *n*<br /><br /> **Nome**: Coleção *n*<br /><br /> **Descrição**: Necessário. As coleções a serem avaliadas. Um objeto que aparece em qualquer uma das coleções aparece no resultado.|  
|primeiro|Retorna o primeiro elemento na matriz ou na cadeia de caracteres passada. Por exemplo, essa função retorna `0`:<br /><br /> `first([0,2,3])`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Collection<br /><br /> **Descrição**: Necessário. A coleção da qual obter o primeiro objeto.|  
|última|Retorna o último elemento na matriz ou na cadeia de caracteres passada. Por exemplo, essa função retorna `3`:<br /><br /> `last('0123')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Collection<br /><br /> **Descrição**: Necessário. A coleção da qual o último objeto será levado.|  
|ter|Retorna os primeiros elementos de **contagem** da matriz ou da cadeia de caracteres passada, por exemplo, `[1, 2]`essa função retorna:`take([1, 2, 3, 4], 2)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Collection<br /><br /> **Descrição**: Necessário. A coleção da qual obter os primeiros objetos de **contagem** .<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Count<br /><br /> **Descrição**: Necessário. O número de objetos a serem tirados da **coleção**. Deve ser um inteiro positivo.|  
|ignorar|Retorna os elementos na matriz começando na **contagem**de índice, por exemplo, essa função `[3, 4]`retorna:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Collection<br /><br /> **Descrição**: Necessário. A coleção da qual ignorar os primeiros objetos de **contagem** .<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Count<br /><br /> **Descrição**: Necessário. O número de objetos a serem removidos da frente da **coleção**. Deve ser um inteiro positivo.|  
  
## <a name="logical-functions"></a>Funções lógicas  
 Essas funções são úteis dentro das condições, elas podem ser usadas para avaliar qualquer tipo de lógica.  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|é igual a|Retornará true se dois valores forem iguais. Por exemplo, se parâmetro1 for foo, a expressão a seguir retornará `true`:`equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Objeto 1<br /><br /> **Descrição**: Necessário. O objeto a ser comparado com o **objeto 2**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Objeto 2<br /><br /> **Descrição**: Necessário. O objeto a ser comparado com o **objeto 1**.|  
|inferiores|Retornará true se o primeiro argumento for menor que o segundo. Observe que os valores só podem ser do tipo inteiro, flutuante ou cadeia de caracteres. Por exemplo, a expressão a seguir `true`retorna:`less(10,100)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Objeto 1<br /><br /> **Descrição**: Necessário. O objeto para verificar se ele é menor que o **objeto 2**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Objeto 2<br /><br /> **Descrição**: Necessário. O objeto para verificar se ele é maior que o **objeto 1**.|  
|lessOrEquals|Retornará true se o primeiro argumento for menor ou igual ao segundo. Observe que os valores só podem ser do tipo inteiro, flutuante ou cadeia de caracteres. Por exemplo, a expressão a seguir `true`retorna:`lessOrEquals(10,10)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Objeto 1<br /><br /> **Descrição**: Necessário. O objeto para verificar se ele é menor ou igual ao **objeto 2**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Objeto 2<br /><br /> **Descrição**: Necessário. O objeto para verificar se ele é maior ou igual ao **objeto 1**.|  
|grande|Retornará true se o primeiro argumento for maior que o segundo. Observe que os valores só podem ser do tipo inteiro, flutuante ou cadeia de caracteres. Por exemplo, a expressão a seguir `false`retorna:`greater(10,10)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Objeto 1<br /><br /> **Descrição**: Necessário. O objeto para verificar se ele é maior que o **objeto 2**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Objeto 2<br /><br /> **Descrição**: Necessário. O objeto para verificar se ele é menor que o **objeto 1**.|  
|greaterOrEquals|Retornará true se o primeiro argumento for maior ou igual ao segundo. Observe que os valores só podem ser do tipo inteiro, flutuante ou cadeia de caracteres. Por exemplo, a expressão a seguir `false`retorna:`greaterOrEquals(10,100)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Objeto 1<br /><br /> **Descrição**: Necessário. O objeto para verificar se ele é maior ou igual ao **objeto 2**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Objeto 2<br /><br /> **Descrição**: Necessário. O objeto para verificar se ele é menor ou igual ao **objeto 1**.|  
|e|Retornará true se ambos os parâmetros forem verdadeiros. Ambos os argumentos precisam ser boolianos. O seguinte retorna `false`:`and(greater(1,10),equals(0,0))`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Booliano 1<br /><br /> **Descrição**: Necessário. O primeiro argumento que deve ser `true`.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Booliano 2<br /><br /> **Descrição**: Necessário. O segundo argumento deve ser `true`.|  
|ou|Retornará true se um dos parâmetros for true. Ambos os argumentos precisam ser boolianos. O seguinte retorna `true`:`or(greater(1,10),equals(0,0))`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Booliano 1<br /><br /> **Descrição**: Necessário. O primeiro argumento que pode ser `true`.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Booliano 2<br /><br /> **Descrição**: Necessário. O segundo argumento pode ser `true`.|  
|não|Retornará true se o parâmetro for `false`. Ambos os argumentos precisam ser boolianos. O seguinte retorna `true`:`not(contains('200 Success','Fail'))`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Booleano<br /><br /> **Descrição**: Retornará true se o parâmetro for `false`. Ambos os argumentos precisam ser boolianos. O seguinte retorna `true`:`not(contains('200 Success','Fail'))`|  
|que|Retorna um valor especificado com base em se a expressão fornecida resulta `true` em `false`ou.  Por exemplo, o seguinte retorna `"yes"`:`if(equals(1, 1), 'yes', 'no')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Expressão<br /><br /> **Descrição**: Necessário. Um valor booliano que determina qual valor é retornado pela expressão.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: True<br /><br /> **Descrição**: Necessário. O valor a ser retornado se a expressão `true`for.<br /><br /> **Número do parâmetro**: 3<br /><br /> **Nome**: False<br /><br /> **Descrição**: Necessário. O valor a ser retornado se a expressão `false`for.|  
  
## <a name="conversion-functions"></a>Funções de conversão  
 Essas funções são usadas para converter entre cada um dos tipos nativos no idioma:  
  
-   Cadeia de caracteres  
  
-   integer  
  
-   float  
  
-   boolean  
  
-   Storage  
  
-   OldValues  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|int|Converta o parâmetro em um inteiro. Por exemplo, a expressão a seguir retorna 100 como um número, em vez de uma cadeia de caracteres:`int('100')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrição**: Necessário. O valor que é convertido em um número inteiro.|  
|Cadeia de caracteres|Converta o parâmetro em uma cadeia de caracteres. Por exemplo, a expressão a seguir `'10'`retorna:  `string(10)`Você também pode converter um objeto em uma cadeia de caracteres, por exemplo, se o parâmetro **foo** for um objeto `bar : baz`com uma propriedade, o seguinte `{"bar" : "baz"}` retornará`string(pipeline().parameters.foo)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrição**: Necessário. O valor que é convertido em uma cadeia de caracteres.|  
|json|Converta o parâmetro em um valor de tipo JSON. É o oposto da cadeia de caracteres (). Por exemplo, a expressão a seguir `[1,2,3]` retorna como uma matriz, em vez de uma cadeia de caracteres:<br /><br /> `json('[1,2,3]')`<br /><br /> Da mesma forma, você pode converter uma cadeia de caracteres em um objeto. Por exemplo, `json('{"bar" : "baz"}')` retorna:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia<br /><br /> **Descrição**: Necessário. A cadeia de caracteres que é convertida em um valor de tipo nativo.<br /><br /> A função JSON também dá suporte à entrada XML. Por exemplo, o valor do parâmetro de:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> é convertido para o JSON a seguir:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|Converta o argumento de parâmetro em um número de ponto flutuante. Por exemplo, a expressão a seguir `10.333`retorna:`float('10.333')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrição**: Necessário. O valor que é convertido em um número de ponto flutuante.|  
|bool|Converta o parâmetro em um booliano. Por exemplo, a expressão a seguir `false`retorna:`bool(0)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrição**: Necessário. O valor que é convertido em um booliano.|  
|COALESCE|Retorna o primeiro objeto não nulo nos argumentos passados. Observação: uma cadeia de caracteres vazia não é nula. Por exemplo, se os parâmetros 1 e 2 não estiverem definidos, isso `fallback`retornará:`coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Número do parâmetro**: 1... *n*<br /><br /> **Nome**: Objeto*n*<br /><br /> **Descrição**: Necessário. Os objetos a serem verificados `null`.|  
|base64|Retorna a representação Base64 da cadeia de caracteres de entrada. Por exemplo, a expressão a seguir `c29tZSBzdHJpbmc=`retorna:`base64('some string')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia de caracteres 1<br /><br /> **Descrição**: Necessário. A cadeia de caracteres a ser codificada na representação base64.|  
|base64ToBinary|Retorna uma representação binária de uma cadeia de caracteres codificada em base64. Por exemplo, a expressão a seguir retorna a representação binária de alguma cadeia `base64ToBinary('c29tZSBzdHJpbmc=')`de caracteres:.<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia<br /><br /> **Descrição**: Necessário. A cadeia de caracteres codificada em base64.|  
|base64ToString|Retorna uma representação de cadeia de caracteres de uma cadeia de caracteres codificada caracteres com. Por exemplo, a expressão a seguir retorna alguma cadeia `base64ToString('c29tZSBzdHJpbmc=')`de caracteres:.<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia<br /><br /> **Descrição**: Necessário. A cadeia de caracteres codificada em base64.|  
|Binary|Retorna uma representação binária de um valor.  Por exemplo, a expressão a seguir retorna uma representação binária de alguma cadeia de caracteres:`binary(‘some string’).`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrição**: Necessário. O valor que é convertido em binário.|  
|dataUriToBinary|Retorna uma representação binária de um URI de dados. Por exemplo, a expressão a seguir retorna a representação binária de alguma cadeia de caracteres:`dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia<br /><br /> **Descrição**: Necessário. O URI de dados a ser convertido em representação binária.|  
|dataUriToString|Retorna uma representação de cadeia de caracteres de um URI de dados. Por exemplo, a expressão a seguir retorna alguma cadeia de caracteres:`dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia<br /><br />**Descrição**: Necessário. O URI de dados a ser convertido em representação de cadeia de caracteres.|  
|dataUri|Retorna um URI de dados de um valor. Por exemplo, a expressão a seguir retorna dados:`text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Número do parâmetro**: 1<br /><br />**Nome**: Value<br /><br />**Descrição**: Necessário. O valor a ser convertido em URI de dados.|  
|decodeBase64|Retorna uma representação de cadeia de caracteres de uma cadeia de caracteres caracteres com de entrada. Por exemplo, a expressão a seguir `some string`retorna:`decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia<br /><br /> **Descrição**: Retorna uma representação de cadeia de caracteres de uma cadeia de caracteres caracteres com de entrada.|  
|encodeUriComponent|URL-escapa a cadeia de caracteres que é passada. Por exemplo, a expressão a seguir `You+Are%3ACool%2FAwesome`retorna:`encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia<br /><br /> **Descrição**: Necessário. A cadeia de caracteres com a qual não é segurada a URL de personagens.|  
|decodeUriComponent|Não URL-sai da cadeia de caracteres que é passada. Por exemplo, a expressão a seguir `You Are:Cool/Awesome`retorna:`encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia<br /><br /> **Descrição**: Necessário. A cadeia de caracteres a ser decodificada de um caractere não seguro de URL.|  
|decodeDataUri|Retorna uma representação binária de uma cadeia de caracteres de URI de dados de entrada. Por exemplo, a expressão a seguir retorna a representação binária `some string`de:`decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia<br /><br /> **Descrição**: Necessário. O dataURI a ser decodificado em uma representação binária.|  
|uriComponent|Retorna uma representação codificada de URI de um valor. Por exemplo, a expressão a seguir retorna`You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Detalhes do parâmetro: Automática 1, nome: Cadeia de caracteres, descrição: Necessário. A cadeia de caracteres a ser codificada em URI.|  
|uriComponentToBinary|Retorna uma representação binária de uma cadeia de caracteres codificada em URI. Por exemplo, a expressão a seguir retorna uma representação binária `You Are:Cool/Awesome`de:`uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia<br /><br />**Descrição**: Necessário. A cadeia de caracteres codificada em URI.|  
|uriComponentToString|Retorna uma representação de cadeia de caracteres de uma cadeia de caracteres codificada em URI. Por exemplo, a expressão a seguir `You Are:Cool/Awesome`retorna:`uriComponentToString('You+Are%3ACool%2FAwesome')`<br /><br /> **Número do parâmetro**: 1<br /><br />**Nome**: Cadeia<br /><br />**Descrição**: Necessário. A cadeia de caracteres codificada em URI.|  
|xml|Retornar uma representação XML do valor. Por exemplo, a expressão a seguir retorna um conteúdo XML representado `'\<name>Alan\</name>'`por `xml('\<name>Alan\</name>')`:. A função XML também dá suporte à entrada de objeto JSON. Por exemplo, o parâmetro `{ "abc": "xyz" }` é convertido em um conteúdo XML`\<abc>xyz\</abc>`<br /><br /> **Número do parâmetro**: 1<br /><br />**Nome**: Value<br /><br />**Descrição**: Necessário. O valor a ser convertido em XML.|  
|xpath|Retornar uma matriz de nós XML que correspondem à expressão XPath de um valor que a expressão XPath avalia.<br /><br />  **Exemplo 1**<br /><br /> Suponha que o valor do parâmetro ' P1 ' seja uma representação de cadeia de caracteres do seguinte XML:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. Este código:`xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> retornaria<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> invés<br /><br /> 2. Este código:`xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> retornaria<br /><br /> `13`<br /><br /> <br /><br /> **Exemplo 2**<br /><br /> Dado o seguinte conteúdo XML:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1.  Este código:`@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> ou<br /><br /> 2. Este código:`@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> apresenta<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> e<br /><br /> 3. Este código:`@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> apresenta<br /><br /> ``bar``<br /><br /> **Número do parâmetro**: 1<br /><br />**Nome**: Xml<br /><br />**Descrição**: Necessário. O XML no qual avaliar a expressão XPath.<br /><br /> **Número do parâmetro**: 2<br /><br />**Nome**: XPath<br /><br />**Descrição**: Necessário. A expressão XPath a ser avaliada.|  
|array|Converta o parâmetro em uma matriz.  Por exemplo, a expressão a seguir `["abc"]`retorna:`array('abc')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrição**: Necessário. O valor que é convertido em uma matriz.|
|createArray|Cria uma matriz a partir dos parâmetros.  Por exemplo, a expressão a seguir `["a", "c"]`retorna:`createArray('a', 'c')`<br /><br /> **Número do parâmetro**: 1... p<br /><br /> **Nome**: Qualquer n<br /><br /> **Descrição**: Necessário. Os valores a serem combinados em uma matriz.|

## <a name="math-functions"></a>Funções matemáticas  
 Essas funções podem ser usadas para qualquer um dos tipos de números: **inteiros** e **floats**.  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|adicionar|Retorna o resultado da adição dos dois números. Por exemplo, essa função retorna `20.333`:`add(10,10.333)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Soma 1<br /><br /> **Descrição**: Necessário. O número a ser adicionado ao **soma 2**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Soma 2<br /><br /> **Descrição**: Necessário. O número a ser adicionado ao **soma 1**.|  
|projeto|Retorna o resultado da subtração dos dois números. Por exemplo, essa função retorna: `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Minuendo<br /><br /> **Descrição**: Necessário. O número do qual o **subtraendo** é removido.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Subtraendo<br /><br /> **Descrição**: Necessário. O número a ser removido do **minuendo**.|  
|Mul|Retorna o resultado da multiplicação dos dois números. Por exemplo, o seguinte retorna `103.33`:<br /><br /> `mul(10,10.333)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Multiplicando 1<br /><br /> **Descrição**: Necessário. O número para multiplicar **multiplicando 2** .<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Multiplicando 2<br /><br /> **Descrição**: Necessário. O número para multiplicar **multiplicando 1** por.|  
|div|Retorna o resultado da divisão dos dois números. Por exemplo, o seguinte retorna `1.0333`:<br /><br /> `div(10.333,10)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cheque<br /><br /> **Descrição**: Necessário. O número a ser dividido pelo **divisor**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Divisor<br /><br /> **Descrição**: Necessário. O número pelo qual dividir o dividendo.|  
|resto|Retorna o resultado do resto após a divisão dos dois números (módulo). Por exemplo, a expressão a seguir `2`retorna:<br /><br /> `mod(10,4)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cheque<br /><br /> **Descrição**: Necessário. O número a ser dividido pelo **divisor**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Divisor<br /><br /> **Descrição**: Necessário. O número pelo qual dividir o dividendo. Após a divisão, o restante é tirado.|  
|mín.|Há dois padrões diferentes para chamar essa função: `min([0,1,2])`Aqui, min usa uma matriz. Essa expressão retorna `0`. Como alternativa, essa função pode usar uma lista de valores separados por vírgulas:  `min(0,1,2)`Essa função também retorna 0. Observe que todos os valores devem ser números, portanto, se o parâmetro for uma matriz, ele terá que conter apenas números.<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Coleção ou valor<br /><br /> **Descrição**: Necessário. Pode ser uma matriz de valores para localizar o valor mínimo ou o primeiro valor de um conjunto.<br /><br /> **Número do parâmetro**: 2... *n*<br /><br /> **Nome**: Valor *n*<br /><br /> **Descrição**: Opcional. Se o primeiro parâmetro for um valor, você poderá passar valores adicionais e o mínimo de todos os valores passados será retornado.|  
|máx.|Há dois padrões diferentes para chamar essa função:`max([0,1,2])`<br /><br /> Aqui Max usa uma matriz. Essa expressão retorna `2`. Como alternativa, essa função pode usar uma lista de valores separados por vírgulas:  `max(0,1,2)`Essa função retorna 2. Observe que todos os valores devem ser números, portanto, se o parâmetro for uma matriz, ele terá que conter apenas números.<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Coleção ou valor<br /><br /> **Descrição**: Necessário. Pode ser uma matriz de valores para localizar o valor máximo ou o primeiro valor de um conjunto.<br /><br /> **Número do parâmetro**: 2... *n*<br /><br /> **Nome**: Valor *n*<br /><br /> **Descrição**: Opcional. Se o primeiro parâmetro for um valor, você poderá passar valores adicionais e o máximo de todos os valores passados será retornado.|  
|amplitude| Gera uma matriz de inteiros a partir de um determinado número e você define o comprimento da matriz retornada. Por exemplo, essa função retorna `[3,4,5,6]`:<br /><br /> `range(3,4)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Índice de início<br /><br /> **Descrição**: Necessário. É o primeiro inteiro na matriz.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Count<br /><br /> **Descrição**: Necessário. Número de inteiros que estão na matriz.|  
|Rand| Gera um inteiro aleatório dentro do intervalo especificado (inclusive em ambas as extremidades. Por exemplo, isso poderia retornar `42`:<br /><br /> `rand(-1000,1000)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Mínimo<br /><br /> **Descrição**: Necessário. O menor inteiro que pode ser retornado.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Máximo<br /><br /> **Descrição**: Necessário. O número inteiro mais alto que pode ser retornado.|  
  
## <a name="date-functions"></a>Funções de data  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|UtcNow|Retorna o carimbo de data/hora atual como uma cadeia de caracteres. Por exemplo `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Formato<br /><br /> **Descrição**: Opcional. Um [único caractere especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um padrão de [formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|hipersegundos|Adiciona um número inteiro de segundos a um carimbo de data/hora de cadeia de caracteres passado. O número de segundos pode ser positivo ou negativo. O resultado é uma cadeia de caracteres no formato ISO 8601 ("o") por padrão, a menos que um especificador de formato seja fornecido. Por exemplo `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrição**: Necessário. Uma cadeia de caracteres que contém a hora.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Segundos<br /><br /> **Descrição**: Necessário. O número de segundos a serem adicionados. Pode ser negativo para subtrair segundos.<br /><br /> **Número do parâmetro**: 3<br /><br /> **Nome**: Formato<br /><br /> **Descrição**: Opcional. Um [único caractere especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um padrão de [formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|addminutos|Adiciona um número inteiro de minutos a um carimbo de data/hora de cadeia de caracteres passado. O número de minutos pode ser positivo ou negativo. O resultado é uma cadeia de caracteres no formato ISO 8601 ("o") por padrão, a menos que um especificador de formato seja fornecido. Por exemplo, `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrição**: Necessário. Uma cadeia de caracteres que contém a hora.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Minutos<br /><br /> **Descrição**: Necessário. O número de minutos a serem adicionados. Pode ser negativo para subtrair minutos.<br /><br /> **Número do parâmetro**: 3<br /><br /> **Nome**: Formato<br /><br /> **Descrição**: Opcional. Um [único caractere especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um padrão de [formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|AddHours|Adiciona um número inteiro de horas a um carimbo de data/hora de cadeia de caracteres passado. O número de horas pode ser positivo ou negativo. O resultado é uma cadeia de caracteres no formato ISO 8601 ("o") por padrão, a menos que um especificador de formato seja fornecido. Por exemplo `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrição**: Necessário. Uma cadeia de caracteres que contém a hora.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Horas<br /><br /> **Descrição**: Necessário. O número de horas a serem adicionadas. Pode ser negativo para subtrair horas.<br /><br /> **Número do parâmetro**: 3<br /><br /> **Nome**: Formato<br /><br /> **Descrição**: Opcional. Um [único caractere especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um padrão de [formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|AddDays|Adiciona um número inteiro de dias a um carimbo de data/hora de cadeia de caracteres passado. O número de dias pode ser positivo ou negativo. O resultado é uma cadeia de caracteres no formato ISO 8601 ("o") por padrão, a menos que um especificador de formato seja fornecido. Por exemplo `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrição**: Necessário. Uma cadeia de caracteres que contém a hora.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Dias<br /><br /> **Descrição**: Necessário. O número de dias a serem adicionados. Pode ser negativo para subtrair dias.<br /><br /> **Número do parâmetro**: 3<br /><br /> **Nome**: Formato<br /><br /> **Descrição**: Opcional. Um [único caractere especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um padrão de [formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|formatDateTime|Retorna uma cadeia de caracteres no formato de data. O resultado é uma cadeia de caracteres no formato ISO 8601 ("o") por padrão, a menos que um especificador de formato seja fornecido. Por exemplo `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Date<br /><br /> **Descrição**: Necessário. Uma cadeia de caracteres que contém a data.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Formato<br /><br /> **Descrição**: Opcional. Um [único caractere especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um padrão de [formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista de variáveis de sistema que você pode usar em expressões, consulte [variáveis do sistema](control-flow-system-variables.md).
