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
ms.openlocfilehash: 0ae82f8980b995edab3419374e27a2cb905935d5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099504"
---
# <a name="create-an-azure-iot-central-application-preview-features"></a>Criar um aplicativo de IoT Central do Azure (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Este guia de início rápido mostra como criar um aplicativo de IoT Central do Azure que usa recursos de visualização, como o IoT Plug and Play.

> [!WARNING]
> Os recursos de Plug and Play de IoT no Azure IoT Central estão atualmente em visualização pública. Não use um aplicativo de IoT Central de Plug and Play habilitado para IoT para cargas de trabalho de produção. Para ambientes de produção, use um aplicativo IoT central criado a partir de um modelo de aplicativo atual, geralmente disponível.

## <a name="create-an-application"></a>Criar uma aplicação

Navegue até o site [do Azure IOT central Application Manager](https://aka.ms/iotcentral) . Em seguida, entre com uma conta pessoal ou de trabalho ou de estudante da Microsoft.

Para começar a criar uma nova aplicação do Azure IoT Central, selecione **New Application** (Nova Aplicação). Esse link leva você para a página **criar um aplicativo** .

![Azure IoT Central criar uma página de aplicativo](media/quick-deploy-iot-central-pnp/iotcentralcreate.png)

Para criar um novo aplicativo de IoT Central do Azure que inclui recursos de visualização, como o IoT Plug and Play:

1. Escolha um plano de pagamento.
   - Os aplicativos de **avaliação** são gratuitos por sete dias antes de expirarem. Podem ser convertidas em Pay As You Go a qualquer momento antes de expirarem. Se você criar um aplicativo de **avaliação** , precisará inserir suas informações de contato e escolher se deseja receber informações e dicas da Microsoft.
   - Os aplicativos **pagos conforme o uso** são cobrados por dispositivo, com os primeiros cinco dispositivos gratuitos. Se você criar um aplicativo **pago conforme o uso** , precisará selecionar seu *diretório*, sua *assinatura do Azure*e uma *região*:
      - O *diretório* é o Azure Active Directory (AD) para criar seu aplicativo. Contém identidades de utilizadores, credenciais e outras informações organizacionais. Se você não tiver um Azure AD, um será criado para você quando você criar uma assinatura do Azure.
      - A *Subscrição do Azure* permite-lhe criar instâncias de serviços do Azure. O IoT Central aprovisiona os recursos na sua subscrição. Se não tiver uma subscrição do Azure, pode criá-la na [página de inscrição do Azure](https://aka.ms/createazuresubscription). Depois de criar a subscrição, regresse à página **Create Application** (Criar Aplicação). A subscrição nova aparece no menu pendente **Azure Subscription** (Subscrição do Azure).
      - A *região* é a localização física na qual pretende criar a aplicação. Normalmente, você escolhe a região que está fisicamente mais próxima de seus dispositivos para obter o desempenho ideal. Você pode ver as regiões nas quais o Azure IoT Central está disponível na página [produtos disponíveis por região](https://azure.microsoft.com/regions/services/) . Depois de escolher uma região, você não poderá mover o aplicativo para uma região diferente posteriormente.

      Saiba mais sobre os preços na [página de preços do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

1. Escolha um nome simpático para a aplicação, como **Contoso IoT**. O Azure IoT Central gera um prefixo de URL exclusivo para si. Pode alterar este prefixo de URL para algo mais memorável.

1. Escolha o modelo de **aplicativo de visualização** . Os modelos de aplicação podem conter itens predefinidos, como modelos de dispositivo e dashboards, que o ajudam a começar.

1. Selecione **criar** na parte inferior da página.

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você criou um aplicativo IoT Central que usa os recursos de visualização. A próxima etapa sugerida é:

> [!div class="nextstepaction"]
> [Definir um novo tipo de dispositivo em seu aplicativo de IoT Central do Azure](./tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
