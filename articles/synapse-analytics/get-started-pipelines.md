---
title: 'Tutorial: Começar a orquestrar com oleodutos'
description: Neste tutorial, você vai aprender como orquestrar oleodutos e atividades usando o Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 42d2ac6cf6592f8e22b0a66aee84c3436d466572
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329888"
---
# <a name="orchestrate-with-pipelines"></a>Orquestrar com oleodutos

Neste tutorial, você vai aprender como orquestrar oleodutos e atividades usando o Synapse Studio. 

## <a name="overview"></a>Descrição geral

Você pode orquestrar uma grande variedade de tarefas em Azure Synapse.

1. No Synapse Studio, vá ao centro **de Integração.**
1. Selecione **+**  >  **Pipeline** para criar um novo oleoduto.
1. Vá ao centro **De Desenvolvimento** e selecione um dos cadernos que criou anteriormente.
1. Arraste esse caderno para o oleoduto **(Nota:** Adicione os módulos de importação passo no caderno conforme especificado no [documento](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace) que são necessários durante o funcionamento do gasoduto)
1. No oleoduto, **selecione Adicionar gatilho**  >  **Novo/editar**.
1. Em **Escolha o gatilho**, selecione **New**, e desacorda a **Recorrência** para "a cada 1 hora".
1. Selecione **OK**. 
1. Selecione **Publicar Tudo**.
1. Para fazer o gasoduto funcionar imediatamente, sem esperar pela próxima hora, **selecione Adicione o gatilho**  >  **agora**.



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Visualizar dados com o Power BI](get-started-visualize-power-bi.md)
                                 
