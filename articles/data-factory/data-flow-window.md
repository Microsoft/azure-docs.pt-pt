---
title: Mapeamento de transformação de janela de fluxo de dados de fábrica de dados do Azure
description: Mapeamento de transformação de janela de fluxo de dados de fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 60617261e14b77e9ef3a2968dc37159e961bb947
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735388"
---
# <a name="azure-data-factory-mapping-data-flow-window-transformation"></a>Mapeamento de transformação de janela de fluxo de dados de fábrica de dados do Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A transformação de janela é onde definirá agregações com base na janela de colunas em seus fluxos de dados. No construtor de expressões, pode definir tipos diferentes de agregações que se baseiam em windows de data ou hora (cláusula SQL OVER), como oportunidades potenciais, ATRASO, NTILE, CUMEDIST, classificação, etc.). Um novo campo será gerado no resultado que inclui estas agregações. Também pode incluir campos de grupo opcional.

![Opções da janela](media/data-flow/windows1.png "windows 1")

## <a name="over"></a>Mais de
Defina o particionamento de dados de coluna para a sua transformação de janela. O equivalente SQL é o ```Partition By``` na cláusula Over em SQL. Se desejar criar um cálculo ou criar uma expressão a utilizar para a criação de partições, pode fazê-lo passando o mouse sobre o nome da coluna e selecione "a coluna calculada".

![Opções da janela](media/data-flow/windows4.png "windows 4")

## <a name="sort"></a>Ordenar
Outra parte da cláusula Over é definir o ```Order By```. Isso definirá a classificação de dados de ordenação. Também pode criar uma expressão para um valor de calculate neste campo de coluna para ordenar.

![Opções da janela](media/data-flow/windows5.png "windows 5")

## <a name="range-by"></a>Intervalo por
Em seguida, configure a moldura de janela Unbounded ou limitado. Para definir uma moldura de janela não vinculado, defina o controlo de deslize como Unbounded em ambas as extremidades. Se optar por uma configuração entre Unbounded e Current Row, tem de definir o início de deslocamento e valores de fim. Ambos os valores serão números inteiros positivos. Pode utilizar números relativos ou valores dos seus dados.

O controlo de deslize de janela tem dois valores para definir: os valores antes da linha atual e os valores após a linha atual. O deslocamento de início e de fim corresponde os dois seletores no controlo de deslize.

![Opções da janela](media/data-flow/windows6.png "windows 6")

## <a name="window-columns"></a>Colunas de janela
Por último, utilize o construtor de expressões para definir as agregações que pretende utilizar com os dados de windows, como classificação, COUNT, MIN, MAX, classificação DENSA, oportunidade potencial, ATRASO, etc.

![Opções da janela](media/data-flow/windows7.png "windows 7")

A lista completa de agregação e as funções analíticas disponíveis para utilização no ADF dados fluxo expressão de idioma via o construtor de expressões estão listados aqui: https://aka.ms/dataflowexpressions.

