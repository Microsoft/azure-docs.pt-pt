---
title: Começar com o fluxo de dados na Azure Data Factory
description: Um tutorial sobre como preparar dados na Azure Data Factory usando o fluxo de dados
author: dcstwh
ms.author: weetok
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 5ee1069ca24dc16a52d30450c0f2163130f549cb
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494907"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Preparar dados com o fluxo de dados de estrangulamento

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

> [!NOTE]
> O fluxo de dados de estrangulamento é atualmente avilável na visualização pública

## <a name="create-a-wrangling-data-flow"></a>Criar um fluxo de dados de estrangulamento

Há duas formas de criar um fluxo de dados na Azure Data Factory. Uma maneira é clicar no ícone plus e selecionar o **Fluxo de Dados** no painel de recursos da fábrica.

![Screenshot que mostra Data Flow no painel de recursos de fábrica.](media/wrangling-data-flow/tutorial7.png)

O outro método está no painel de atividades da tela do gasoduto. Abra o **acordeão Move and Transform** e arraste a atividade **de fluxo de dados** para a tela.

Em ambos os métodos, no painel lateral que se abre, **selecione Criar um novo fluxo de dados** e escolher o fluxo de **dados de Wrangling**. Clique em OK.

![Screenshot que realça a opção de fluxo de dados wrangling.](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Autor de um fluxo de dados de estrangulamento

Adicione um **conjunto de dados Source** para o fluxo de dados de problemas. Pode escolher um conjunto de dados existente ou criar um novo. Também pode selecionar um conjunto de dados de pia. Pode escolher um ou mais conjuntos de dados de origem, mas apenas uma pia é permitida neste momento. Escolher um conjunto de dados de lavatório é opcional, mas pelo menos um conjunto de dados de origem é necessário.

> [!NOTE]
> Apenas o Texto Delimitado ADLS Gen 2 é suportado para pré-visualização limitada. 

![Brigas](media/wrangling-data-flow/tutorial4.png)

Clique em **Criar** para abrir o editor de mashup online da Power Query.

![Screenshot que mostra o botão Criar que abre o editor de mashup online da Power Query Online.](media/wrangling-data-flow/tutorial5.png)

Autore o fluxo de dados de luta utilizando a preparação de dados sem código. Para a lista de funções disponíveis, consulte [as funções de transformação](wrangling-data-flow-functions.md).

![Screenshot que mostra o processo de autoria do fluxo de dados de luta.](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Executar e monitorizar um fluxo de dados de estrangulamento

Para executar uma execução de depurar o gasoduto de um fluxo de dados de distúrbios, clique em **Debug** na tela do gasoduto. Assim que publicar o fluxo de dados, **o Trigger** executa agora uma execução a pedido do último oleoduto publicado. Os fluxos de dados de estrangulamento podem ser programados com todos os gatilhos existentes da Azure Data Factory.

![Screenshot que mostra como adicionar um fluxo de dados de estrangulamento.](media/wrangling-data-flow/tutorial3.png)

Vá ao **separador Monitor** para visualizar a saída de uma atividade de fluxo de dados desencadeada.

![Screenshot que mostra a saída de uma atividade de fluxo de dados desencadeada.](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Passos seguintes

Saiba como [criar um fluxo de dados de mapeamento.](tutorial-data-flow.md)