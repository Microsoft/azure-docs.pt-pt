---
title: Transformação não-adívora no fluxo de dados de mapeamento
description: Azure Data Factory mapeamento de dados fluxo unpivot Transformação
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: 38986c3f93856981e903ae93ed7788ae01fc6d5b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91823583"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>Transformação não-adívora no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize o fluxo de dados de mapeamento unpivot em ADF como forma de transformar um conjunto de dados não normalizado numa versão mais normalizada, expandindo valores de várias colunas num único registo em múltiplos registos com os mesmos valores numa única coluna.

![Transformação Não-3](media/data-flow/unpivot1.png "Opções não-depívoras 1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>Desagrupamento por

![Transformação Não-3](media/data-flow/unpivot5.png "Opções não-abertas 2")

Primeiro, desagrega as colunas pelas que deseja agrupar para a sua agregação de pivôs. Desaprova uma ou mais colunas para desagrupamento com o sinal + ao lado da lista de colunas.

## <a name="unpivot-key"></a>Chave não-marfim

![Transformação Não-3](media/data-flow/unpivot6.png "Opções não-marfim 3")

A Chave Pivot é a coluna que a ADF irá girar de linha para coluna. Por predefinição, cada valor único no conjunto de dados deste campo irá girar para uma coluna. No entanto, pode introduzir opcionalmente os valores a partir do conjunto de dados que deseja orientar para valores de coluna.

## <a name="unpivoted-columns"></a>Colunas Não-Edívoras

![Transformação Não-3](media/data-flow//unpivot7.png "Opções não-marfim 4")

Por último, escolha a agregação que deseja utilizar para os valores mais indicados e como gostaria que as colunas fossem exibidas na nova projeção de saída a partir da transformação.

(Opcional) Pode definir um padrão de nomeação com um prefixo, meio e sufixo a ser adicionado a cada novo nome de coluna a partir dos valores da linha.

Por exemplo, apostar em "Vendas" por "Região" simplesmente lhe daria novos valores de coluna a partir de cada valor de venda. Por exemplo: "25", "50", "1000", ... No entanto, se definir um valor prefixo de "Vendas", então "Vendas" será prefixado nos valores.

![Imagem mostrando as colunas PO, Fornecedor e Frutas antes e depois de uma transformação unipivot usando a coluna Fruit como a chave unipivot.](media/data-flow/unpivot3.png)

A definição do Convénio da Coluna para "Normal" agrupará todas as colunas com os seus valores agregados. A definição do arranjo das colunas para "Lateral" alternará entre coluna e valor.

![Transformação Não-3](media/data-flow//unpivot7.png "Opções não-abertas 5")

O conjunto final de resultados de dados não-preparados mostra que os totais da coluna agora não são despívorados em valores de linha separados.

## <a name="next-steps"></a>Passos seguintes

Use a [transformação do pivô](data-flow-pivot.md) para pisar linhas em colunas.
