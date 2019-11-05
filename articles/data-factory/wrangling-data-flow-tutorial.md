---
title: Introdução ao fluxo de dados do Wrangling no Azure Data Factory | Microsoft Docs
description: Um tutorial sobre como preparar dados no Azure Data Factory usando o fluxo de dados do Wrangling
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 6de6f6976643360613b7bd44d8cb390c409e33e1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518531"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Preparar dados com o fluxo de dados do Wrangling

## <a name="create-a-wrangling-data-flow"></a>Criar um fluxo de dados Wrangling

Há duas maneiras de criar um fluxo de dados Wrangling em Azure Data Factory. Uma delas é clicar no ícone de adição e selecionar o **fluxo de dados** no painel recursos de fábrica.

![Wrangling](media/wrangling-data-flow/tutorial7.png)

O outro método está no painel atividades da tela do pipeline. Abra o adparador de **movimentação e transformação** e arraste a atividade **fluxo de dados** para a tela.

Em ambos os métodos, no painel lateral que é aberto, selecione **criar novo fluxo de dados** e escolha **Wrangling fluxo de dados**. Clique em OK.

![Wrangling](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Criar um fluxo de dados do Wrangling

Adicione um **conjunto** de dados de origem para seu fluxo de data Wrangling. Você pode escolher um conjunto de um existente ou criar um novo. Você também pode selecionar um conjunto de um banco de uma coletor. Você pode escolher um ou mais conjuntos de data de origem, mas apenas um coletor é permitido neste momento. A escolha de um conjunto de coletas é opcional, mas pelo menos um conjunto de fonte de origem é necessário.

> [!NOTE]
> Somente o texto delimitado ADLS Gen 2 tem suporte para visualização limitada. 

![Wrangling](media/wrangling-data-flow/tutorial4.png)

Clique em **criar** para abrir o Power Query editor de mashup online.

![Wrangling](media/wrangling-data-flow/tutorial5.png)

Crie seu fluxo de dados do Wrangling usando a preparação de dados sem código. Para obter a lista de funções disponíveis, consulte [funções de transformação](wrangling-data-flow-functions.md)/

![Wrangling](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Executando e monitorando um fluxo de dados do Wrangling

Para executar uma depuração de pipeline de execução de um fluxo de dados Wrangling, clique em **depurar** na tela do pipeline. Depois de publicar o fluxo de dados, o **gatilho agora** executa uma execução sob demanda do último pipeline publicado. Os fluxos de dados do Wrangling podem ser agendados com todos os gatilhos de Azure Data Factory existentes.

![Wrangling](media/wrangling-data-flow/tutorial3.png)

Vá para a guia **Monitor** para visualizar a saída de uma execução de atividade de fluxo de dados Wrangling disparada.

![Wrangling](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Passos seguintes

Saiba como [criar um fluxo de dados de mapeamento](tutorial-data-flow.md).