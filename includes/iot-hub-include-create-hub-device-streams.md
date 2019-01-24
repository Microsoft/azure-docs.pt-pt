---
title: Incluir ficheiro (fluxos de dispositivos pré-visualização)
description: Incluir ficheiro (fluxos de dispositivos pré-visualização)
author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 01/15/2019
ms.author: rezas
ms.custom: include file
ms.openlocfilehash: 53d8df7e2366cfbf2f62e79fc99c8ef2f9b48ba1
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830246"
---
Esta secção descreve como criar um hub IoT com o [portal do Azure](https://portal.azure.com).

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 

2. Escolher +**criar um recurso**, em seguida, escolha **Internet das coisas**.

3. Clique em **Iot Hub** da lista à direita. Veja a primeira tela para criar um hub IoT.

   ![Captura de ecrã que mostra a criação de um hub no portal do Azure](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-1.png)

   Preencha os campos:

   **Subscrição**: Selecione a subscrição a utilizar para o seu hub IoT.

   **Grupo de recursos**: Pode criar um novo grupo de recursos ou utilize um já existente. Para criar um novo, clique em **criar novo** e preencha o nome que pretende utilizar. Para utilizar um grupo de recursos existente, clique em **utilizar existente** e selecione o grupo de recursos na lista pendente. Para obter mais informações, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../articles/azure-resource-manager/resource-group-portal.md).

   **Região**: Esta é a região na qual pretende que o seu hub a ser localizado. Certifique-se de que seleciona uma região suportada (por exemplo, centro dos E.U.A. ou E.u.a. Central EUAP).

   **Nome do IoT Hub**: Coloque o nome do hub IoT. Este nome tem de ser globalmente exclusivo. Se o nome introduzido estiver disponível, aparece uma marca de verificação verde.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

4. Clique em **seguinte: Tamanho e a escala** para continuar a criar o hub IoT.

   ![Captura de ecrã que mostra o tamanho de definição e a escala para um novo hub IoT no portal do Azure](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-2-free.png)


   Neste ecrã, pode obter os padrões e basta clicar **rever + criar** na parte inferior. 

   **Escalão de preço e escala**: Certifique-se de que seleciona uma standard (S1, S2, S3) ou o escalão gratuito (F1). Esta opção também pode ser orientada pelo tamanho da sua frota e as cargas de trabalho sem transmissão que esperar do seu hub (por exemplo, mensagens de telemetria). Por exemplo, o escalão gratuito destina-se para teste e avaliação. Ele permite que os 500 dispositivos de estar ligados ao IoT hub e até 8000 mensagens por dia. Cada subscrição do Azure pode criar um IoT Hub no escalão gratuito. 

   **Unidades do IoT Hub**: Essa escolha depende da carga de trabalho sem transmissão que esperar do seu hub - pode selecionar 1 por agora.

   Para obter detalhes sobre as outras opções de escalão, consulte [escolher o escalão certo do IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

5. Clique em **rever + criar** para rever as opções. Verá algo semelhante a este ecrã.

   ![Captura de ecrã rever informações para a criação de novo hub IoT](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-3-free.png)

6. Clique em **criar** para criar o seu hub IoT novo. A criar o hub demora alguns minutos.
