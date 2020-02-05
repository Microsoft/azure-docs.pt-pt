---
title: Criar uma aplicação do Azure IoT Central | Microsoft Docs
description: Crie uma nova aplicação do Azure IoT Central. Crie uma aplicação Trial ou Standard utilizando um modelo de aplicação.
author: lmasieri
ms.author: lmasieri
ms.date: 12/18/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 1b19909f005fa11b842fccc0497cb49960e32a3b
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989734"
---
# <a name="create-an-azure-iot-central-application-preview-features"></a>Criar um aplicativo de IoT Central do Azure (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este guia de início rápido mostra como criar um aplicativo de IoT Central do Azure que contém recursos de visualização, como o IoT Plug and Play.

> [!WARNING]
> Os recursos de Plug and Play de IoT no Azure IoT Central estão atualmente em visualização pública. Não use um aplicativo de IoT Central de Plug and Play habilitado para IoT para cargas de trabalho de produção. Para ambientes de produção, use um aplicativo IoT central criado a partir de um modelo de aplicativo atual, geralmente disponível.

## <a name="create-an-application"></a>Criar uma aplicação

Navegue até o site do [Azure IOT central Build](https://aka.ms/iotcentral) . Em seguida, entre com uma conta pessoal, corporativa ou de estudante da Microsoft.

Você cria um novo aplicativo a partir da lista de modelos de IoT Central relevantes do setor para ajudá-lo a começar rapidamente ou a começar do zero usando o modelo de **aplicativo personalizado** .

![Azure IoT Central criar uma página de aplicativo](media/quick-deploy-iot-central/iotcentralcreate-templates-pnp.png)

Para criar uma nova aplicação do Azure IoT Central:

1. Para criar um novo aplicativo de IoT Central do Azure de um *modelo do setor*, selecione um modelo de aplicativo na lista de modelos disponíveis em um dos setores. Você também pode começar do zero escolhendo *aplicativo personalizado*.
1. O Azure IoT Central sugere automaticamente um **nome de aplicativo** com base no modelo de aplicativo que você selecionou. Você pode usar esse nome ou inserir seu próprio nome de aplicativo amigável.
1. O Azure IoT Central também gera um prefixo de **URL de aplicativo** exclusivo para você, com base no nome do aplicativo. Você usa essa URL para acessar seu aplicativo. Você está livre para alterar esse prefixo de URL para algo mais fácil de memorizar, se desejar.

    ![Azure IoT Central criar uma página de aplicativo](media/quick-deploy-iot-central/iotcentralcreate-industry-pnp.png)

    ![Azure IoT Central criar uma página de aplicativo](media/quick-deploy-iot-central/iotcentralcreate-billinginfo-pnp.png)
    > [!NOTE]
    > Se você estiver usando o modelo de aplicativo personalizado, verá um campo suspenso de **modelo de aplicativo** . Aqui, você pode alternar entre visualização e modelos geralmente disponíveis. Você também pode ver outros modelos que foram disponibilizados para sua organização.

1. Escolha se pretende criar esta aplicação usando um teste gratuito de 7 dias ou utilize uma subscrição padrão.
    - As aplicações que cria utilizando o plano **Standard** são faturadas por dispositivo, pode escolher o plano de preços **Standard 1** ou **Standard 2,** com os dois primeiros dispositivos gratuitos. Saiba mais sobre os planos de preços gratuitos e standard na página de [preços Do Azure IoT Central.](https://azure.microsoft.com/pricing/details/iot-central/) Se criar uma aplicação padrão, terá de selecionar o seu *Diretório,* *Subscrição Azure*e *Localização:*
      - O *diretório* é o Azure Active Directory (AAD) no qual você criará seu aplicativo. Um Azure AD contém identidades de usuário, credenciais e outras informações organizacionais. Se você não tiver um Azure AD, um será criado para você quando você criar uma assinatura do Azure.
      - A *Subscrição do Azure* permite-lhe criar instâncias de serviços do Azure. IoT Central provisiona recursos em sua assinatura. Se não tiver uma subscrição do Azure, pode criá-la na [página de inscrição do Azure](https://aka.ms/createazuresubscription). Depois de criar a assinatura do Azure, navegue de volta para a página **criar um aplicativo** . Sua nova assinatura será exibida na lista suspensa **assinatura do Azure** .
      - *Local* é a [geografia](https://azure.microsoft.com/global-infrastructure/geographies/) onde você gostaria de criar seu aplicativo. Normalmente, você deve escolher o local que está fisicamente mais próximo de seus dispositivos para obter o desempenho ideal. A visualização pública do Azure IoT Central está disponível atualmente no **Estados Unidos**ou na **Europa**. Depois de escolher um local, você não poderá mover seu aplicativo para um local diferente mais tarde.
        > [!NOTE]
        > Durante a visualização pública, os únicos locais disponíveis para **aplicativos de visualização** são **Europa** e **Estados Unidos**.

1. Examine os termos e condições e selecione **criar** na parte inferior da página.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou uma aplicação do IoT Central. O passo seguinte sugerido é:

> [!div class="nextstepaction"]
> [Adicionar um dispositivo simulado ao seu aplicativo IoT Central](./quick-create-pnp-device.md)
