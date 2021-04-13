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
ms.openlocfilehash: 22631cfd872ed226fc78a97d38c423fbc300f78e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304719"
---
# <a name="integrate-with-pipelines"></a>Integrar-se com oleodutos

Neste tutorial, você vai aprender a integrar oleodutos e atividades usando o Synapse Studio. 

## <a name="create-a-pipeline-and-add-a-notebook-activity"></a>Crie um oleoduto e adicione uma atividade de caderno



1. No Synapse Studio, vá ao centro **de Integração.**
1. Selecione **+**  >  **Pipeline** para criar um novo oleoduto. Clique no novo objeto do gasoduto para abrir o designer pipeline.
1. No âmbito **de Atividades,** expanda a pasta **Synapse** e arraste um objeto **Portátil** para o designer.
1. Selecione o **separador Definições** das propriedades da atividade do Portátil. Utilize a lista de drop-down para selecionar qualquer caderno do seu espaço de trabalho synapse atual.

## <a name="schedule-the-pipeline-to-run-every-hour"></a>Agende o oleoduto para correr a cada hora

1. No oleoduto, **selecione Adicionar gatilho**  >  **Novo/editar**.
1. Em **Escolha o gatilho**, selecione **New**, e desacorda a **Recorrência** para "a cada 1 hora".
1. Selecione **OK**. 
1. Selecione **Publicar Tudo**. 


## <a name="monitor-pipeline"></a>Monitorizar o pipeline

1. Uma vez publicado o gasoduto, para fazer o gasoduto funcionar imediatamente, sem esperar pela hora seguinte, **selecione Adicione o gatilho**  >  **agora**.
1. No Synapse Studio, vá ao **centro** monitor, e selecione **Pipeline corre** para monitorizar o progresso da execução do gasoduto.



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Visualizar dados com o Power BI](get-started-visualize-power-bi.md)
