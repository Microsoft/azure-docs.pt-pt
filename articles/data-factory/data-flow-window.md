---
title: Transformação da janela de fluxo de dados de mapeamento
description: Azure Data Factory mapeamento dados fluxo de janela transformação de janela
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 1f440a5877dcefeca8c1baa82fad4299b5ce7bce
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81606232"
---
# <a name="azure-data-factory-window-transformation"></a>Transformação da janela da fábrica de dados Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A transformação da Janela é onde definirá agregações baseadas em janelas de colunas nos seus fluxos de dados. No Expression Builder, pode definir diferentes tipos de agregações que se baseiam em dados ou janelas de tempo (cláusula SQL OVER) tais como LEAD, LAG, NTILE, CUMEDIST, RANK, etc.). Um novo campo será gerado na sua saída que inclui estas agregações. Também pode incluir campos de grupo opcionais.

![Opções de janela](media/data-flow/windows1.png "janelas 1")

## <a name="over"></a>Mais
Desajei a partição dos dados da coluna para a transformação da sua janela. O equivalente SQL é ```Partition By``` a cláusula Over em SQL. Se desejar criar um cálculo ou criar uma expressão a utilizar para a partição, pode fazê-lo pairando sobre o nome da coluna e selecionando "coluna computada".

![Opções de janela](media/data-flow/windows4.png "janelas 4")

## <a name="sort"></a>Ordenar
Outra parte da cláusula Over é definir a ```Order By``` . Isto definirá o tipo de pedido de dados. Também pode criar uma expressão para um valor de cálculo neste campo de coluna para a triagem.

![Opções de janela](media/data-flow/windows5.png "janelas 5")

## <a name="range-by"></a>Alcance por
Em seguida, coloque a moldura da janela como Unbounded ou Bounded. Para definir uma janela sem limites, coloque o slider em Unbounded em ambas as extremidades. Se escolher uma definição entre a Linha Não Limitada e a Corrente, então tem de definir os valores de início e final do Offset. Ambos os valores serão inteiros positivos. Pode utilizar números relativos ou valores a partir dos seus dados.

O deslizador da janela tem dois valores a definir: os valores antes da linha atual e os valores após a linha atual. A compensação Start and End corresponde aos dois selecionadores do slider.

![Opções de janela](media/data-flow/windows6.png "janelas 6")

## <a name="window-columns"></a>Colunas de janelas
Por último, utilize o Expression Builder para definir as agregações que pretende utilizar com as janelas de dados tais como RANK, COUNT, MIN, MAX, DENSE RANK, LEAD, LAG, etc.

![Opções de janela](media/data-flow/windows7.png "janelas 7")

A lista completa de funções agregadas e analíticas disponíveis para utilizar na Linguagem de Expressão de Fluxo de Dados ADF através do Expression Builder está listada aqui: https://aka.ms/dataflowexpressions .

## <a name="next-steps"></a>Próximos passos

Se procura uma agregação simples de grupo, use a [transformação agregada](data-flow-aggregate.md)
