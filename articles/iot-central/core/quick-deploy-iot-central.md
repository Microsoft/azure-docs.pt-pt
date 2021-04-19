---
title: Quickstart - Crie uma aplicação Azure IoT Central | Microsoft Docs
description: Quickstart - Crie uma nova aplicação Azure IoT Central. Crie a aplicação utilizando o plano de preços gratuitos ou um dos planos de preços padrão.
author: viv-liu
ms.author: viviali
ms.date: 12/28/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 7b33beaad580e64a4760b0557f04f266ecfc1b4d
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718814"
---
# <a name="quickstart---create-an-azure-iot-central-application"></a>Quickstart - Criar uma aplicação Azure IoT Central

Este quickstart mostra-lhe como criar uma aplicação Azure IoT Central.

## <a name="prerequisite"></a>Pré-requisito 

 - Uma conta Azure com uma subscrição ativa. Crie uma conta [gratuita.](https://aka.ms/createazuresubscription)
 - A sua subscrição Azure deve ter acesso ao Contribuinte

## <a name="create-an-application"></a>Criar uma aplicação

Navegue até ao sítio central de [construção Azure IoT.](https://aka.ms/iotcentral) Em seguida, inscreva-se com uma conta pessoal, de trabalho ou escola da Microsoft.

Pode criar uma nova aplicação a partir da lista de modelos IoT Central relevantes para a indústria para ajudá-lo a começar rapidamente, ou começar do zero usando um modelo de **aplicação personalizado.** Neste arranque rápido, utilize o modelo **de aplicação personalizado.**

Para criar uma nova aplicação Azure IoT Central a partir do modelo de **aplicação personalizado:**

1. Navegue para a página **Build:**

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-new-application.png" alt-text="Construa a sua página de aplicações IoT":::

1. Escolha **aplicativo personalizado**

1. Na nova página de **aplicações,** certifique-se de que **a aplicação Personalizada** é selecionada no **modelo de Aplicação.**

1. O Azure IoT Central sugere automaticamente um **nome de aplicação** baseado no modelo de aplicação que selecionou. Pode usar este nome ou introduzir o seu próprio nome de aplicação amigável.

1. A azure IoT Central também gera um prefixo **URL** único para si, com base no nome da aplicação. Utilize este URL para aceder à sua aplicação. Mude este prefixo URL para algo mais memorável, se quiser.

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-custom.png" alt-text="Central Azure IoT Criar uma página de aplicação":::

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png" alt-text="Informações de faturação Azure IoT Central":::

    > [!Tip]
    > Se escolher **a aplicação Custom** na página anterior, verá um **dropdown do modelo de aplicação.** O dropdown pode mostrar outros modelos que foram disponibilizados por você pela sua organização.

1. Opte por criar esta aplicação utilizando o plano de preços gratuitos de 7 dias, ou um dos planos de preços padrão:

    - As aplicações que cria usando o plano *gratuito* são gratuitas durante sete dias e suportam até cinco dispositivos. Pode convertê-los para usar um plano de preços padrão a qualquer momento antes de expirarem.
        > [!NOTE]
        > As aplicações criadas através do plano *gratuito* não requerem uma subscrição do Azure, pelo que não as encontrará listadas na subscrição do Azure no portal Azure. Só é possível ver e gerir aplicações gratuitas a partir do portal IoT Central.          
    - As aplicações que cria usando um plano *padrão* são faturadas por dispositivo, pode escolher o plano de preços **Standard 0,** **Standard 1** ou **Standard 2,** com os dois primeiros dispositivos a serem gratuitos. Saiba mais sobre os planos de preços gratuitos e padrão na página de preços da [Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). Se criar uma aplicação utilizando um plano de preços padrão, tem de selecionar o seu *Diretório,* *Subscrição Azure* e *Localização*:
        - *O Diretório* é o Diretório Ativo Azure no qual cria a sua aplicação. Um Diretório Ativo Azure contém identidades de utilizador, credenciais e outras informações organizacionais. Se não tiver um Diretório Ativo Azure, um é criado para si quando cria uma subscrição do Azure.
        - A *Subscrição do Azure* permite-lhe criar instâncias de serviços do Azure. A IoT Central fornece recursos na sua subscrição. Se não tiver uma subscrição do Azure, pode criar uma gratuitamente na página de inscrição do [Azure](https://aka.ms/createazuresubscription). Depois de criar a subscrição do Azure, volte à página de **aplicação Nova.** A sua nova subscrição aparece agora na entrega da **Subscrição Azure.**
        - *A localização* é a [geografia](https://azure.microsoft.com/global-infrastructure/geographies/) onde gostaria de criar a sua aplicação. Normalmente, deve escolher o local mais próximo fisicamente dos seus dispositivos para obter um desempenho ideal. Uma vez que você escolhe um local, você não pode mais tarde mover a sua aplicação para um local diferente.

1. Reveja os Termos e Condições e selecione **Criar** na parte inferior da página. Após alguns minutos, a sua aplicação IoT Central estará pronta a ser utilizada:

    :::image type="content" source="media/quick-deploy-iot-central/iotcentral-application.png" alt-text="Aplicação Azure IoT Central":::

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou uma aplicação do IoT Central. Aqui está o próximo passo sugerido para continuar a aprender sobre a IoT Central:

> [!div class="nextstepaction"]
> [Adicione um dispositivo simulado à sua aplicação IoT Central](./quick-create-simulated-device.md)

Se você é um desenvolvedor de dispositivos e quer mergulhar em algum código, o próximo passo sugerido é:
> [!div class="nextstepaction"]
> [Crie e conecte uma aplicação de cliente à sua aplicação Azure IoT Central](./tutorial-connect-device.md)
