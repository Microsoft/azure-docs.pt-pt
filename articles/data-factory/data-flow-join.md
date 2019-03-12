---
title: Transformação de associação de fluxo de dados de fábrica de dados do Azure
description: Transformação de associação de fluxo de dados de fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 204ee1b812949311258be968de387dc5b66c4fc0
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726877"
---
# <a name="mapping-data-flow-join-transformation"></a>Transformação de associação de fluxo de dados de mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilize a associação de combinar dados de duas tabelas no seu fluxo de dados. Clique na transformação que será a relação à esquerda e adicione uma transformação de associação da caixa de ferramentas. Dentro a transformação de associação, irá selecionar outro fluxo de dados a partir do seu fluxo de dados para ser certa relação.

![Junte-se a transformação](media/data-flow/join.png "aderir")

## <a name="join-types"></a>Junte-se de tipos

Selecionar tipo de associação é necessária para a transformação de associação.

### <a name="inner-join"></a>Associação interna

Associação interna passará apenas as linhas que coincidam com as condições de coluna das duas tabelas.

### <a name="left-outer"></a>Externa à esquerda

Todas as linhas do fluxo de esquerda não cumprir a condição de associação que elas passem por e colunas de saída da outra tabela estão definidas como NULL, além de todas as linhas retornadas da associação interna.

### <a name="right-outer"></a>Externa à direita

Todas as linhas do fluxo de direito não cumprir a condição de associação que elas passem por e colunas de saída que correspondem à outra tabela estão definidas como NULL, além de todas as linhas retornadas da associação interna.

### <a name="full-outer"></a>Externa completa

Externa completa produz todas as colunas e linhas de ambos os lados com valores nulos para colunas que são não presentes na outra tabela.

### <a name="cross-join"></a>Associação cruzada

Especifique o produto cruzado de dois fluxos com uma expressão. Pode utilizar este para criar condições de associação personalizado.

## <a name="specify-join-conditions"></a>Especificar as condições de associação

É a condição de associação à esquerda do fluxo de dados ligado à esquerda de sua associação. A condição de aderir à direita é o segundo fluxo de dados ligado à sua associação na parte inferior, que vai ser um conector direto para outro fluxo ou uma referência a outro fluxo.

É necessário introduzir pelo menos 1 condições de associação (1..n). Eles podem ser campos que também são referenciados diretamente, selecionado a partir do menu pendente ou expressões.

## <a name="join-performance-optimizations"></a>Junte-se a otimizações de desempenho

Ao contrário de Merge Join, em ferramentas como o SSIS, a associação no fluxo de dados do ADF não é uma operação de associação de intercalação obrigatório. Por conseguinte, as chaves de associação não é necessário ser ordenada em primeiro lugar. A operação de associação ocorrerá no Spark com o Databricks baseada a operação de associação ideal no Spark: Associação de difusão / lado do mapa:

![Junte-se a transformação otimizar](media/data-flow/joinoptimize.png "Junte-se a otimização")

Se o conjunto de dados se encaixa na memória do nó de trabalho do Databricks, que podemos otimizar o desempenho da sua associação. Também pode especificar a criação de partições dos seus dados sobre a operação de associação para criar conjuntos de dados que se encaixa melhor em memória por função de trabalho.

## <a name="self-join"></a>Self-Join

Pode obter condições de associação automática de mensagens em fila no fluxo de dados do ADF ao usando a transformação Selecione para alias de um fluxo existente. Em primeiro lugar, crie um "novo ramo" a partir de um fluxo e depois adicionar um, selecione alias no todo fluxo original.

![Associação automática](media/data-flow/selfjoin.png "associação automática")

No diagrama acima, a transformação selecione é na parte superior. Todos os está fazendo é aliasing o fluxo original para "OrigSourceBatting". A transformação de associação realçada abaixo dele pode ver que utilizamos esta sequência de alias Selecione como a associação a um direita, permitindo-nos fazer referência a mesma chave no lado esquerdo & no lado direito da associação interna.

## <a name="next-steps"></a>Passos Seguintes

Depois de associar dados, pode então [criar novas colunas](data-flow-derived-column.md) e [seus dados de sink para um arquivo de dados de destino](data-flow-sink.md).
