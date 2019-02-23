---
title: Transformação de coluna derivada de fluxo de dados de fábrica de dados do Azure
description: Transformação de coluna derivada de fluxo de dados de fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 1335b86431bcf345f452ead0c95926d295ae185f
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728765"
---
# <a name="azure-data-factory-data-flow-derived-column-transformation"></a>Transformação de coluna derivada de fluxo de dados de fábrica de dados do Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilize a transformação Derived Column para gerar novas colunas do fluxo de dados ou para modificar os campos existentes.

![derivar coluna](media/data-flow/dc1.png "Derived Column")

Pode efetuar várias ações de coluna derivada numa transformação Derived Column única. Clique em "Adicionar coluna" para transformar a mais de 1 coluna no passo único de transformação.

No campo de coluna, selecione uma coluna existente para substituir com um novo valor derivado ou clique em "Criar nova coluna" para gerar uma nova coluna com o valor derivado recentemente.

A caixa de texto expressão irá abrir o construtor de expressões onde pode criar a expressão para colunas derivadas, com as funções de expressão.
