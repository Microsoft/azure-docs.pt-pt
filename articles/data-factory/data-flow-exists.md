---
title: Mapeamento de fluxo de dados de fábrica de dados do Azure existe transformação
description: Mapeamento de fluxo de dados de fábrica de dados do Azure existe transformação
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6ce27ba699ae766ed4d2428f67d91379464bb9f1
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006134"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Mapeamento de fluxo de dados de fábrica de dados do Azure existe transformação

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A transformação de Exists é uma transformação que interrompe ou permite linhas nos seus dados a fluir através de filtragem de linha. Existe transformar é semelhante à ```SQL WHERE EXISTS``` e ```SQL WHERE NOT EXISTS```. Depois de uma transformação de filtro, as linhas resultantes do seu fluxo de dados irão optar por incluir todas as linhas onde os valores de coluna da origem 1 existem na origem 2 ou não existem na origem 2.

![Definições de existe](media/data-flow/exsits.png "existe 1")

Escolha a segunda origem para sua Exists, de modo a que o fluxo de dados pode comparar valores de 1 de Stream contra Stream 2.

Selecione a coluna a partir de 1 de origem e de origem 2 cujos valores que pretende procurar contra Exists ou não existe.

## <a name="multiple-exists-conditions"></a>Existe de várias condições

Junto a cada linha nas suas condições de coluna para Exsits, encontrará um + início de sessão disponíveis quando focaliza alcançar a linha. Isso permitirá que adicionar várias linhas para condições de Exists.

## <a name="next-steps"></a>Passos Seguintes

