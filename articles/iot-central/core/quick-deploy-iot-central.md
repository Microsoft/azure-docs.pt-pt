---
title: Criar uma aplicação do Azure IoT Central | Microsoft Docs
description: Crie uma nova aplicação do Azure IoT Central. Crie a aplicação utilizando o plano de preços gratuitos ou um dos planos de preços padrão.
author: viv-liu
ms.author: viviali
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: b670776804c9758774bf216052254148f063e7da
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758145"
---
# <a name="create-an-azure-iot-central-application"></a>Criar uma aplicação do Azure IoT Central

Este quickstart mostra-lhe como criar uma aplicação Azure IoT Central.

## <a name="create-an-application"></a>Criar uma aplicação

Navegue para o local de [construção central azure ioT.](https://aka.ms/iotcentral) Em seguida, inscreva-se com uma conta pessoal, de trabalho ou escolar da Microsoft.

Cria uma nova aplicação a partir da lista de modelos IoT Central relevantes para ajudar a começar rapidamente, ou começar do zero usando um modelo de **aplicações Personalizadas.** Neste arranque rápido, você usa o modelo de **aplicação Personalizado.**

Para criar uma nova aplicação Azure IoT Central a partir do modelo de **aplicação Personalizado:**

1. Navegue para a página **Build:**

    ![Construa a sua página de aplicação IoT](media/quick-deploy-iot-central/iotcentralcreate-new-application.png)

1. Escolha **aplicativos Personalizados** e certifique-se de que o modelo de **aplicação Personalizado** é selecionado.

1. A Azure IoT Central sugere automaticamente um nome de **aplicação** baseado no modelo de aplicação que selecionou. Pode utilizar este nome ou inserir o seu próprio nome de candidatura amigável.

1. A Azure IoT Central também gera um prefixo URL de **aplicação** único para si, com base no nome da aplicação. Usa este URL para aceder à sua aplicação. Mude este prefixo de URL para algo mais memorável, se quiser.

    ![Azure IoT Central Criar uma página de candidatura](media/quick-deploy-iot-central/iotcentralcreate-custom.png)

    ![Informação de faturação central Azure IoT](media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png)

    > [!NOTE]
    > Se escolheu **a aplicação Custom** na página anterior, vê um desfecho do **modelo de aplicação.** A partir daqui pode alternar entre modelos personalizados e legados. Você também pode ver outros modelos que foram disponibilizados para a sua organização.

1. Opte por criar esta aplicação utilizando o plano de preços de teste gratuito de 7 dias, ou um dos planos de preços padrão:

    - As aplicações que cria utilizando o plano *gratuito* são gratuitas durante sete dias e suportam até cinco dispositivos. Pode convertê-los para usar um plano de preços padrão a qualquer momento antes de expirarem.
    - As aplicações que cria utilizando um plano *padrão* são faturadas por dispositivo, pode escolher o plano de preços **Standard 1** ou **Standard 2,** com os dois primeiros dispositivos gratuitos. Saiba mais sobre os planos de preços gratuitos e standard na página de [preços Do Azure IoT Central.](https://azure.microsoft.com/pricing/details/iot-central/) Se criar uma aplicação utilizando um plano de preços padrão, tem de selecionar o seu *Diretório,* *Subscrição Azure*e *Localização:*
        - *O Diretório* é o Diretório Ativo Azure no qual cria a sua aplicação. Um Diretório Ativo Azure contém identidades de utilizador, credenciais e outras informações organizacionais. Se não tiver um Diretório Ativo Azure, é criado um para si quando cria uma subscrição Azure.
        - A *Subscrição do Azure* permite-lhe criar instâncias de serviços do Azure. A IoT Central disponibiliza recursos na sua subscrição. Se não tiver uma subscrição Azure, pode criar uma gratuitamente na [página de inscrição do Azure](https://aka.ms/createazuresubscription). Depois de criar a subscrição Do Azure, navegue de volta para a **página de aplicação New.** A sua nova subscrição aparece agora na queda da **Subscrição Azure.**
        - *Localização* é a [geografia](https://azure.microsoft.com/global-infrastructure/geographies/) onde você gostaria de criar a sua aplicação. Normalmente, deve escolher o local fisicamente mais próximo dos seus dispositivos para obter um desempenho ideal. Uma vez que você escolhe um local, você não pode mais tarde mover a sua aplicação para um local diferente.

1. Reveja os Termos e Condições e selecione **Criar** na parte inferior da página. Após alguns minutos, a aplicação IoT Central está pronta a ser utilizada:

    ![Aplicação Central Azure IoT](media/quick-deploy-iot-central/iotcentral-application.png)

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou uma aplicação do IoT Central. Aqui está o próximo passo sugerido para continuar a aprender sobre a IoT Central:

> [!div class="nextstepaction"]
> [Adicione um dispositivo simulado à sua aplicação IoT Central](./quick-create-simulated-device.md)

Se é um desenvolvedor de dispositivos e quer mergulhar em algum código, o próximo passo sugerido é:
> [!div class="nextstepaction"]
> [Crie e ligue uma aplicação de cliente à sua aplicação Azure IoT Central](./tutorial-connect-device-nodejs.md)
