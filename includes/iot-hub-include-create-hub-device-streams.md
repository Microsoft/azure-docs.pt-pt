---
title: Incluir ficheiro (fluxos de dispositivos pré-visualização)
description: Incluir ficheiro (fluxos de dispositivos pré-visualização)
author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/14/2019
ms.author: rezas
ms.custom: include file
ms.openlocfilehash: 93c71fa8b0c39cc16d2a8e24472e8d68717a6c32
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733274"
---
Esta secção descreve como criar um hub IoT com o [portal do Azure](https://portal.azure.com).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **criar um recurso**e, em seguida, selecione **Internet das coisas**.

1. Na lista à direita, selecione **Iot Hub**. É aberta a página de primeira para a criação de um hub IoT.

   ![Criar um hub IoT no portal do Azure](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-01.png)

   Preencha os campos:

   a. Na **subscrição** pendente, selecione a subscrição a utilizar para o seu hub IoT.

   b. Para **grupo de recursos**, efetue um dos seguintes procedimentos: 
      * Para criar um novo grupo de recursos, selecione **criar novo** e introduza o nome que pretende utilizar. 
      * Para utilizar um grupo de recursos existente, selecione **utilizar existente** e, em seguida, na lista pendente, selecione o grupo de recursos. 
      
        Para obter mais informações, consulte [grupos de recursos de gerir o Azure Resource Manager](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   c. Na **região** pendente, selecione a região na qual pretende que o seu hub a ser localizado. Selecione uma região que suporta a visualização de fluxos de dispositivo do IoT Hub, seja **EUA Central** ou **E.u.a. Central EUAP**.

   d. Na **nome do Hub IoT** , introduza o nome do hub IoT. O nome tem de ser globalmente exclusivo. Se o nome introduzido estiver disponível, aparece uma marca de verificação verde.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. Para continuar a criar o hub IoT, selecione **seguinte: Tamanho e a escala**.

   ![Definir o tamanho e escala para um novo hub IoT no portal do Azure](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   Neste painel, pode aceitar as predefinições e selecione **rever + criar** na parte inferior. Considere as seguintes opções:

   * Na **escalão de preços e dimensionamento** na lista pendente, selecione um dos escalões standard (**S1**, **S2**, ou **S3**) ou **F1: Escalão gratuito**. Esta opção também pode ser orientada pelo tamanho da sua frota e as cargas de trabalho sem transmissão que esperava no seu hub (por exemplo, mensagens de telemetria). Por exemplo, o escalão gratuito destina-se para teste e avaliação. Ele permite que os 500 dispositivos de estar ligados ao IoT hub e até 8000 mensagens por dia. Cada subscrição do Azure pode criar um hub IoT no escalão gratuito. 

   * Para **unidades do número do IoT Hub**: Essa escolha depende da carga de trabalho sem transmissão que esperar do seu hub. Pode selecionar 1 por agora.

   Para obter mais informações sobre as opções de escalão, consulte [escolha o escalão do hub IoT certo](../articles/iot-hub/iot-hub-scaling.md).

1. Para rever as opções, selecione o **rever + criar** separador. O painel que se abre é semelhante ao seguinte:

   ![Informações para a criação do novo hub IoT](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. Para criar o seu hub IoT novo, selecione **criar**. O processo demora alguns minutos.
