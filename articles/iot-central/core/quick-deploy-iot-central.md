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
ms.openlocfilehash: 5268f5438c005033f9c6ecf74657dc1a01d3b673
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989411"
---
# <a name="create-an-azure-iot-central-application"></a>Criar uma aplicação do Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Como _construtor_, utilize a IU do Azure IoT Central para definir a sua aplicação do Microsoft Azure IoT Central. Este guia de início rápido mostra como criar um aplicativo de IoT Central do Azure que contém um _modelo de dispositivo_de exemplo. O aplicativo que você cria não usa nenhum recurso de visualização.

## <a name="create-an-application"></a>Criar uma aplicação

Navegue até o site do [Azure IOT central Build](https://aka.ms/iotcentral) . Em seguida, entre com uma conta pessoal, corporativa ou de estudante da Microsoft.

Para começar a criar um aplicativo de IoT Central do Azure sem recursos de visualização habilitados, selecione **Compilar**. Esse link leva você para a página **criar seu aplicativo IOT** .

![Página de compilação de IoT Central do Azure](media/quick-deploy-iot-central/iotcentralcreate.png)

Em seguida, selecione **aplicativo personalizado**.

Para criar uma nova aplicação do Azure IoT Central:

1. O Azure IoT Central sugere automaticamente um nome de aplicativo com base no modelo de aplicativo que você selecionou. Você pode aceitar esse nome ou inserir seu próprio nome de aplicativo amigável, como o **contoso IOT**. O Azure IoT Central também gera uma URL exclusiva para você, com base no nome do aplicativo. Você está livre para alterar esse prefixo de URL para algo mais fácil de memorizar, se desejar.

1. Selecione o modelo de **aplicativo herdado** que não usa recursos de visualização.

    | Modelo de aplicação | Descrição |
    | -------------------- | ----------- |
    | Aplicativo herdado   | Cria uma aplicação vazia que pode preencher com os seus próprios modelos de dispositivo e dispositivos. |

    ![Nova aplicação azure IoT Central](media/quick-deploy-iot-central/newapplication.png)

    ![Informação de faturação central Azure IoT](media/quick-deploy-iot-central/billinginfo.png)

1. Opte por criar esta aplicação utilizando o plano de preços de teste gratuito de 7 dias, ou um dos planos de preços padrão:
   - As aplicações que cria utilizando o plano *gratuito* são gratuitas durante sete dias e suportam até cinco dispositivos. Pode convertê-los para usar um plano de preços padrão a qualquer momento antes de expirarem.
   - As aplicações que cria utilizando o plano *padrão* são faturadas por dispositivo, pode escolher o plano de preços **Standard 1** ou **Standard 2,** com os dois primeiros dispositivos gratuitos. Saiba mais sobre os planos de preços gratuitos e standard na página de [preços Do Azure IoT Central.](https://azure.microsoft.com/pricing/details/iot-central/) Se criar uma aplicação padrão, terá de selecionar o seu *Diretório,* *Subscrição Azure*e *Localização:*
        - O *diretório* é o Azure Active Directory (AD) para criar seu aplicativo. Contém identidades de utilizadores, credenciais e outras informações organizacionais. Se você não tiver um Azure AD, um será criado para você quando você criar uma assinatura do Azure.
        - A *Subscrição do Azure* permite-lhe criar instâncias de serviços do Azure. IoT Central provisiona recursos em sua assinatura. Se não tiver uma subscrição do Azure, pode criá-la na [página de inscrição do Azure](https://aka.ms/createazuresubscription). Depois de criar a assinatura do Azure, navegue de volta para a página **criar um aplicativo** . A subscrição nova aparece no menu pendente **Azure Subscription** (Subscrição do Azure).
        - *Local* é a [geografia](https://azure.microsoft.com/global-infrastructure/geographies/) onde você gostaria de criar seu aplicativo. Normalmente, você deve escolher o local que está fisicamente mais próximo de seus dispositivos para obter o desempenho ideal. No momento, o Azure IoT Central está disponível no **Estados Unidos**, na **austrália**, no **Pacífico Asiático**ou na **Europa**.  Depois de escolher um local, você não poderá mover seu aplicativo para um local diferente mais tarde.
        Saiba mais sobre os preços na [página de preços do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

1. Preencha as informações adicionais necessárias para o plano de pagamento selecionado anteriormente, na etapa 1.

1. Selecione **criar** na parte inferior da página.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou uma aplicação do IoT Central. O passo seguinte sugerido é:

> [!div class="nextstepaction"]
> [Definir um novo tipo de dispositivo em seu aplicativo de IoT Central do Azure](./tutorial-define-device-type.md)
