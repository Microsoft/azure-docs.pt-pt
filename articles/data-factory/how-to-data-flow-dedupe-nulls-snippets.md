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
ms.openlocfilehash: 841484a647d2737d621c75ebe63f65f2de829a26
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666509"
---
# <a name="dedupe-rows-and-find-nulls-using-data-flow-snippets"></a>Dedupe linhas e encontrar nulos usando cortes de fluxo de dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ao utilizar os snippets de código no mapeamento dos fluxos de dados, pode facilmente executar tarefas comuns como a deduplicação de dados e a filtragem nula. Este guia de como adicionar essas funções aos seus oleodutos muito facilmente usando os excertos de scripts de fluxo de dados.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Selecione **+Novo Pipeline** para criar um novo oleoduto.

2. Adicione uma atividade de fluxo de dados.

3. Adicione uma transformação de Fonte e conecte-a a um dos seus conjuntos de dados

    ![Fonte Snippet 2](media/data-flow/snippet-adf-2.png)

4. Os snippets de verificação de dedupe e NULL utilizam padrões genéricos que alavancam a deriva do esquema de fluxo de dados para que funcionem com qualquer esquema do seu conjunto de dados, ou com conjuntos de dados que não tenham nenhum esquema pré-definido.

5. [Vá à página de documentação do Data Flow Script e copie o corte de código para Linhas Distintas.](https://docs.microsoft.com/azure/data-factory/data-flow-script#distinct-row-using-all-columns)

6. Na UI do seu designer de fluxo de dados, clique no botão Script no topo direito para abrir o editor de script por trás do gráfico de fluxo de dados.

    ![Fonte Snippet 3](media/data-flow/snippet-adf-3.png)

7. Depois da definição para ```source1``` o seu script, acerte Enter e, em seguida, cole no corte de código.

8. Irá ligar este código colado à transformação anterior de Fonte que criou no gráfico digitando "source1" em frente ao código colado.

9. Em alternativa, pode ligar a nova transformação no designer selecionando o fluxo de entrada a partir do novo nó de transformação no gráfico.

    ![Fonte Snippet 4](media/data-flow/snippet-adf-4.png)

10. Agora, o seu fluxo de dados removerá linhas duplicadas da sua fonte usando a transformação agregada que agrupe por todas as linhas usando um hash geral em todos os valores da coluna.
    
11. Em seguida, vamos adicionar um corte de código para dividir os seus dados em um fluxo que contém linhas com NULLs e um fluxo que não tem nenhum NULLs.

12. [Volte para a biblioteca snippet e desta vez copie o código para os cheques NU.](https://docs.microsoft.com/azure/data-factory/data-flow-script#check-for-nulls-in-all-columns)

13. No seu designer de fluxo de dados, clique novamente em Script e cole este novo código de transformação na parte inferior, ligando-o à sua transformação anterior, digitando o nome dessa transformação em frente ao corte colado.

14. O seu gráfico de fluxo de dados deve agora ser semelhante a este:

    ![Fonte Snippet 1](media/data-flow/snippet-adf-1.png)

  Tem agora um fluxo de dados de funcionamento com verificações genéricas de deduping e NUS, retirando os fragmentos de código existentes da biblioteca data flow script e adicionando-os ao seu design existente.

## <a name="next-steps"></a>Passos seguintes

* Construa o resto da sua lógica de fluxo de dados utilizando [transformações](concepts-data-flow-overview.md)de fluxos de dados de mapeamento.
