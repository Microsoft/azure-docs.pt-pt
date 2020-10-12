---
title: Transformação existente no fluxo de dados de mapeamento
description: Verifique se as linhas existentes utilizando a transformação existente no fluxo de dados de mapeamento da Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/07/2020
ms.openlocfilehash: 805b51bf4e6d8feab9539f660dfc72ca78b82d5c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82982637"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Transformação existente no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A transformação existente é uma transformação de filtragem de linha que verifica se os seus dados existem em outra fonte ou fluxo. O fluxo de saída inclui todas as linhas no fluxo esquerdo que existem ou não existem no fluxo direito. A transformação existente é semelhante a ```SQL WHERE EXISTS``` e ```SQL WHERE NOT EXISTS``` .

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vZKz]

## <a name="configuration"></a>Configuração

1. Escolha qual o fluxo de dados que está a verificar se existe na entrega do **fluxo certo.**
1. Especifique se está à procura dos dados para existir ou não na definição **do tipo Exist.**
1. Selecione se deseja ou não uma **expressão personalizada.**
1. Escolha quais colunas-chave que pretende comparar como as suas condições existem. Por predefinição, o fluxo de dados procura a igualdade entre uma coluna em cada fluxo. Para comparar através de um valor calculado, paire sobre a coluna dropdown e **selecione coluna Computada**.

![Definições existentes](media/data-flow/exists.png "existe 1")

### <a name="multiple-exists-conditions"></a>Múltiplas condições existem

Para comparar várias colunas de cada fluxo, adicione uma nova condição existente clicando no ícone mais ao lado de uma linha existente. Cada condição adicional é acompanhada por uma declaração de "e". Comparar duas colunas é a mesma expressão:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Expressão personalizada

Para criar uma expressão de forma livre que contenha operadores que não "e" e "iguais", selecione o campo **de expressão Personalizado.** Introduza uma expressão personalizada através do construtor de expressão de fluxo de dados clicando na caixa azul.

![Existe configurações personalizadas](media/data-flow/exists1.png "existe costume")

## <a name="broadcast-optimization"></a>Otimização de transmissão

![Transmissão Aderir](media/data-flow/broadcast.png "Transmissão Aderir")

Em juntas, procura e transformação existente, se um ou ambos os fluxos de dados se encaixarem na memória do nó do trabalhador, pode otimizar o desempenho permitindo a **radiodifusão.** Por predefinição, o motor de faísca decidirá automaticamente se transmite ou não um dos lados. Para escolher manualmente qual lado transmitir, selecione **Fixo**.

Não é aconselhável desativar a transmissão através da opção **Off,** a menos que as suas junções estejam a ter erros de tempo.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Exemplo

O exemplo abaixo é uma transformação existente chamada `checkForChanges` que toma o fluxo esquerdo e o fluxo `NameNorm2` `TypeConversions` direito.  A condição existente é a expressão `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` que retorna verdadeira se as `EMPID` colunas e `Region` colunas em cada fluxo corresponderem. Como estamos à procura da existência, `negate` é falso. Não estamos a permitir qualquer transmissão no separador otimizar, por isso `broadcast` tem `'none'` valor.

No Data Factory UX, esta transformação parece a imagem abaixo:

![Existe exemplo](media/data-flow/exists-script.png "Existe exemplo")

O roteiro do fluxo de dados para esta transformação está no snippet abaixo:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'auto'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Passos seguintes

Transformações semelhantes são [Lookup](data-flow-lookup.md) and [Join](data-flow-join.md).
