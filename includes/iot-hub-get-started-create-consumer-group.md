---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248772"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicionar um grupo de consumidores ao seu hub IoT

[Grupos de consumidores](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) fornecer vistas independentes para o fluxo de eventos que permitem a aplicações e serviços do Azure de forma independente consumir dados a partir do mesmo ponto de final do Hub de eventos. Nesta secção, é possível adicionar um grupo de consumidores para o ponto final incorporado do seu hub IoT que é utilizado mais tarde neste tutorial para extrair os dados do ponto final.

Para adicionar um grupo de consumidores ao seu hub IoT, siga estes passos:

1. No [portal do Azure](https://portal.azure.com/), abra o hub IoT.

2. No painel esquerdo, selecione **pontos finais incorporados**, selecione **eventos** no painel da direita e introduza um nome sob **grupos de consumidores**. Selecione **Guardar**.

   ![Criar um grupo de consumidores do seu hub IoT](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
