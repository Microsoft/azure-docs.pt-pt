---
title: Azure Data Factory transformação junção de fluxo de dados
description: Azure Data Factory transformação junção de fluxo de dados
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: da6c3c90ebbeffcf468aad3809da097976d8ef0d
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387226"
---
# <a name="mapping-data-flow-join-transformation"></a>Mapeando a transformação junção de fluxo de dados



Use Join para combinar dados de duas tabelas em seu fluxo de dados. Clique na transformação que será a relação à esquerda e adicione uma transformação de junção da caixa de ferramentas. Dentro da transformação de junção, você selecionará outro fluxo de dados do seu fluxo de dados para ser a relação certa.

![Transformação de junção](media/data-flow/join.png "Associar")

## <a name="join-types"></a>Tipos de junção

A seleção do tipo de junção é necessária para a transformação junção.

### <a name="inner-join"></a>Junção interna

A junção interna passará apenas por linhas que correspondam às condições de coluna de ambas as tabelas.

### <a name="left-outer"></a>Externa esquerda

Todas as linhas do fluxo à esquerda que não atendem à condição de junção são passadas e as colunas de saída da outra tabela são definidas como NULL, além de todas as linhas retornadas pela junção interna.

### <a name="right-outer"></a>Externa direita

Todas as linhas do fluxo à direita que não atendem à condição de junção são passadas e as colunas de saída que correspondem à outra tabela são definidas como NULL, além de todas as linhas retornadas pela junção interna.

### <a name="full-outer"></a>Externo completo

Externo completo produz todas as colunas e linhas de ambos os lados com valores nulos para colunas que não estão presentes na outra tabela.

### <a name="cross-join"></a>Junção cruzada

Especifique o produto cruzado dos dois fluxos com uma expressão. Você pode usar isso para criar condições de junção personalizadas.

## <a name="specify-join-conditions"></a>Especificar condições de junção

A condição de junção à esquerda é do fluxo de dados conectado à esquerda de seu ingresso. A condição de junção correta é o segundo fluxo de dados conectado à sua junção na parte inferior, que será um conector direto para outro fluxo ou uma referência a outro fluxo.

Você precisa inserir pelo menos uma (1.. n) condições de junção. Eles podem ser campos que são referenciados diretamente, selecionados no menu suspenso ou expressões.

## <a name="join-performance-optimizations"></a>Unir otimizações de desempenho

Ao contrário da junção de mesclagem em ferramentas como o SSIS, a junção no fluxo de dados do ADF não é uma operação de junção de mesclagem obrigatória. Portanto, as chaves de junção não precisam ser classificadas primeiro. A operação de junção ocorrerá com base na operação de junção ideal no Spark: difusão/junção no lado do mapa:

![Transformação de junção otimizar](media/data-flow/joinoptimize.png "Otimização de junção")

Se o conjunto de seus conjuntos de trabalho puder se ajustar à memória do nó do trabalhador, podemos otimizar seu desempenho de junção. Você também pode especificar o particionamento de seus dados na operação de junção para criar conjuntos de dados que podem se ajustar melhor à memória por trabalhador.

## <a name="self-join"></a>Auto-associação

Você pode obter condições de autojunção no fluxo de dados do ADF usando a transformação selecionar para alias de um fluxo existente. Primeiro, crie uma "nova ramificação" de um fluxo e, em seguida, adicione uma seleção para criar um alias do fluxo original inteiro.

![Auto-associação](media/data-flow/selfjoin.png "Auto-associação")

No diagrama acima, a transformação selecionar está na parte superior. Tudo o que está fazendo é fazer o alias do fluxo original para "OrigSourceBatting". Na transformação junção realçada abaixo dela, você pode ver que usamos essa transmissão de alias Select como a junção à direita, permitindo que possamos fazer referência à mesma chave tanto no lado esquerdo & direito da junção interna.

## <a name="composite-and-custom-keys"></a>Chaves compostas e personalizadas

Você pode criar chaves compostas e personalizadas em tempo real dentro da transformação de junção. Adicione linhas para colunas de junção adicionais com o sinal de adição (+) ao lado de cada linha de relação. Ou Compute um novo valor de chave no construtor de expressões para um valor de junção em tempo real.

## <a name="next-steps"></a>Passos seguintes

Depois de unir os dados, você pode [criar novas colunas](data-flow-derived-column.md) e [coletar seus dados para um armazenamento de dados de destino](data-flow-sink.md).
