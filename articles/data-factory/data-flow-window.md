---
title: Mapeando a transformação da janela fluxo de dados
description: Transformação da janela fluxo de dados de mapeamento de Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: fa34def67d91332a00bf0ee92b365957a47f9616
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931487"
---
# <a name="azure-data-factory-window-transformation"></a>Transformação de janela de Azure Data Factory



A transformação janela é onde você definirá as agregações baseadas em janela de colunas em seus fluxos de dados. No construtor de expressões, você pode definir diferentes tipos de agregações que se baseiam em dados ou em janelas de tempo (cláusula SQL OVER), como LEAD, LAG, NTILE, CUMEDIST, RANK, etc.). Um novo campo será gerado na saída que inclui essas agregações. Você também pode incluir campos de grupo-por opcionais.

![Opções de janela](media/data-flow/windows1.png "Windows 1")

## <a name="over"></a>Mais de
Defina o particionamento de dados da coluna para a transformação da janela. O equivalente do SQL é o ```Partition By``` na cláusula over no SQL. Se você quiser criar um cálculo ou criar uma expressão a ser usada para o particionamento, poderá fazer isso passando o mouse sobre o nome da coluna e selecionando "coluna computada".

![Opções de janela](media/data-flow/windows4.png "Windows 4")

## <a name="sort"></a>Ordenar
Outra parte da cláusula over é definir a ```Order By```. Isso definirá a ordenação de classificação de dados. Você também pode criar uma expressão para um valor de cálculo neste campo de coluna para classificação.

![Opções de janela](media/data-flow/windows5.png "Windows 5")

## <a name="range-by"></a>Intervalo por
Em seguida, defina o quadro da janela como não associado ou limitado. Para definir um quadro de janela não associado, defina o controle deslizante como não associado em ambas as extremidades. Se você escolher uma configuração entre a linha não vinculada e a atual, deverá definir os valores de início e término do deslocamento. Ambos os valores serão inteiros positivos. Você pode usar números ou valores relativos de seus dados.

O controle deslizante de janela tem dois valores a serem definidos: os valores antes da linha atual e os valores após a linha atual. O deslocamento inicial e final corresponde aos dois seletores no controle deslizante.

![Opções de janela](media/data-flow/windows6.png "Windows 6")

## <a name="window-columns"></a>Colunas da janela
Por fim, use o construtor de expressões para definir as agregações que você deseja usar com as janelas de dados, como classificação, contagem, mín., máx., classificação DENSa, LEAD, retardo, etc.

![Opções de janela](media/data-flow/windows7.png "windows 7")

A lista completa de agregações e funções analíticas disponíveis para uso na linguagem de expressão do fluxo de dados do ADF por meio do construtor de expressões estão listadas aqui: https://aka.ms/dataflowexpressions.

## <a name="next-steps"></a>Passos seguintes

Se você estiver procurando uma agregação de grupo por simples, use a [transformação Agregação](data-flow-aggregate.md)
