---
title: Transformação anular dinamização de fluxo de dados de mapeamento de fábrica de dados do Azure
description: Transformação anular dinamização de fluxo de dados de mapeamento de fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: f1eca7f76f45966ba43c15b3d8b92203c8199bfe
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732080"
---
# <a name="azure-data-factory-mapping-data-flow-unpivot-transformation"></a>Transformação anular dinamização de fluxo de dados de mapeamento de fábrica de dados do Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilize Unpivot no fluxo de dados de mapeamento ADF como uma forma de transformar um conjunto de dados unnormalized numa versão mais normalizada ao expandir os valores de várias colunas num único Registro em vários registos com os mesmos valores numa única coluna.

![Anular dinamização de transformação](media/data-flow/unpivot1.png "anular dinamização das opções de 1")

## <a name="ungroup-by"></a>Desagrupar por

![Anular dinamização de transformação](media/data-flow/unpivot5.png "anular dinamização das opções de 2")

Em primeiro lugar, defina as colunas que deseja agrupar por para a agregação de pivot. Definir uma ou mais colunas para ungrouping com a + início de sessão junto à lista de colunas.

## <a name="unpivot-key"></a>Anular dinamização de chave

![Anular dinamização de transformação](media/data-flow/unpivot6.png "anular dinamização das opções de 3")

A chave de pivô é a coluna que ADF será dinâmico da linha a coluna. Por predefinição, cada valor exclusivo no conjunto de dados para este campo será dinâmico a uma coluna. No entanto, pode, opcionalmente, introduza os valores do conjunto de dados que pretende passar para os valores de coluna.

## <a name="unpivoted-columns"></a>Colunas convertidas em valores

![Anular dinamização de transformação](media/data-flow//unpivot7.png "anular dinamização das opções de 4")

Por último, escolha a agregação de que deseja usar para os valores articulados e como gostaria que as colunas a apresentar na nova projeção de saída da transformação.

(Opcional) Pode definir um padrão de nomenclatura com um prefixo, intermediária e sufixo para ser adicionado a cada novo nome de coluna de entre os valores de linha.

Por exemplo, dinamização "vendas" por "Região" simplesmente daria novos valores de coluna de cada valor de vendas. Por exemplo: "25", "50", "1000", ... No entanto, se definir um valor de prefixo de "Vendas", em seguida, "Vendas" terá o prefixo para os valores.

<img src="media/data-flow/unpivot3.png" width="400">

Definir a disposição de coluna como "Normal" será agrupar todas as colunas articuladas com os respetivos valores agregados. Definir a disposição de colunas para "Lateral" irá se alternam entre a coluna e valor.

![Anular dinamização de transformação](media/data-flow//unpivot7.png "anular dinamização das opções de 5")

O conjunto de resultado final dados convertidas em valores mostra os totais de colunas convertidas em valores agora em valores de linha separado.
