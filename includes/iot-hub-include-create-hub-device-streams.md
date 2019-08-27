---
title: incluir arquivo (visualização de fluxos de dispositivo)
description: incluir arquivo (visualização de fluxos de dispositivo)
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/14/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a02c16a75bbdf4827ce6a6c5f50751caf9c5aab8
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "67445997"
---
Esta seção descreve como criar um hub IoT usando o [portal do Azure](https://portal.azure.com).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **criar um recurso**e, em seguida, selecione **Internet das coisas**.

1. Na lista à direita, selecione **Hub IOT**. A primeira página para a criação de um hub IoT é aberta.

   ![Criando um hub IoT no portal do Azure](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-01.png)

   Preencha os campos:

   a. Na lista suspensa **assinatura** , selecione a assinatura a ser usada para o Hub IOT.

   b. Para **grupo de recursos**, siga um destes procedimentos: 
      * Para criar um novo grupo de recursos, selecione **criar novo** e insira o nome que você deseja usar. 
      * Para usar um grupo de recursos existente, selecione **usar existente** e, em seguida, na lista suspensa, selecione o grupo de recursos. 
      
        Para obter mais informações, consulte [Manage Azure Resource Manager Resource groups](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   c. Na lista suspensa **região** , selecione a região na qual você deseja que o Hub seja localizado. Selecione uma região que dê suporte à visualização de fluxos de dispositivo do Hub IoT, seja **EUA Central** ou **EUA Central EUAP**.

   d. Na caixa **nome do Hub IOT** , digite o nome do Hub IOT. O nome tem de ser globalmente exclusivo. Se o nome introduzido estiver disponível, aparece uma marca de verificação verde.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. Para continuar criando o Hub IOT, selecione **avançar: Tamanho e escala**.

   ![Definindo o tamanho e a escala de um novo hub IoT usando o portal do Azure](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   Nesse painel, você pode aceitar as configurações padrão e selecionar revisar **+ criar** na parte inferior. Considere as seguintes opções:

   * Na lista suspensa **preço e camada de escala** , selecione uma das camadas padrão (**S1**, **S2**ou **S3**) ou **F1: Camada**gratuita. Essa opção também pode ser guiada pelo tamanho de sua frota e pelas cargas de trabalho que não são de streaming que você espera em seu hub (por exemplo, mensagens de telemetria). Por exemplo, a camada gratuita destina-se a testes e avaliação. Ele permite que os dispositivos 500 sejam conectados ao Hub IoT e até 8.000 mensagens por dia. Cada assinatura do Azure pode criar um hub IoT na camada gratuita. 

   * Para o **número de unidades do Hub IOT**: Essa opção depende da carga de trabalho que não é de streaming que você espera em seu hub. Você pode selecionar 1 por enquanto.

   Para obter mais informações sobre as opções de camada, consulte [escolher a camada certa do Hub IOT](../articles/iot-hub/iot-hub-scaling.md).

1. Para revisar suas escolhas, selecione a guia revisar **+ criar** . O painel que é aberto é semelhante ao seguinte:

   ![Informações para criar o novo hub IoT](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. Para criar seu novo hub IoT, selecione **criar**. O processo leva alguns minutos.
