---
title: Mapeando a transformação da janela do fluxo de dados
description: Azure Data Factory mapeando dados fluxo de janela transformação da janela
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 67e966e3b3dd274e993797ed37e17c5490d632c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416479"
---
# <a name="azure-data-factory-window-transformation"></a>Transformação da janela da fábrica de dados azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A transformação da Janela é onde definirá agregações baseadas em janelas de colunas nos seus fluxos de dados. No Construtor de Expressão, pode definir diferentes tipos de agregações que são baseadas em datas ou janelas de tempo (cláusula SQL OVER) tais como LEAD, LAG, NTILE, CUMEDIST, RANK, etc.). Um novo campo será gerado na sua saída que inclui estas agregações. Também pode incluir campos de grupo opcionais.

![Opções de janela](media/data-flow/windows1.png "janelas 1")

## <a name="over"></a>Mais
Detete a divisão de dados da coluna para a sua transformação da janela. O equivalente SQL ```Partition By``` é o na cláusula Over em SQL. Se desejar criar um cálculo ou criar uma expressão para usar para a partilha, pode fazê-lo pairando sobre o nome da coluna e selecionando "coluna computorizada".

![Opções de janela](media/data-flow/windows4.png "janelas 4")

## <a name="sort"></a>Ordenar
Outra parte da cláusula Over ```Order By```é a definição do . Isto irá definir a ordem de classificação de dados. Também pode criar uma expressão para calcular valor neste campo de colunas para a triagem.

![Opções de janela](media/data-flow/windows5.png "janelas 5")

## <a name="range-by"></a>Intervalo por
Em seguida, coloque a moldura da janela como deslimitada ou limitada. Para definir uma janela deslimitada, coloque o slider deslimitado em ambas as extremidades. Se escolher uma definição entre a Linha Não Limitada e a Linha Atual, então deve definir os valores de início e fim do Offset. Ambos os valores serão inteiros positivos. Pode utilizar números ou valores relativos a partir dos seus dados.

O slider da janela tem dois valores a definir: os valores antes da linha atual e os valores após a linha atual. O offset Iniciar e Fim corresponde aos dois selecionadores no slider.

![Opções de janela](media/data-flow/windows6.png "janelas 6")

## <a name="window-columns"></a>Colunas de janela
Por último, utilize o Construtor de Expressão para definir as agregações que pretende utilizar com as janelas de dados tais como RANK, COUNT, MIN, MAX, DENSE RANK, LEAD, LAG, etc.

![Opções de janela](media/data-flow/windows7.png "janelas 7")

A lista completa de agregação e funções analíticas disponíveis para que possa utilizar https://aka.ms/dataflowexpressionsna Linguagem de Expressão de Fluxo de Dados ADF através do Construtor de Expressão está listada aqui: .

## <a name="next-steps"></a>Passos seguintes

Se procura uma simples agregação em grupo, use a [transformação agregada](data-flow-aggregate.md)
