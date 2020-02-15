---
title: incluir ficheiro
description: incluir ficheiro
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/13/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: bbdafde85097d0052edd5984b594fd37066dc1e6
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77279601"
---
Esta secção descreve como criar um hub IoT utilizando o [portal Azure](https://portal.azure.com).

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. A partir da página inicial do Azure, selecione o **botão + Criar um botão de recurso** e, em seguida, introduza *o IoT Hub* no campo Search the **Marketplace.**

1. Selecione **IoT Hub** a partir dos resultados da pesquisa e, em seguida, selecione **Criar**.

1. No separador **Basics,** preencha os campos da seguinte forma:

   - **Subscrição**: Selecione a subscrição a utilizar para o seu hub.

   - **Grupo de Recursos**: Selecione um grupo de recursos ou crie um novo. Para criar um novo, selecione **Criar novo** e preencha o nome que pretende utilizar. Para usar um grupo de recursos existente, selecione esse grupo de recursos. Para mais informações, consulte [Manage Azure Resource Manager grupos](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)de recursos .

   - **Região**: Selecione a região em que pretende localizar o seu hub. Selecione o local mais próximo de si.

   - **IoT Hub Name**: Introduza um nome para o seu hub. Este nome tem de ser globalmente exclusivo. Se o nome introduzido estiver disponível, aparece uma marca de verificação verde.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Criar um Hub no portal do Azure](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png)

1. Selecione **Seguinte: Tamanho e escala** para continuar a criar o seu hub.

   ![Definir o tamanho e a escala de um novo hub usando o portal do Azure](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

    Esta tela permite que você defina os seguintes valores:

    - **Nível de preços e escala**: O seu nível selecionado. Você pode escolher entre várias camadas, dependendo de quantos recursos você deseja e quantas mensagens você envia por meio de sua solução por dia. A camada gratuita destina-se a testes e avaliação. Ele permite que os dispositivos 500 sejam conectados ao Hub e até 8.000 mensagens por dia. Cada assinatura do Azure pode criar um hub IoT na camada gratuita.

    - **Unidades IoT Hub**: O número de mensagens permitidas por unidade por dia depende do nível de preços do seu hub. Por exemplo, se você quiser que o Hub dê suporte à entrada de 700.000 mensagens, escolha duas unidades de camada S1.
    Para mais detalhes sobre as outras opções de nível, consulte [Escolher o nível ioT hub certo](../articles/iot-hub/iot-hub-scaling.md).

    - **Centro de Segurança Azure**: Ligue isto para adicionar uma camada extra de proteção contra ameaças ao IoT e aos seus dispositivos. Esta opção não está disponível para centros de nível livre. Para mais informações sobre esta funcionalidade, consulte [o Azure Security Center para ioT](https://docs.microsoft.com/azure/asc-for-iot/).

    - **Definições avançadas** > **divisórias dispositivo-a-nuvem**: Esta propriedade relaciona as mensagens dispositivo-a-nuvem com o número de leitores simultâneos das mensagens. A maioria dos hubs precisa de apenas quatro partições.

1. Para este artigo, aceite as escolhas predefinidas e, em seguida, selecione **Next: Tags** para passar para o ecrã seguinte.

    As etiquetas são pares de nome/valor. Pode atribuir a mesma etiqueta a múltiplos recursos e grupos de recursos para categorizar recursos e consolidar a faturação.

   ![Definir o tamanho e a escala de um novo hub usando o portal do Azure](./media/iot-hub-include-create-hub/iot-hub-create-tabs.png)

    Selecione **Seguinte: Rever + criar** para rever as suas escolhas. Você verá algo semelhante a esta tela.

   ![Examinar informações para criar o novo hub](./media/iot-hub-include-create-hub/iot-hub-create-review.png)

1. Selecione **Criar** para criar o seu novo hub. A criação do Hub leva alguns minutos.
