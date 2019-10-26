---
title: Criar uma aplicação do Azure IoT Central | Microsoft Docs
description: Crie uma nova aplicação do Azure IoT Central. Utilize um modelo de aplicação para criar uma aplicação de avaliação ou pay as you go.
author: lmasieri
ms.author: lmasieri
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 5672c1868ef33e02a900e8ada1f027c3216ca585
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957672"
---
# <a name="create-an-azure-iot-central-application-preview-features"></a>Criar um aplicativo de IoT Central do Azure (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este guia de início rápido mostra como criar um aplicativo de IoT Central do Azure que contém recursos de visualização, como o IoT Plug and Play.

> [!WARNING]
> Os recursos de Plug and Play de IoT no Azure IoT Central estão atualmente em visualização pública. Não use um aplicativo de IoT Central de Plug and Play habilitado para IoT para cargas de trabalho de produção. Para ambientes de produção, use um aplicativo IoT central criado a partir de um modelo de aplicativo atual, geralmente disponível.

## <a name="create-an-application"></a>Criar uma aplicação

Navegue até o site do [Azure IOT central Build](https://aka.ms/iotcentral) . Em seguida, entre com uma conta pessoal, corporativa ou de estudante da Microsoft.

Você cria um novo aplicativo a partir da lista de modelos de IoT Central relevantes do setor para ajudá-lo a começar rapidamente ou a começar do zero usando o modelo de **aplicativo personalizado** .

![Azure IoT Central criar uma página de aplicativo](media/quick-deploy-iot-central-pnp/iotcentralcreate-templates-pnp.png)

Para criar uma nova aplicação do Azure IoT Central:

1. Para criar um novo aplicativo de IoT Central do Azure de um *modelo do setor*, selecione um modelo de aplicativo na lista de modelos disponíveis em um dos setores. Você também pode começar do zero escolhendo *aplicativo personalizado*.
1. O Azure IoT Central sugere automaticamente um **nome de aplicativo** com base no modelo de aplicativo que você selecionou. Você pode usar esse nome ou inserir seu próprio nome de aplicativo amigável.
1. O Azure IoT Central também gera um prefixo de **URL de aplicativo** exclusivo para você, com base no nome do aplicativo. Você usa essa URL para acessar seu aplicativo. Você está livre para alterar esse prefixo de URL para algo mais fácil de memorizar, se desejar.

    ![Azure IoT Central criar uma página de aplicativo](media/quick-deploy-iot-central-pnp/iotcentralcreate-industry-pnp.png)

    > [!NOTE]
    > Se você estiver usando o modelo de aplicativo personalizado, verá um campo suspenso de **modelo de aplicativo** . Aqui, você pode alternar entre visualização e modelos geralmente disponíveis. Você também pode ver outros modelos que foram disponibilizados para sua organização.

1. Escolha se deseja criar esse aplicativo usando uma avaliação gratuita de 7 dias ou use uma assinatura paga conforme o uso.
    - Os aplicativos de **avaliação** são gratuitos por sete dias e dão suporte a até cinco dispositivos. Podem ser convertidas em Pay As You Go a qualquer momento antes de expirarem. Se você criar um aplicativo de avaliação, precisará inserir suas informações de contato e escolher se deseja receber informações e dicas da Microsoft.
    - Os aplicativos **pagos conforme o uso** são cobrados por dispositivo, com os dois primeiros dispositivos livres. Saiba mais sobre [preços de IOT central](https://aka.ms/iotcentral-pricing). Se você criar um aplicativo pago conforme o uso, precisará selecionar o *diretório*, a *assinatura do Azure*e a *região*:
        - O *diretório* é o Azure Active Directory (AAD) no qual você criará seu aplicativo. Um Azure AD contém identidades de usuário, credenciais e outras informações organizacionais. Se você não tiver um Azure AD, um será criado para você quando você criar uma assinatura do Azure.
        - A *Subscrição do Azure* permite-lhe criar instâncias de serviços do Azure. IoT Central provisiona recursos em sua assinatura. Se não tiver uma subscrição do Azure, pode criá-la na [página de inscrição do Azure](https://aka.ms/createazuresubscription). Depois de criar a assinatura do Azure, navegue de volta para a página **criar um aplicativo** . Sua nova assinatura será exibida na lista suspensa **assinatura do Azure** .
        - *Região* é o local físico onde os dados dos dispositivos serão armazenados. Normalmente, você deve escolher a região que está fisicamente mais próxima de seus dispositivos para obter o desempenho ideal, bem como para garantir a conformidade da soberania de dados. Depois de escolher uma região, você não poderá mover seu aplicativo para uma região diferente mais tarde.

        > [!NOTE]
        > Durante a visualização pública, as únicas regiões disponíveis para **aplicativos de visualização** são **Europa setentrional** e **EUA Central**.

1. Examine os termos e condições e selecione **criar** na parte inferior da página.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou uma aplicação do IoT Central. O passo seguinte sugerido é:

> [!div class="nextstepaction"]
> [Adicionar um dispositivo simulado ao seu aplicativo IoT Central](./quick-create-pnp-device-pnp.md)
