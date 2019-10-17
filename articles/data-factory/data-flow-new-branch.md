---
title: Transformação de Azure Data Factory mapeamento de fluxo de dados novo Branch
description: Transformação de Azure Data Factory mapeamento de fluxo de dados novo Branch
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: de8cb74d788e3ca7599f226e4204c4b09112e70c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387224"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Transformação de Azure Data Factory mapeamento de fluxo de dados novo Branch



![Opções de ramificação](media/data-flow/menu.png "AdicionarMenu")

A ramificação pegará o fluxo de dados atual em seu fluxo de dados e o replicará para outro fluxo. Use a nova ramificação para executar vários conjuntos de operações e transformações no mesmo fluxo de dados.

Exemplo: seu fluxo de dados tem uma transformação de origem com um conjunto selecionado de colunas e conversões de tipo de dados. Em seguida, coloque uma coluna derivada imediatamente após essa origem. Na coluna derivada, você criou um novo campo que combina nome e sobrenome para criar um novo campo "nome completo".

Você pode tratar esse novo fluxo com um conjunto de transformações e um coletor em uma linha e usar a nova ramificação para criar uma cópia desse fluxo, na qual é possível transformar os mesmos dados com um conjunto diferente de transformações. Ao transformar os dados copiados em uma ramificação separada, você pode, posteriormente, coletar esses dados em um local separado.

> [!NOTE]
> A "nova ramificação" só será mostrada como uma ação no menu + transformação quando houver uma transformação subsequente após o local atual em que você está tentando ramificar. ou seja, você não verá uma opção "nova ramificação" no final aqui até adicionar outra transformação após a seleção

![Ramo](media/data-flow/branch2.png "Ramificação 2")
