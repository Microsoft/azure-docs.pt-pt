---
title: Dedupe linhas e encontrar nulos usando cortes de fluxo de dados
description: Aprenda a dedupe facilmente e encontre nulos usando código snippets nos fluxos de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: 1b49b2584c4cb462c7c0f520fe8d1b5bf69c8674
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393671"
---
# <a name="dedupe-rows-and-find-nulls-by-using-data-flow-snippets"></a>Dedupe linhas e encontrar nulos usando cortes de fluxo de dados

[!INCLUDE [appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ao utilizar os snippets de código no mapeamento dos fluxos de dados, pode facilmente executar tarefas comuns, tais como a deduplica de dados e a filtragem nula. Este artigo explica como adicionar facilmente essas funções aos seus oleodutos utilizando os excertos de scripts de fluxo de dados.
<br>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Selecione **Novo Pipeline**.

1. Adicione uma atividade de fluxo de dados.

1. Selecione o separador **Definições de Origem,** adicione uma transformação de fonte e, em seguida, conecte-o a um dos seus conjuntos de dados.

    ![Screenshot do painel "Definições de origem" para adicionar um tipo de fonte.](media/data-flow/snippet-adf-2.png)

    Os cortes de verificação de dedupe e nulos utilizam padrões genéricos que se aproveitam da deriva do esquema de fluxo de dados. Os snippets funcionam com qualquer esquema do seu conjunto de dados, ou com conjuntos de dados que não têm esquema pré-definido.

1. Na secção "Linha Distinta utilizando todas as colunas" do script de fluxo de [dados (DFS),](./data-flow-script.md#distinct-row-using-all-columns)copie o corte de código para DistinctRows.

1. [Vá à página de documentação do Data Flow Script e copie o corte de código para Linhas Distintas.](./data-flow-script.md#distinct-row-using-all-columns)

    ![Screenshot de um corte de fonte.](media/data-flow/snippet-adf-3.png)

1. No seu script, depois da definição para `source1` , hit Enter, e depois colar o corte de código.

1. Efetue um dos seguintes procedimentos:

   * Ligue este corte de código colado à transformação de origem que criou anteriormente no gráfico, digitando **a fonte1** em frente ao código colado.

   * Em alternativa, pode ligar a nova transformação no designer selecionando o fluxo de entrada a partir do novo nó de transformação no gráfico.

     ![Screenshot do painel "Configurações de divisão condicional".](media/data-flow/snippet-adf-4.png)

   Agora, o seu fluxo de dados removerá linhas duplicadas da sua fonte utilizando a transformação agregada, que se agrupe por todas as linhas utilizando um haxixe geral em todos os valores das colunas.
    
1. Adicione um corte de código para dividir os seus dados num fluxo que contém linhas com nulos e outro fluxo sem nulos. Para tal:

1. [Volte para a biblioteca snippet e desta vez copie o código para os cheques NU.](./data-flow-script.md#check-for-nulls-in-all-columns)

   b. No seu designer de fluxo de dados, selecione **Script** novamente e, em seguida, cole este novo código de transformação na parte inferior. Esta ação liga o script à sua transformação anterior colocando o nome dessa transformação em frente ao corte colado.

   O seu gráfico de fluxo de dados deve agora ser semelhante a este:

    ![Screenshot do gráfico de fluxo de dados.](media/data-flow/snippet-adf-1.png)

  Criou agora um fluxo de dados de funcionamento com verificações genéricas de deduping e nulos, retirando os fragmentos de código existentes da biblioteca data flow script e adicionando-os ao seu design existente.

## <a name="next-steps"></a>Passos seguintes

* Construa o resto da sua lógica de fluxo de dados utilizando [transformações](concepts-data-flow-overview.md)de fluxos de dados de mapeamento.