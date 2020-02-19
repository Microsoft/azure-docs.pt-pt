---
title: incluir ficheiro
description: incluir ficheiro
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/14/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: cc53f7ae6eb6254eaa05bf643ecfa0188650df95
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461871"
---
Esta secção descreve como criar um hub IoT utilizando o [portal Azure](https://portal.azure.com).

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. A partir da página inicial do Azure, selecione o **botão + Criar um botão de recurso** e, em seguida, introduza *o IoT Hub* no campo Search the **Marketplace.**

1. Selecione **IoT Hub** a partir dos resultados da pesquisa e, em seguida, selecione **Criar**.

1. No separador **Basics,** preencha os campos da seguinte forma:

   - **Subscrição**: Selecione a subscrição a utilizar para o seu hub.

   - **Grupo de Recursos**: Selecione um grupo de recursos ou crie um novo. Para criar um novo, selecione **Criar novo** e preencha o nome que pretende utilizar. Para utilizar um grupo de recursos existente, selecione esse grupo de recursos. Para mais informações, consulte [Manage Azure Resource Manager grupos](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)de recursos .

   - **Região**: Selecione a região em que pretende localizar o seu hub. Selecione o local mais próximo de si. Algumas funcionalidades, como os streams de [dispositivos IoT Hub,](../articles/iot-hub/iot-hub-device-streams-overview.md)só estão disponíveis em regiões específicas. Para estas características limitadas, deve selecionar uma das regiões apoiadas.

   - **IoT Hub Name**: Introduza um nome para o seu hub. Este nome tem de ser globalmente exclusivo. Se o nome introduzido estiver disponível, aparece uma marca de verificação verde.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Criar um hub no portal Azure](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png)

1. Selecione **Seguinte: Tamanho e escala** para continuar a criar o seu hub.

   ![Detete o tamanho e a escala para um novo hub usando o portal Azure](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

   Pode aceitar as definições predefinidas aqui. Se desejar, pode modificar qualquer um dos seguintes campos: 

    - **Nível de preços e escala**: O seu nível selecionado. Pode escolher entre vários níveis, dependendo de quantas funcionalidades deseja e quantas mensagens envia através da sua solução por dia. O nível livre destina-se a testes e avaliação. Permite que 500 dispositivos sejam ligados ao hub e até 8.000 mensagens por dia. Cada subscrição do Azure pode criar um hub IoT no nível livre. 

      Se estiver a trabalhar através de um Quickstart para streams de dispositivos IoT Hub, selecione o nível livre.

    - **Unidades IoT Hub**: O número de mensagens permitidas por unidade por dia depende do nível de preços do seu hub. Por exemplo, se quiser que o hub suporte a entrada de 700.000 mensagens, escolha duas unidades de nível S1.
    Para mais detalhes sobre as outras opções de nível, consulte [Escolher o nível ioT hub certo](../articles/iot-hub/iot-hub-scaling.md).

    - **Centro de Segurança Azure**: Ligue isto para adicionar uma camada extra de proteção contra ameaças ao IoT e aos seus dispositivos. Esta opção não está disponível para centros de nível livre. Para mais informações sobre esta funcionalidade, consulte [o Azure Security Center para ioT](https://docs.microsoft.com/azure/asc-for-iot/).

    - **Definições avançadas** > **divisórias dispositivo-a-nuvem**: Esta propriedade relaciona as mensagens dispositivo-a-nuvem com o número de leitores simultâneos das mensagens. A maioria dos centros só precisa de quatro divisórias.

1.  Selecione **Seguinte: Tags** para continuar para o próximo ecrã.

    As etiquetas são pares de nome/valor. Pode atribuir a mesma etiqueta a múltiplos recursos e grupos de recursos para categorizar recursos e consolidar a faturação. para mais informações, consulte [Use tags para organizar os seus recursos Azure.](../articles/azure-resource-manager/management/tag-resources.md)

    ![Atribuir etiquetas para o hub utilizando o portal Azure](./media/iot-hub-include-create-hub/iot-hub-create-tabs.png)

1.  Selecione **Seguinte: Rever + criar** para rever as suas escolhas. Vê-se algo semelhante a este ecrã, mas com os valores que selecionou ao criar o hub. 

    ![Rever informações para criar o novo hub](./media/iot-hub-include-create-hub/iot-hub-create-review.png)

1.  Selecione **Criar** para criar o seu novo hub. Criar o centro leva alguns minutos.
