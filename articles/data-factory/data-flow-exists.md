---
title: Existe transformação no fluxo de dados de mapeamento
description: Verifique se existem linhas existentes utilizando a transformação existente no fluxo de dados de mapeamento da Fábrica de Dados da Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/07/2020
ms.openlocfilehash: 805b51bf4e6d8feab9539f660dfc72ca78b82d5c
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982637"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Existe transformação no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A transformação existente é uma transformação de filtragem de linha que verifica se os seus dados existem em outra fonte ou fluxo. O fluxo de saída inclui todas as linhas no fluxo esquerdo que existem ou não existem no fluxo certo. A transformação existente ```SQL WHERE EXISTS``` ```SQL WHERE NOT EXISTS```é semelhante a e.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vZKz]

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

## <a name="broadcast-optimization"></a>Otimização de transmissão

![Broadcast Join](media/data-flow/broadcast.png "Broadcast Join")

Em conjuntos, as aparências e a transformação existe, se um ou ambos os fluxos de dados se encaixarem na memória do nó do trabalhador, pode otimizar o desempenho permitindo a **Radiodifusão**. Por predefinição, o motor de faísca decidirá automaticamente se transmite ou não um dos lados. Para escolher manualmente de que lado transmitir, selecione **Fixed**.

Não é aconselhável desativar a radiodifusão através da opção **Off,** a menos que as suas juntas estejam a ter erros de tempo.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
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
        broadcast: 'auto'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Passos seguintes

Transformações semelhantes são [Lookup](data-flow-lookup.md) e [Join](data-flow-join.md).
