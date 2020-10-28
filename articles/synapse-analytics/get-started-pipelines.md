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
ms.date: 10/27/2020
ms.openlocfilehash: af01d5b5e424dd5ea229115f7aa3570d0b7cd511
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744929"
---
# <a name="integrate-with-pipelines"></a>Integrar-se com oleodutos

Neste tutorial, você vai aprender a integrar oleodutos e atividades usando o Synapse Studio. 

## <a name="overview"></a>Descrição geral

Pode integrar uma grande variedade de tarefas no Azure Synapse.

1. No Synapse Studio, vá ao centro **de Integração.**
1. Selecione **+**  >  **Pipeline** para criar um novo oleoduto.
1. Vá ao centro **De Desenvolvimento** e selecione um dos cadernos que criou anteriormente.
1. Arraste esse caderno para o oleoduto **(Nota** : Adicione os módulos de importação passo no caderno conforme especificado no [documento,](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace)que são necessários durante o funcionamento do gasoduto)
1. No oleoduto, **selecione Adicionar gatilho**  >  **Novo/editar** .
1. Em **Escolha o gatilho** , selecione **New** , e desacorda a **Recorrência** para "a cada 1 hora".
1. Selecione **OK** . 
1. Selecione **Publicar Tudo** .
1. Para fazer o gasoduto funcionar imediatamente, sem esperar pela próxima hora, **selecione Adicione o gatilho**  >  **agora** .



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Visualizar dados com o Power BI](get-started-visualize-power-bi.md)
                                 
