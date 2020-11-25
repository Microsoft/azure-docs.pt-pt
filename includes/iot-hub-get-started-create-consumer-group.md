---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: fbb4e53e0047b9768a70c01aecfb7f31ae213b3f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027398"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicione um grupo de consumidores ao seu hub IoT

[Os grupos de consumidores fornecem vistas independentes](../articles/event-hubs/event-hubs-features.md#event-consumers) sobre o fluxo de eventos que permitem que as aplicações e os serviços Azure consumam dados independentes a partir do mesmo ponto final do Event Hub. Nesta secção, adicione um grupo de consumidores ao ponto final incorporado do seu hub IoT que é usado mais tarde neste tutorial para retirar dados do ponto final.

Para adicionar um grupo de consumidores ao seu hub IoT, siga estes passos:

1. No [portal do Azure](https://portal.azure.com/), abra o hub IoT.

2. No painel esquerdo, selecione **pontos finais incorporados,** selecione **Eventos** no painel direito e introduza um nome nos **grupos de consumidores**. Selecione **Guardar**.

   ![Crie um grupo de consumidores no seu hub IoT](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)