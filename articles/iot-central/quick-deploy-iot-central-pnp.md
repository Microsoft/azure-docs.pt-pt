---
title: Criar uma aplicação do Azure IoT Central | Microsoft Docs
description: Crie uma nova aplicação do Azure IoT Central. Utilize um modelo de aplicação para criar uma aplicação de avaliação ou pay as you go.
author: viv-liu
ms.author: viviali
ms.date: 06/07/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 72d02cecf37c631e6f8097b220848425c5e1fe9c
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719129"
---
# <a name="create-an-azure-iot-central-application-preview-features"></a>Criar um aplicativo de IoT Central do Azure (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Este guia de início rápido mostra como criar um aplicativo de IoT Central do Azure que usa recursos de visualização, como o IoT Plug and Play.

> [!WARNING]
> Os recursos de Plug and Play de IoT no Azure IoT Central estão atualmente em visualização pública. Não use um aplicativo de IoT Central de Plug and Play habilitado para IoT para cargas de trabalho de produção. Para ambientes de produção, use um aplicativo IoT central criado a partir de um modelo de aplicativo atual, geralmente disponível.

## <a name="create-an-application"></a>Criar uma aplicação

Navegue até o site [do Azure IOT central Application Manager](https://aka.ms/iotcentral) . Em seguida, entre com uma conta pessoal, corporativa ou de estudante da Microsoft.

Para começar a criar uma nova aplicação do Azure IoT Central, selecione **New Application** (Nova Aplicação). Esse link leva você para a página **criar um aplicativo** .

![Azure IoT Central criar uma página de aplicativo](media/quick-deploy-iot-central-pnp/iotcentralcreate-pnp.png)

Para criar um novo aplicativo de IoT Central do Azure que inclui recursos de visualização, como o IoT Plug and Play:

1. Escolha um plano de pagamento.
   - Os aplicativos de **avaliação** são gratuitos por sete dias antes de expirarem. Eles podem ser convertidos para **o pré-pago** a qualquer momento antes de expirarem. Se você criar um aplicativo de **avaliação** , precisará inserir suas informações de contato e escolher se deseja receber informações e dicas da Microsoft.
   - Os aplicativos **pagos conforme o uso** são cobrados por dispositivo, com os primeiros cinco dispositivos gratuitos. Se você criar um aplicativo **pago conforme o uso** , precisará selecionar o *diretório*, a *assinatura do Azure*e a *região*:
        - O *diretório* é o Azure Active Directory (AD) para criar seu aplicativo. Contém identidades de utilizadores, credenciais e outras informações organizacionais. Se você não tiver um Azure AD, um será criado para você quando você criar uma assinatura do Azure.
        - A *Subscrição do Azure* permite-lhe criar instâncias de serviços do Azure. O IoT Central aprovisiona os recursos na sua subscrição. Se não tiver uma subscrição do Azure, pode criá-la na [página de inscrição do Azure](https://aka.ms/createazuresubscription). Depois de criar a assinatura do Azure, navegue de volta para a página **criar um aplicativo** . A subscrição nova aparece no menu pendente **Azure Subscription** (Subscrição do Azure).
        - A *região* é a localização física na qual pretende criar a aplicação. Normalmente, você deve escolher a região que está fisicamente mais próxima de seus dispositivos para obter o desempenho ideal. Durante a visualização pública, as únicas regiões disponíveis para um **aplicativo de visualização** são **Europa setentrional** e **EUA Central**. Depois de escolher uma região, você não poderá mover seu aplicativo para uma região diferente mais tarde.

        Saiba mais sobre os preços na [página de preços do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

1. Selecione o modelo de **aplicativo de visualização** . Os modelos de aplicação podem conter itens predefinidos, como modelos de dispositivo e dashboards, que o ajudam a começar.

1. O Azure IoT Central sugere automaticamente um nome de aplicativo com base no modelo de aplicativo que você selecionou. Você pode aceitar esse nome ou inserir seu próprio nome de aplicativo amigável, como o **contoso IOT**. O Azure IoT Central também gera um prefixo de URL exclusivo para você, com base no nome do aplicativo. Você está livre para alterar esse prefixo de URL para algo mais fácil de memorizar, se desejar.

1. Preencha as informações adicionais necessárias para o plano de pagamento selecionado anteriormente, na etapa 1.

1. Selecione **criar** na parte inferior da página.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um aplicativo IoT Central que usa os recursos de visualização. O passo seguinte sugerido é:

> [!div class="nextstepaction"]
> [Definir um novo tipo de dispositivo em seu aplicativo de IoT Central do Azure](./tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
