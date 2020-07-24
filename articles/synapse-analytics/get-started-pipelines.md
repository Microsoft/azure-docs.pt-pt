---
title: 'Tutorial: Começar a orquestrar com oleodutos'
description: Neste tutorial, você vai aprender como orquestrar oleodutos e atividades usando o Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 5e32a6a9817f2a3176e96e39c5e261875e8f4ed1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101799"
---
# <a name="orchestrate-with-pipelines"></a>Orquestrar com oleodutos

Neste tutorial, você vai aprender como orquestrar oleodutos e atividades usando o Synapse Studio. 

## <a name="overview"></a>Descrição geral

Você pode orquestrar uma grande variedade de tarefas em Azure Synapse.

1. No Estúdio Synapse, vá ao centro **de Orchestrate.**
1. Selecione **+**  >  **Pipeline** para criar um novo oleoduto.
1. Vá ao centro **De desenvolvimento** e encontre o caderno que criou anteriormente.
1. Arraste o caderno para o oleoduto.
1. No oleoduto, **selecione Adicionar gatilho**  >  **Novo/editar**.
1. No **Seletor**Trigger , selecione **Novo**e, em seguida, em **recorrência,** desacorda o gatilho a cada 1 hora.
1. Selecione **OK**.
1. Selecione **Publicar Tudo**. O oleoduto funciona a cada hora.
1. Para fazer o gasoduto funcionar agora, sem esperar pela próxima hora, **selecione Adicionar gatilho**  >  **Novo/editar**.



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Visualizar dados com o Power BI](get-started-visualize-power-bi.md)
                                 
