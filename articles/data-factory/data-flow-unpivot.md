---
title: Transformação não-adívora no fluxo de dados de mapeamento
description: Azure Data Factory mapeamento de dados fluxo unpivot Transformação
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: ea8881adf39a315df7746dbce14dedcbee18ccf6
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521055"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>Transformação não-adívora no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize o fluxo de dados de mapeamento unpivot em ADF como forma de transformar um conjunto de dados não normalizado numa versão mais normalizada, expandindo valores de várias colunas num único registo em múltiplos registos com os mesmos valores numa única coluna.

![Transformação Não-3](media/data-flow/unpivot1.png "Opções não-depívoras 1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>Desagrupamento por

![Transformação Não-3](media/data-flow/unpivot5.png "Opções não-abertas 2")

Primeiro, desagrega as colunas pelas que deseja desagrupar para a sua agregação não-pívora. Desaprova uma ou mais colunas para desagrupamento com o sinal + ao lado da lista de colunas.

## <a name="unpivot-key"></a>Chave não-marfim

![Transformação Não-3](media/data-flow/unpivot6.png "Opções não-marfim 3")

A Chave Unpivot é a coluna que a ADF vai girar de coluna para linha. Por predefinição, cada valor único no conjunto de dados deste campo irá girar para uma linha. No entanto, pode introduzir opcionalmente os valores a partir do conjunto de dados que deseja orientar para valores de linha.

## <a name="unpivoted-columns"></a>Colunas Não-Edívoras

![Transformação Não-3](media/data-flow//unpivot7.png "Opções não-marfim 4")

Por último, escolha o nome da coluna para armazenar os valores para colunas não nomeadas que são transformadas em linhas.

(Opcional) Pode cair fileiras com valores nulos.

Por exemplo, SumCost é o nome da coluna que é escolhido no exemplo partilhado acima.

![Imagem mostrando as colunas PO, Fornecedor e Frutas antes e depois de uma transformação unipivot usando a coluna Fruit como a chave unipivot.](media/data-flow/unpivot3.png)

Definir o Acordo de Coluna para "Normal" irá agrupar todas as novas colunas não-porpavoradas de um único valor. Definir o arranjo das colunas para "Lateral" irá agrupar novas colunas não-edívoras geradas a partir de uma coluna existente.

![Transformação Não-3](media/data-flow//unpivot7.png "Opções não-abertas 5")

O conjunto final de resultados de dados não-preparados mostra que os totais da coluna agora não são despívorados em valores de linha separados.

## <a name="next-steps"></a>Passos seguintes

Use a [transformação do pivô](data-flow-pivot.md) para pisar linhas em colunas.
