---
title: Criar uma aplicação do Azure IoT Central | Microsoft Docs
description: Crie uma nova aplicação do Azure IoT Central. Utilize um modelo de aplicação para criar uma aplicação de avaliação ou pay as you go.
author: viv-liu
ms.author: viviali
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 23ef0afbf3a3fd3e0d0db6e3b4130b45530916be
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001262"
---
# <a name="create-an-azure-iot-central-application"></a>Criar uma aplicação do Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Como _construtor_, utilize a IU do Azure IoT Central para definir a sua aplicação do Microsoft Azure IoT Central. Este guia de início rápido mostra como criar um aplicativo de IoT Central do Azure que contém um _modelo de dispositivo_de exemplo.

## <a name="create-an-application"></a>Criar uma aplicação

Navegue até o site [do Azure IOT central Application Manager](https://aka.ms/iotcentral) . Em seguida, entre com uma conta pessoal, corporativa ou de estudante da Microsoft.

Para começar a criar uma nova aplicação do Azure IoT Central, selecione **New Application** (Nova Aplicação). Esse link leva você para a página **criar um aplicativo** .

![Azure IoT Central criar uma página de aplicativo](media/quick-deploy-iot-central/iotcentralcreate.png)

Para criar uma nova aplicação do Azure IoT Central:

1. Escolha um plano de pagamento.
   - Os aplicativos de **avaliação** são gratuitos por sete dias antes de expirarem. Eles podem ser convertidos para **o pré-pago** a qualquer momento antes de expirarem. Se você criar um aplicativo de **avaliação** , precisará inserir suas informações de contato e escolher se deseja receber informações e dicas da Microsoft.
   - Os aplicativos **pagos conforme o uso** são cobrados por dispositivo, com os primeiros cinco dispositivos gratuitos. Se você criar um aplicativo **pago conforme o uso** , precisará selecionar seu *diretório*, sua *assinatura do Azure*e uma *região*:
        - O *diretório* é o Azure Active Directory (AD) para criar seu aplicativo. Contém identidades de utilizadores, credenciais e outras informações organizacionais. Se você não tiver um Azure AD, um será criado para você quando você criar uma assinatura do Azure.
        - A *Subscrição do Azure* permite-lhe criar instâncias de serviços do Azure. IoT Central provisiona recursos em sua assinatura. Se não tiver uma subscrição do Azure, pode criá-la na [página de inscrição do Azure](https://aka.ms/createazuresubscription). Depois de criar a assinatura do Azure, navegue de volta para a página **criar um aplicativo** . A subscrição nova aparece no menu pendente **Azure Subscription** (Subscrição do Azure).
        - *Região* é o local físico ou [geografia](https://azure.microsoft.com/global-infrastructure/geographies/) onde você gostaria de criar seu aplicativo. Normalmente, você deve escolher a região que está fisicamente mais próxima de seus dispositivos para obter o desempenho ideal. Você pode ver as regiões nas quais o Azure IoT Central está disponível na página [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=iot-central) . Depois de escolher uma região, você não poderá mover seu aplicativo para uma região diferente mais tarde.

        Saiba mais sobre os preços na [página de preços do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

1. Selecione um modelo de aplicativo. Os modelos de aplicação podem conter itens predefinidos, como modelos de dispositivo e dashboards, que o ajudam a começar.

    | Modelo de aplicação | Descrição |
    | -------------------- | ----------- |
    | Exemplo da Contoso       | Cria uma aplicação que inclui um modelo de dispositivo já criado para uma máquina dispensadora de bebidas. Utilize este modelo para começar a explorar o Azure IoT Central. |
    | Exemplo da Devkits       | Cria uma aplicação com modelos de dispositivo prontos para se poder ligar a dispositivos MXChip ou Raspberry Pi. Se for programador de dispositivos, utilize este modelo para experimentar um destes dispositivos. |
    | Aplicação personalizada   | Cria uma aplicação vazia que pode preencher com os seus próprios modelos de dispositivo e dispositivos. |

1. O Azure IoT Central sugere automaticamente um nome de aplicativo com base no modelo de aplicativo que você selecionou. Você pode aceitar esse nome ou inserir seu próprio nome de aplicativo amigável, como o **contoso IOT**. O Azure IoT Central também gera um prefixo de URL exclusivo para você, com base no nome do aplicativo. Você está livre para alterar esse prefixo de URL para algo mais fácil de memorizar, se desejar.

1. Preencha as informações adicionais necessárias para o plano de pagamento selecionado anteriormente, na etapa 1.

1. Selecione **criar** na parte inferior da página.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou uma aplicação do IoT Central. O passo seguinte sugerido é:

> [!div class="nextstepaction"]
> [Definir um novo tipo de dispositivo em seu aplicativo de IoT Central do Azure](./tutorial-define-device-type.md)
