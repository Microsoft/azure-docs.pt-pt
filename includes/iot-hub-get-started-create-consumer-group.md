---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "66248772"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicione um grupo de consumidores ao seu hub IoT

[Os grupos de consumidores](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) fornecem pontos de vista independentes para o fluxo de eventos que permitem que apps e serviços Azure consumam dados independentemente do mesmo ponto final do Event Hub. Nesta secção, você adiciona um grupo de consumidores ao ponto final incorporado do seu hub IoT que é usado mais tarde neste tutorial para retirar dados do ponto final.

Para adicionar um grupo de consumidores ao seu hub IoT, siga estes passos:

1. No [portal do Azure](https://portal.azure.com/), abra o hub IoT.

2. No painel esquerdo, selecione **pontos finais incorporados,** selecione **Eventos** no painel direito e introduza um nome em **grupos de consumidores**. Selecione **Guardar**.

   ![Crie um grupo de consumidores no seu hub IoT](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
