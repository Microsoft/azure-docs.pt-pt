---
title: incluir ficheiro
description: incluir ficheiro
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e01bb13b904aaf8671512daa3ef3cbad182f01e3
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70161939"
---
Esta seção descreve como criar um hub IoT usando o [portal do Azure](https://portal.azure.com).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Escolha **criar um recurso**e, em seguida, insira *Hub IOT* no campo **Pesquisar no Marketplace** .

1. Selecione **Hub IOT** nos resultados da pesquisa e, em seguida, selecione **criar**.

1. Na guia **noções básicas** , preencha os campos da seguinte maneira:

   - **Subscrição**: Selecione a assinatura a ser usada para o Hub.

   - **Grupo de recursos**: Selecione um grupo de recursos ou crie um novo. Para criar um novo, selecione **criar novo** e preencha o nome que você deseja usar. Para usar um grupo de recursos existente, selecione esse grupo de recursos. Para obter mais informações, consulte [Manage Azure Resource Manager Resource groups](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   - **Região**: Selecione a região na qual você deseja que o Hub seja localizado. Selecione o local mais próximo de você.

   - **Nome do Hub IOT**: Insira um nome para o Hub. Este nome tem de ser globalmente exclusivo. Se o nome introduzido estiver disponível, aparece uma marca de verificação verde.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Criar um Hub no portal do Azure](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics-vs2019.png)

1. Selecione **avançar: Tamanho e escala** para continuar criando o Hub.

   ![Definir o tamanho e a escala de um novo hub usando o portal do Azure](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

    Esta tela permite que você defina os seguintes valores:

    - **Camada de preços e escala**: Sua camada selecionada. Você pode escolher entre várias camadas, dependendo de quantos recursos você deseja e quantas mensagens você envia por meio de sua solução por dia. A camada gratuita destina-se a testes e avaliação. Ele permite que os dispositivos 500 sejam conectados ao Hub e até 8.000 mensagens por dia. Cada assinatura do Azure pode criar um hub IoT na camada gratuita.

    - **Unidades do Hub IOT**: O número de mensagens permitidas por unidade por dia depende do tipo de preço do seu hub. Por exemplo, se você quiser que o Hub dê suporte à entrada de 700.000 mensagens, escolha duas unidades de camada S1.
    Para obter detalhes sobre as outras opções de camada, consulte [escolhendo a camada certa do Hub IOT](../articles/iot-hub/iot-hub-scaling.md).

    - Configurações > avançadas**partições de dispositivo para nuvem**: Essa propriedade relaciona as mensagens do dispositivo para a nuvem com o número de leitores simultâneos das mensagens. A maioria dos hubs precisa de apenas quatro partições.

1. Para este artigo, aceite as opções padrão e, em seguida, selecione **examinar + criar** para examinar suas escolhas. Você verá algo semelhante a esta tela.

   ![Examinar informações para criar o novo hub](./media/iot-hub-include-create-hub/iot-hub-create-review-vs2019.png)

1. Selecione **criar** para criar o novo hub. A criação do Hub leva alguns minutos.
