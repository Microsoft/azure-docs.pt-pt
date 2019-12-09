---
title: Mapeando nova transformação de Branch de fluxo de dados
description: Transformação de Azure Data Factory mapeamento de fluxo de dados novo Branch
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/12/2019
ms.openlocfilehash: b4617689fe1ab14856bde9a4e8134b12aa6d815b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930306"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Transformação de Azure Data Factory mapeamento de fluxo de dados novo Branch

![Opções de ramificação](media/data-flow/menu.png "menu")

A ramificação pegará o fluxo de dados atual em seu fluxo de dados e o replicará para outro fluxo. Use a nova ramificação para executar vários conjuntos de operações e transformações no mesmo fluxo de dados.

Exemplo: seu fluxo de dados tem uma transformação de origem com um conjunto selecionado de colunas e conversões de tipo de dados. Em seguida, coloque uma coluna derivada imediatamente após essa origem. Na coluna derivada, você criou um novo campo que combina nome e sobrenome para criar um novo campo "nome completo".

Você pode tratar esse novo fluxo com um conjunto de transformações e um coletor em uma linha e usar a nova ramificação para criar uma cópia desse fluxo, na qual é possível transformar os mesmos dados com um conjunto diferente de transformações. Ao transformar os dados copiados em uma ramificação separada, você pode, posteriormente, coletar esses dados em um local separado.

> [!NOTE]
> A "nova ramificação" só será mostrada como uma ação no menu + transformação quando houver uma transformação subsequente após o local atual em que você está tentando ramificar. ou seja, você não verá uma opção "nova ramificação" no final aqui até adicionar outra transformação após a seleção

![Ramo](media/data-flow/branch2.png "Ramificação 2")
