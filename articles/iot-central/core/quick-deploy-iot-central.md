---
title: Criar uma aplicação do Azure IoT Central | Microsoft Docs
description: Crie uma nova aplicação do Azure IoT Central. Crie a aplicação utilizando o plano de preços gratuitos ou um dos planos de preços padrão.
author: viv-liu
ms.author: viviali
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 09ff84bf17bbc07ee86e90f3985a949f70d9fe27
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77018300"
---
# <a name="create-an-azure-iot-central-application"></a>Criar uma aplicação do Azure IoT Central

Este quickstart mostra-lhe como criar uma aplicação Azure IoT Central.

## <a name="create-an-application"></a>Criar uma aplicação

Navegue até o site do [Azure IOT central Build](https://aka.ms/iotcentral) . Em seguida, entre com uma conta pessoal, corporativa ou de estudante da Microsoft.

Você cria um novo aplicativo a partir da lista de modelos de IoT Central relevantes do setor para ajudá-lo a começar rapidamente ou a começar do zero usando o modelo de **aplicativo personalizado** . Neste arranque rápido, você usa o modelo de **aplicativo Personalizado.**

Para criar uma nova aplicação Azure IoT Central a partir do modelo de **aplicação Custom:**

1. Navegue para a página **Build:**

    ![Construa a sua página de aplicação IoT](media/quick-deploy-iot-central/iotcentralcreate-new-application.png)

1. Escolha **a aplicação Custom** e certifique-se de que o modelo de **aplicação Personalizado** é selecionado.

1. O Azure IoT Central sugere automaticamente um **nome de aplicativo** com base no modelo de aplicativo que você selecionou. Você pode usar esse nome ou inserir seu próprio nome de aplicativo amigável.

1. O Azure IoT Central também gera um prefixo de **URL de aplicativo** exclusivo para você, com base no nome do aplicativo. Você usa essa URL para acessar seu aplicativo. Mude este prefixo de URL para algo mais memorável, se quiser.

    ![Azure IoT Central criar uma página de aplicativo](media/quick-deploy-iot-central/iotcentralcreate-custom.png)

    ![Informação de faturação central Azure IoT](media/quick-deploy-iot-central/iotcentralcreate-billinginfo-pnp.png)

    > [!NOTE]
    > Se escolheu **a aplicação Custom** na página anterior, vê um desfecho do **modelo de aplicação.** A partir daqui pode alternar entre modelos personalizados e legados. Você também pode ver outros modelos que foram disponibilizados para sua organização.

1. Opte por criar esta aplicação utilizando o plano de preços de teste gratuito de 7 dias, ou um dos planos de preços padrão:

    - As aplicações que cria utilizando o plano *gratuito* são gratuitas durante sete dias e suportam até cinco dispositivos. Pode convertê-los para usar um plano de preços padrão a qualquer momento antes de expirarem.
    - As aplicações que cria utilizando um plano *padrão* são faturadas por dispositivo, pode escolher o plano de preços **Standard 1** ou **Standard 2,** com os dois primeiros dispositivos gratuitos. Saiba mais sobre os planos de preços gratuitos e standard na página de [preços Do Azure IoT Central.](https://azure.microsoft.com/pricing/details/iot-central/) Se criar uma aplicação padrão, terá de selecionar o seu *Diretório,* *Subscrição Azure*e *Localização:*
        - *O Diretório* é o Diretório Ativo Azure no qual cria a sua aplicação. Um Diretório Ativo Azure contém identidades de utilizador, credenciais e outras informações organizacionais. Se não tiver um Diretório Ativo Azure, é criado um para si quando cria uma subscrição Azure.
        - A *Subscrição do Azure* permite-lhe criar instâncias de serviços do Azure. IoT Central provisiona recursos em sua assinatura. Se não tiver uma subscrição Azure, pode criar uma gratuitamente na [página de inscrição do Azure](https://aka.ms/createazuresubscription). Depois de criar a subscrição Do Azure, navegue de volta para a **página de aplicação New.** A sua nova subscrição aparece agora na queda da **Subscrição Azure.**
        - *Localização* é a [geografia](https://azure.microsoft.com/global-infrastructure/geographies/) onde você gostaria de criar a sua aplicação. Normalmente, você deve escolher o local que está fisicamente mais próximo de seus dispositivos para obter o desempenho ideal. Uma vez que você escolhe um local, você não pode mais tarde mover a sua aplicação para um local diferente.

1. Examine os termos e condições e selecione **criar** na parte inferior da página. Após alguns minutos, a aplicação IoT Central está pronta a ser utilizada:

    ![Aplicação Central Azure IoT](media/quick-deploy-iot-central/iotcentral-application.png)

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou uma aplicação do IoT Central. O passo seguinte sugerido é:

> [!div class="nextstepaction"]
> [Adicionar um dispositivo simulado ao seu aplicativo IoT Central](./quick-create-pnp-device.md)
