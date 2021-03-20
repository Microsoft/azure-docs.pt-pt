---
title: Começar com o fluxo de dados na Azure Data Factory
description: Um tutorial sobre como preparar dados na Azure Data Factory usando o fluxo de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: 3d43a532f57feab361c6a3de79269991f46fc55d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684027"
---
# <a name="prepare-data-with-data-wrangling"></a>Preparar dados com a disputa de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A luta de dados na fábrica de dados permite-lhe construir mash-ups de power query interativos nativamente em ADF e, em seguida, executá-los em escala dentro de um oleoduto ADF.

> [!NOTE]
> A acitividade da consulta de energia em ADF é atualmente avilável na pré-visualização pública

## <a name="create-a-power-query-activity"></a>Criar uma atividade de consulta de energia

Existem duas formas de criar uma Consulta de Energia na Azure Data Factory. Uma maneira é clicar no ícone plus e selecionar o **Fluxo de Dados** no painel de recursos da fábrica.

> [!NOTE]
> Anteriormente, a funcionalidade de conflito de dados estava localizada no fluxo de trabalho do fluxo de dados. Agora, você vai construir os seus dados que disputam mash-up de ```New > Power query```

![Screenshot que mostra Power Consultary no painel de recursos da fábrica.](media/data-flow/power-query-wrangling.png)

O outro método está no painel de atividades da tela do gasoduto. Abra o **acordeão de consulta de** potência e arraste a atividade **de consulta de energia** para a tela.

![Screenshot que realça a opção de disputa de dados.](media/data-flow/power-query-activity.png)

## <a name="author-a-power-query-data-wrangling-activity"></a>Autor de uma atividade de conflito de dados de consulta de energia

Adicione um **conjunto de dados Source** para o seu mash-up de consulta de energia. Pode escolher um conjunto de dados existente ou criar um novo. Também pode selecionar um conjunto de dados de pia. Pode escolher um ou mais conjuntos de dados de origem, mas apenas uma pia é permitida neste momento. Escolher um conjunto de dados de lavatório é opcional, mas pelo menos um conjunto de dados de origem é necessário.

![Brigas](media/wrangling-data-flow/tutorial4.png)

Clique em **Criar** para abrir o editor de mashup online da Power Query.

![Screenshot que mostra o botão Criar que abre o editor de mashup online da Power Query Online.](media/wrangling-data-flow/tutorial5.png)

Autore a sua consulta de energia de discussão utilizando a preparação de dados sem código. Para a lista de funções disponíveis, consulte [as funções de transformação](wrangling-functions.md). A ADF traduz o script M num script de fluxo de dados para que possa executar a sua Consulta de Energia em escala utilizando o ambiente de fluxo de dados Azure Data Factory Spark.

![Screenshot que mostra o processo de autoria dos seus dados que disputam a Consulta de Energia.](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-power-query-data-wrangling-activity"></a>Executar e monitorizar uma atividade de conflito de dados de consulta de energia

Para executar uma execução de depurar o gasoduto de uma atividade de consulta de energia, clique em **Debug** na tela do gasoduto. Assim que publicar o seu oleoduto, **o Trigger** executa agora uma execução a pedido do último oleoduto publicado. Os gasodutos de consulta de energia podem ser programados com todos os gatilhos existentes da Azure Data Factory.

![Screenshot que mostra como adicionar uma atividade de conflito de dados de consulta de energia.](media/wrangling-data-flow/tutorial3.png)

Vá ao **separador Monitor** para visualizar a saída de uma atividade de consulta de energia desencadeada.

![Screenshot que mostra a saída de uma atividade de consulta de energia desencadeada.](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Passos seguintes

Saiba como [criar um fluxo de dados de mapeamento.](tutorial-data-flow.md)
