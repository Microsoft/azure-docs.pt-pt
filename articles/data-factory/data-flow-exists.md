---
title: Existe transformação no fluxo de dados de mapeamento Azure Data Factory
description: Verificar as linhas existentes usando a transformação Exists no fluxo de dados de mapeamento Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: a477eba246c4ebcfbd32e92f1fd30c301ea1cc5b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676736"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Existe transformação no fluxo de dados de mapeamento

A transformação Exists é uma transformação de filtragem de linha que verifica se os dados existem em outra fonte ou fluxo. O fluxo de saída inclui todas as linhas no fluxo à esquerda que existem ou não existem no fluxo correto. A transformação Exists é semelhante a ```SQL WHERE EXISTS``` e ```SQL WHERE NOT EXISTS```.

## <a name="configuration"></a>Configuração

1. Escolha qual fluxo de dados você está verificando a existência no menu suspenso de **fluxo à direita** .
1. Especifique se você está procurando os dados existentes ou não existentes na configuração de **tipo exist** .
1. Selecione se deseja ou não uma **expressão personalizada**.
1. Escolha quais colunas de chave você deseja comparar como suas condições de existência. Por padrão, o fluxo de dados procura igualdade entre uma coluna em cada fluxo. Para comparar por meio de um valor calculado, passe o mouse sobre a lista suspensa coluna e selecione **coluna computada**.

![Configurações existentes](media/data-flow/exists.png "existe 1")

### <a name="multiple-exists-conditions"></a>Várias condições existentes

Para comparar várias colunas de cada fluxo, adicione uma nova condição EXISTS clicando no ícone de adição ao lado de uma linha existente. Cada condição adicional é unida por uma instrução "and". Comparar duas colunas é o mesmo que a seguinte expressão:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Expressão personalizada

Para criar uma expressão de forma livre que contenha operadores diferentes de "and" e "Equals to", selecione o campo **expressão personalizada** . Insira uma expressão personalizada por meio do construtor de expressões de fluxo de dados clicando na caixa azul.

![Existe configurações personalizadas](media/data-flow/exists1.png "existe personalizado")

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: {'none' | 'left' | 'right' | 'both'}
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Exemplo

O exemplo abaixo é uma transformação Exists chamada `checkForChanges` que usa o fluxo à esquerda `NameNorm2` e o `TypeConversions`de fluxo à direita.  A condição EXISTS é a `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` de expressão que retorna true se as colunas `EMPID` e `Region` em cada fluxo correspondem. Como estamos verificando a existência, `negate` é false. Não habilitamos nenhuma difusão na guia otimizar para que `broadcast` tenha valor `'none'`.

No Data Factory UX, essa transformação é semelhante à imagem abaixo:

![Exemplo de Exists](media/data-flow/exists-script.png "Exemplo de Exists")

O script de fluxo de dados para essa transformação está no trecho de código abaixo:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Passos seguintes

As transformações semelhantes são [Lookup](data-flow-lookup.md) e [Join](data-flow-join.md).
