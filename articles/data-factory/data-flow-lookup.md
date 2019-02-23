---
title: Mapeamento de transformação de pesquisa de fluxo de dados de fábrica de dados do Azure
description: Mapeamento de transformação de pesquisa de fluxo de dados de fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b156917a9987b023a9bf94e51c0cc14aebb133c7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738390"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Mapeamento de transformação de pesquisa de fluxo de dados de fábrica de dados do Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilize Lookup para adicionar dados de referência de outra origem para o seu fluxo de dados. A transformação de pesquisa requer uma origem definida que aponta para a tabela de referência e faz a correspondência em campos de chave.

![Transformação lookup](media/data-flow/lookup1.png "pesquisa")

Selecione os campos de chave que deseja correspondência com entre os campos de fluxo de entrada e os campos da origem de referência. Primeiro tem de criar uma nova origem da tela de design de fluxo de dados para utilizar como à direita para a pesquisa.

Quando forem encontradas correspondências, resultante de linhas e colunas da origem de referência serão adicionadas ao seu fluxo de dados. Pode escolher quais os campos de interesse que deseja incluir no seu coletor no final do seu fluxo de dados.
