---
title: incluir ficheiro
description: incluir ficheiro
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e22acc697e837bab91c8b9c32c1fe35f1a7bce1c
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56824526"
---
Esta secção descreve como criar um hub IoT com o [portal do Azure](https://portal.azure.com).

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 

2. Escolher +**criar um recurso**, em seguida, escolha **Internet das coisas**.

3. Clique em **Iot Hub** da lista à direita. Veja a primeira tela para criar um hub IoT.

   ![Captura de ecrã que mostra a criação de um hub no portal do Azure](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png)

   Preencha os campos.

   **Subscrição**: Selecione a subscrição a utilizar para o seu hub IoT.

   **Grupo de recursos**: Pode criar um novo grupo de recursos ou utilize um já existente. Para criar um novo, clique em **criar novo** e preencha o nome que pretende utilizar. Para utilizar um grupo de recursos existente, clique em **utilizar existente** e selecione o grupo de recursos na lista pendente. Para obter mais informações, consulte [grupos de recursos de gerir o Azure Resource Manager](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   **Região**: Esta é a região na qual pretende que o seu hub a ser localizado. Selecione a localização mais próxima de si na lista pendente.

   **Nome do IoT Hub**: Coloque o nome do hub IoT. Este nome tem de ser globalmente exclusivo. Se o nome introduzido estiver disponível, aparece uma marca de verificação verde.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

4. Clique em **seguinte: Tamanho e a escala** para continuar a criar o hub IoT.

   ![Captura de ecrã que mostra o tamanho de definição e a escala para um novo hub IoT no portal do Azure](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

   Neste ecrã, pode obter os padrões e basta clicar **rever + criar** na parte inferior. 

   **Escalão de preço e escala**: Pode escolher entre vários escalões dependendo de quantos recursos pretender e o número de mensagens enviam através da sua solução por dia. O escalão gratuito destina-se para teste e avaliação. Ele permite que os 500 dispositivos de estar ligados ao IoT hub e até 8000 mensagens por dia. Cada subscrição do Azure pode criar um IoT Hub no escalão gratuito. 

   **Unidades do IoT Hub**: O número de mensagens permitidos por unidade por dia depende do escalão de preço do seu hub. Por exemplo, se pretender que o hub IoT para oferecer suporte a uma entrada de 700.000 mensagens, escolher duas unidades de escalão S1.

   Para obter detalhes sobre as outras opções de escalão, consulte [escolher o escalão certo do IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

   **Partições avançadas / dispositivo-para-cloud**: Esta propriedade está relacionado com as mensagens do dispositivo para a cloud para o número de leitores simultâneos das mensagens. A maioria dos hubs de IoT precisam apenas quatro partições. 

5. Clique em **rever + criar** para rever as opções. Verá algo semelhante a este ecrã.

   ![Captura de ecrã rever informações para a criação de novo hub IoT](./media/iot-hub-include-create-hub/iot-hub-create-review.png)

6. Clique em **criar** para criar o seu hub IoT novo. A criar o hub demora alguns minutos.
