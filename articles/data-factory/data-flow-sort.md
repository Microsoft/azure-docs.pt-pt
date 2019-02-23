---
title: Transformação de classificação de fluxo de dados de fábrica de dados do Azure
description: Transformação de associação de tipo de dados de fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 7adc8c80ecca12364fb7f761092b1a5d9f528e19
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729768"
---
# <a name="azure-data-factory-data-sort-transformations"></a>Transformações de tipo de dados de fábrica de dados do Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Definições de ordenar](media/data-flow/sort.png "ordenação")

A transformação Sort permite-lhe classificar as linhas de entrada no fluxo de dados atual. As linhas de saída da transformação Sort, em seguida, seguir as regras de ordenação que definir. Pode escolher colunas individuais e classificá-los ASC ou DEC, usando o indicador de seta junto a cada campo. Se precisar de modificar a coluna antes de aplicar a classificação, clique em "Colunas calculadas" para iniciar o editor de expressões. Isso fornecerá a oportunidade de criar uma expressão para a operação de ordenação em vez de simplesmente aplicar uma coluna para a classificação.

Pode ativar o "Sensível a maiúsculas e minúsculas" se desejar ignorar maiúsculas / minúsculas ao ordenar os campos de cadeia de caracteres ou texto.

"Ordena apenas dentro de partições" tira partido da criação de partições de dados do Spark. A ordenação de dados de entrada apenas dentro de cada partição, os dados fluem pode ordenar os dados particionados em vez de classificação de fluxo de dados inteiro.

Todas as condições de classificação na transformação Sort podem ser reorganizadas. Então, se precisar de mover uma coluna maior na precedência de ordenação, pegar essa linha com o mouse e mova-superior ou inferior na lista de classificação.

Os efeitos exercidos sobre ordenação de criação de partições

O fluxo de dados do ADF é executado em grandes volumes de dados os clusters do Spark com dados distribuídos em vários nós e partições. É importante manter isso em mente ao desenvolver a arquitetura de seu fluxo de dados se dependem de transformação Sort para manter os dados nessa mesma ordem. Se optar por criar novas partições de seus dados numa transformação subsequente, pode perder a sua classificação devido a essa reshuffling de dados.
