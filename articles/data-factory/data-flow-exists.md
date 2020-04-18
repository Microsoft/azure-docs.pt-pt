---
title: Existe transformação no fluxo de dados de mapeamento
description: Verifique se existem linhas existentes utilizando a transformação existente no fluxo de dados de mapeamento da Fábrica de Dados da Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: a303c8fa1e23460fb906232eedb6bfb1930b4bc9
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606474"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Existe transformação no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A transformação existente é uma transformação de filtragem de linha que verifica se os seus dados existem em outra fonte ou fluxo. O fluxo de saída inclui todas as linhas no fluxo esquerdo que existem ou não existem no fluxo certo. A transformação existente ```SQL WHERE EXISTS``` ```SQL WHERE NOT EXISTS```é semelhante a e.

## <a name="configuration"></a>Configuração

1. Escolha qual o fluxo de dados que está a verificar para a existência no **fluxo certo.**
1. Especifique se está à procura de que os dados existam ou não na definição do **tipo Exista.**
1. Selecione se quer ou não uma **expressão personalizada.**
1. Escolha quais as colunas-chave que pretende comparar como as suas condições existentes. Por padrão, o fluxo de dados procura a igualdade entre uma coluna em cada fluxo. Para comparar através de um valor computado, paire sobre a coluna dropdown e **selecione coluna Computed**.

![Existe definições](media/data-flow/exists.png "existe 1")

### <a name="multiple-exists-conditions"></a>Múltiplas existem condições

Para comparar várias colunas de cada fluxo, adicione uma nova condição existente clicando no ícone plus ao lado de uma linha existente. Cada condição adicional é acompanhada por uma declaração de "e". Comparar duas colunas é a mesma expressão que a seguinte expressão:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Expressão personalizada

Para criar uma expressão de forma livre que contenha operadores que não "e" e "iguais", selecione o campo de **expressão Personalizado.** Introduza uma expressão personalizada através do construtor de expressão de fluxo de dados clicando na caixa azul.

![Existe definições personalizadas](media/data-flow/exists1.png "existe costume")

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

O exemplo abaixo é `checkForChanges` uma transformação `NameNorm2` existente `TypeConversions`chamada que toma fluxo esquerdo e fluxo direito .  A condição existente `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` é a expressão `EMPID` que `Region` retorna verdadeira se as colunas e colunas de cada fluxo corresponderem. Como estamos à procura de `negate` existência, é falso. Não estamos a permitir qualquer transmissão no `broadcast` separador `'none'`otimizador, por isso tem valor.

Na Fábrica de Dados UX, esta transformação parece a imagem abaixo:

![Existe exemplo](media/data-flow/exists-script.png "Existe exemplo")

O script de fluxo de dados para esta transformação está no corte abaixo:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Passos seguintes

Transformações semelhantes são [Lookup](data-flow-lookup.md) e [Join](data-flow-join.md).
