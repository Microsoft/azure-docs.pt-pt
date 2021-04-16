---
title: 'Tutorial: Começar a integrar-se com oleodutos'
description: Neste tutorial, você vai aprender a integrar oleodutos e atividades usando o Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 19bff62883341947eb5290118494b8244c5476ac
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518257"
---
# <a name="integrate-with-pipelines"></a>Integrar-se com oleodutos

Neste tutorial, você vai aprender a integrar oleodutos e atividades usando o Synapse Studio. 

## <a name="create-a-pipeline-and-add-a-notebook-activity"></a>Crie um oleoduto e adicione uma atividade de caderno

1. No Synapse Studio, vá ao centro **de Integração.**
1. Selecione **+**  >  **Pipeline** para criar um novo oleoduto. Clique no novo objeto do gasoduto para abrir o designer pipeline.
1. No âmbito **de Atividades,** expanda a pasta **Synapse** e arraste um objeto **Portátil** para o designer.
1. Selecione o **separador Definições** das propriedades da atividade do Portátil. Utilize a lista de drop-down para selecionar um caderno do seu espaço de trabalho synapse atual.

## <a name="schedule-the-pipeline-to-run-every-hour"></a>Agende o oleoduto para correr a cada hora

1. No oleoduto, **selecione Adicionar gatilho**  >  **Novo/editar**.
1. Em **Escolha o gatilho**, selecione **New**, e desacorda a **Recorrência** para "a cada 1 hora".
1. Selecione **OK**. 
1. Selecione **Publicar Tudo**. 

## <a name="forcing-a-pipeline-to-run-immediately"></a>Forçando um oleoduto a funcionar imediatamente

Uma vez publicado o gasoduto, é melhor executá-lo imediatamente sem esperar uma hora para passar.

1. Abra o oleoduto.
1. Clique **em Adicionar gatilho**  >  **agora**.

## <a name="monitor-pipeline-execution"></a>Monitorizar a execução do gasoduto

1. Vai para o centro do **Monitor.**
1. O **Pipeline select funciona** para monitorizar o progresso da execução do gasoduto.
1. Nesta vista pode alternar entre a **Lista** Tabular apresentar um gráfico gráfico **gantt** gráfico. 
1. Clique num nome de oleoduto para ver o estado das atividades nesse oleoduto.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Visualizar dados com o Power BI](get-started-visualize-power-bi.md)
