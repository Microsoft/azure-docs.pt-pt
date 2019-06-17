---
title: Transformação de dados fábrica mapeamento de fluxo que existem dados do Azure
description: Como verificar a existência de linhas existentes com dados de mapeamento de fábrica de dados de fluxos com a transformação de Exists
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: b98b7afb21f2f50d44ba93ed793b6efb20f75164
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65235975"
---
# <a name="mapping-data-flow-exists-transformation"></a>Mapeamento de fluxo de dados existe transformação

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A transformação de Exists é uma transformação que interrompe ou permite linhas nos seus dados a fluir através de filtragem de linha. Existe transformar é semelhante à ```SQL WHERE EXISTS``` e ```SQL WHERE NOT EXISTS```. Após a transformação existe, as linhas resultantes do seu fluxo de dados irão optar por incluir todas as linhas onde os valores de coluna da origem 1 existem na origem 2 ou não existem na origem 2.

![Definições de existe](media/data-flow/exists.png "existe 1")

Escolha a segunda origem para sua Exists, de modo a que o fluxo de dados pode comparar valores de 1 de Stream contra Stream 2.

Selecione a coluna a partir de 1 de origem e de origem 2 cujos valores que pretende procurar contra Exists ou não existe.

## <a name="multiple-exists-conditions"></a>Existe de várias condições

Junto a cada linha nas suas condições de coluna para Exists, encontrará um + início de sessão disponíveis quando focaliza alcançar a linha. Isso permitirá que adicionar várias linhas para condições de Exists. Cada condição adicional é um "E".

## <a name="custom-expression"></a>Expressão personalizada

![Definições personalizadas de existe](media/data-flow/exists1.png "existe personalizado")

Pode clicar em "Expressão personalizada", em vez disso, criar uma expressão de forma livre como sua existe ou não-existe condição. Selecionar esta caixa permitirá que escreva sua própria expressão como uma condição.

## <a name="next-steps"></a>Passos Seguintes

São semelhantes [Lookup](data-flow-lookup.md) e [associar](data-flow-join.md).
