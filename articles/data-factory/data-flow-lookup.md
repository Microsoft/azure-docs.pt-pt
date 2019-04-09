---
title: Mapeamento de transformação de pesquisa de fluxo de dados de fábrica de dados do Azure
description: Mapeamento de transformação de pesquisa de fluxo de dados de fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: ef473ea5f88b9108894787785fe1e9083fab1b0a
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006211"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Mapeamento de transformação de pesquisa de fluxo de dados de fábrica de dados do Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilize Lookup para adicionar dados de referência de outra origem para o seu fluxo de dados. A transformação de pesquisa requer uma origem definida que aponta para a tabela de referência e faz a correspondência em campos de chave.

![Transformação lookup](media/data-flow/lookup1.png "pesquisa")

Selecione os campos de chave que deseja correspondência com entre os campos de fluxo de entrada e os campos da origem de referência. Primeiro tem de criar uma nova origem da tela de design de fluxo de dados para utilizar como à direita para a pesquisa.

Quando forem encontradas correspondências, resultante de linhas e colunas da origem de referência serão adicionadas ao seu fluxo de dados. Pode escolher quais os campos de interesse que deseja incluir no seu coletor no final do seu fluxo de dados.

## <a name="optimizations"></a>Otimizações

No Data Factory, os dados fluem executar em ambientes de Spark de escalamento horizontal. Se o conjunto de dados pode caber no espaço de memória do nó de trabalho, que podemos otimizar o desempenho da sua pesquisa.

![Associação de difusão](media/data-flow/broadcast.png "de difusão de associação")

### <a name="broadcast-join"></a>Associação de difusão

Selecionar à esquerda e/ou associação para pedir o ADF para enviar todo o conjunto de dados a partir de ambos os lados da relação de pesquisa para a memória de difusão do lado direito.

### <a name="data-partitioning"></a>Criação de partições de dados

Também pode especificar a criação de partições dos seus dados ao selecionar "Definir a criação de partições" no separador de Otimização da transformação Lookup para criar conjuntos de dados que se encaixa melhor em memória por função de trabalho.

## <a name="next-steps"></a>Passos Seguintes

[Junte-se](data-flow-join.md) e [Exists](data-flow-exists.md) transformações realizar tarefas semelhantes nos fluxos de dados de mapeamento ADF. Dar uma olhada nessas transformações seguinte.
