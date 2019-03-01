---
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 242f601ced4838a1b4e559774c25d05de04ddb77
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57016360"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicionar um grupo de consumidores ao seu hub IoT

Grupos de consumidores são utilizados por aplicações para obter dados do IoT Hub do Azure. Neste tutorial, vai criar um grupo de consumidores a ser utilizado por um serviço do Azure próximo para ler dados a partir do seu hub IoT.

Para adicionar um grupo de consumidores ao seu hub IoT, siga estes passos:

1. No [portal do Azure](https://portal.azure.com/), abra o hub IoT.

2. No painel esquerdo, clique em **pontos finais incorporados**, selecione **eventos** no painel superior e introduza um nome sob **grupos de consumidores** no painel direito. Clique em **salvar** depois de alterar a **TTL predefinido** valor e retorná-lo novamente para o valor original.

   ![Criar um grupo de consumidores do seu hub IoT](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
