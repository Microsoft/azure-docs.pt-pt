---
title: Nós de movimentação de fluxo de dados de fábrica de dados do Azure
description: Como mover nós num diagrama de fluxo de dados do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: d82b32e876144a626333f3df1481c5fce9862067
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732182"
---
# <a name="mapping-data-flow-move-nodes"></a>Nós de movimentação de fluxo de dados de mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Agregar as opções de transformação](media/data-flow/agghead.png "cabeçalho agregador")

A superfície de design de fluxo de dados do Azure Data Factory é uma superfície de "construção" onde cria dados fluxos cima para baixo, à esquerda para a direita. Há uma caixa de ferramentas anexada a cada transformação com uma vantagem (+) símbolo. Se concentrar na lógica de negócio em vez de ligar nós através de limites num ambiente do DAG de forma livre.

Então, sem um paradigma de arrastar e soltar, a forma de "Mover" um nó de transformação, é alterar o fluxo de entrada. Em vez disso, deve ir transformações em torno ao alterar o "fluxo de entrada".

No fluxo de dados do Azure Data Factory, os fluxos de representam o fluxo de dados. No painel de definições de transformação, verá um campo de "Entrada Steam". Isto indica o fluxo de dados de entrada é a manutenção dessa transformação. Pode alterar a localização física do seu nó de transformação no gráfico ao clicar no nome de entrada Stream e selecionar outro fluxo de dados. A transformação atual juntamente com todas as transformações subseqüentes desse fluxo, em seguida, irá mudar para a nova localização.

Se estiver a mover uma transformação com transformações de um ou mais depois dela, o novo local em que o fluxo de dados ser associado através de um novo ramo.

Se tiver sem as transformações subseqüentes depois do nó que selecionou, em seguida, apenas essa transformação irá mudar para a nova localização.
