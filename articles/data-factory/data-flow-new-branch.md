---
title: Nova transformação do ramo de fluxo de dados de mapeamento de fábrica de dados do Azure
description: Nova transformação do ramo de fluxo de dados de mapeamento de fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 9fac78f21f2f128ccb040e176891c33d39bf2820
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732800"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Nova transformação do ramo de fluxo de dados de mapeamento de fábrica de dados do Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Opções de ramificar](media/data-flow/menu.png "menu")

Ramificação tirar o fluxo de dados atual do fluxo de dados e replicá-la em outro fluxo. Novo ramo de uso para executar vários conjuntos de operações e transformações no mesmo fluxo de dados.

Exemplo: O fluxo de dados tem uma transformação de origem com um conjunto de colunas e conversões de tipo de dados selecionado. Em seguida, coloca uma coluna derivada imediatamente a seguir essa origem. Na coluna derivada, criar um novo campo que combina primeiro nome e apelido para fazer um novo campo de "nome completo".

Pode tratar esse novo fluxo com um conjunto de transformações e um coletor de uma linha e utilizar o novo ramo para criar uma cópia desse fluxo onde pode transformar esses mesmos dados com um conjunto diferente de transformações. Transformando dados copiados numa ramificação separado, pode, em seguida, de sink esses dados para uma localização separada.

> [!NOTE]
> "Novo ramo" apenas será apresentado como uma ação na + menu de transformação quando existe uma transformação subsequente seguindo a localização atual em que está a tentar efetuar a ramificação. ou seja, não verá uma opção de "Novo ramo" no final até adicionar outra transformação depois do Select

![Ramo](media/data-flow/branch2.png "ramo 2")
