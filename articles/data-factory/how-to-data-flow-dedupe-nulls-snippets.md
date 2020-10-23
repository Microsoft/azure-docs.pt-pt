---
title: Dedupe linhas e encontrar nulos usando cortes de fluxo de dados
description: Aprenda a dedupe facilmente e encontre nulos usando código snippets nos fluxos de dados
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: makromer
ms.openlocfilehash: 1c630cdd66fa4f8e609524feb9c3f0bcad9711a0
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458171"
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

1. Na secção "Linha Distinta utilizando todas as colunas" do script de fluxo de [dados (DFS),](https://docs.microsoft.com/azure/data-factory/data-flow-script#distinct-row-using-all-columns)copie o corte de código para DistinctRows.

1. Na UI do seu designer de fluxo de dados, selecione o botão **Script** no canto superior direito para abrir o editor de script por trás do gráfico de fluxo de dados.

    ![Screenshot de um corte de fonte.](media/data-flow/snippet-adf-3.png)

1. No seu script, depois da definição para `source1` , hit Enter, e depois colar o corte de código.

1. Efetue um dos seguintes procedimentos:

   * Ligue este corte de código colado à transformação de origem que criou anteriormente no gráfico, digitando **a fonte1** em frente ao código colado.

   * Em alternativa, pode ligar a nova transformação no designer selecionando o fluxo de entrada a partir do novo nó de transformação no gráfico.

     ![Screenshot do painel "Configurações de divisão condicional".](media/data-flow/snippet-adf-4.png)

   Agora, o seu fluxo de dados removerá linhas duplicadas da sua fonte utilizando a transformação agregada, que se agrupe por todas as linhas utilizando um haxixe geral em todos os valores das colunas.
    
1. Adicione um corte de código para dividir os seus dados num fluxo que contém linhas com nulos e outro fluxo sem nulos. Para tal:

   a. Volte para a [biblioteca de snippets](https://docs.microsoft.com/azure/data-factory/data-flow-script#check-for-nulls-in-all-columns)e, em seguida, copie o código para os cheques nulos.

   b. No seu designer de fluxo de dados, selecione **Script** novamente e, em seguida, cole este novo código de transformação na parte inferior. Esta ação liga o script à sua transformação anterior colocando o nome dessa transformação em frente ao corte colado.

   O seu gráfico de fluxo de dados deve agora ser semelhante a este:

    ![Screenshot do gráfico de fluxo de dados.](media/data-flow/snippet-adf-1.png)

  Criou agora um fluxo de dados de funcionamento com verificações genéricas de deduping e nulos, retirando os fragmentos de código existentes da biblioteca data flow script e adicionando-os ao seu design existente.

## <a name="next-steps"></a>Passos seguintes

* Construa o resto da sua lógica de fluxo de dados utilizando [transformações](concepts-data-flow-overview.md)de fluxos de dados de mapeamento.
