---
title: Começar com o fluxo de dados em Azure Data Factory
description: Um tutorial sobre como preparar dados na Azure Data Factory usando fluxo de dados de estrangulamento
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: a180a7a0c85b642ac09d1d027c95809c4638dee1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81408998"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Preparar dados com fluxo de dados de estrangulamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="create-a-wrangling-data-flow"></a>Criar um fluxo de dados de sangling

Existem duas formas de criar um fluxo de dados em Azure Data Factory. Uma maneira é clicar no ícone plus e selecionar **O Fluxo** de Dados no painel de recursos da fábrica.

![Distúrbios](media/wrangling-data-flow/tutorial7.png)

O outro método está no painel de atividades da tela do gasoduto. Abra o acordeão **Move and Transform** e arraste a atividade de fluxo de **dados** para a tela.

Em ambos os métodos, no painel lateral que se abre, selecione **Criar novos fluxos** de dados e escolha o fluxo de **dados de Wrangling**. Clique em OK.

![Distúrbios](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Autor de um fluxo de dados de estrangulamento

Adicione um conjunto de **dados Source** para o seu fluxo de dados de sangling. Pode escolher um conjunto de dados existente ou criar um novo. Também pode selecionar um conjunto de dados de sumidouro. Pode escolher um ou mais conjuntos de dados de origem, mas apenas um lavatório é permitido neste momento. A escolha de um conjunto de dados de sumidouro é opcional, mas é necessário pelo menos um conjunto de dados de origem.

> [!NOTE]
> Apenas o Texto Delimitado ADLS Gen 2 é suportado para pré-visualização limitada. 

![Distúrbios](media/wrangling-data-flow/tutorial4.png)

Clique em **Criar** para abrir o editor de mashup Power Query Online.

![Distúrbios](media/wrangling-data-flow/tutorial5.png)

Autor do fluxo de dados de sangling utilizando a preparação de dados sem código. Para a lista de funções disponíveis, consulte funções de [transformação](wrangling-data-flow-functions.md)/

![Distúrbios](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Funcionamento e monitorização de um fluxo de dados de estrangulamento

Para executar uma execução de depuração de gasoduto de um fluxo de dados de estrangulamento, clique em **Debug** na tela do gasoduto. Assim que publicar o seu fluxo de dados, o Trigger executa **agora** uma execução a pedido do último oleoduto publicado. Os fluxos de dados de contorcer podem ser agendados com todos os gatilhos da Fábrica de Dados Azure existentes.

![Distúrbios](media/wrangling-data-flow/tutorial3.png)

Vá ao separador **Monitor** para visualizar a saída de uma execução de fluxo de fluxo de dados desencadeado.

![Distúrbios](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Passos seguintes

Aprenda a criar um fluxo de [dados de mapeamento.](tutorial-data-flow.md)