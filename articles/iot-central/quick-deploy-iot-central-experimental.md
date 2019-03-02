---
title: Criar uma aplicação do Azure IoT Central | Microsoft Docs
description: Crie uma nova aplicação do Azure IoT Central. Utilize um modelo de aplicação para criar uma aplicação de avaliação ou pay as you go.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: e538d0caf82d67314834a630d53dd03f2c1a11bd
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57216599"
---
# <a name="create-an-azure-iot-central-application"></a>Criar uma aplicação do Azure IoT Central

Como _construtor_, utilize a IU do Azure IoT Central para definir a sua aplicação do Microsoft Azure IoT Central. Este início rápido mostra como criar uma aplicação do Azure IoT Central que contém um exemplo do _modelo de dispositivo_ e _dispositivos_ simulados.

Navegue para a página do [Gestor de Aplicações](https://aka.ms/iotcentral) do Azure IoT Central. Tem de iniciar sessão com uma conta Microsoft pessoal ou escolar ou profissional.

Para começar a criar uma nova aplicação do Azure IoT Central, selecione **New Application** (Nova Aplicação). É encaminhado para a página **Create Application** (Criar Aplicação).

![Página da Aplicação do Azure IoT Central](media/quick-deploy-iot-central-experimental/iotcentralcreate.png)

Para criar uma nova aplicação do Azure IoT Central:

1. Escolha um plano de pagamento.
    - As aplicações de **avaliação** são gratuitas durante sete dias, até que expiram. Podem ser convertidas em Pay As You Go a qualquer momento antes de expirarem.
    - As aplicações **Pay As You Go** são cobradas por dispositivo, sendo os primeiros cinco gratuitos.

    Saiba mais sobre os preços na [página de preços do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

1. Escolha um nome simpático para a aplicação, como **Contoso IoT**. O Azure IoT Central gera um prefixo de URL exclusivo para si. Pode alterar este prefixo de URL para algo mais memorável.

1. Escolha um modelo de aplicação. Os modelos de aplicação podem conter itens predefinidos, como modelos de dispositivo e dashboards, que o ajudam a começar.

    | Modelo de aplicação | Descrição |
    | -------------------- | ----------- |
    | Exemplo da Contoso       | Cria uma aplicação que inclui um modelo de dispositivo já criado para uma máquina dispensadora de bebidas. Utilize este modelo para começar a explorar o Azure IoT Central. |
    | Exemplo da Devkits       | Cria uma aplicação com modelos de dispositivo prontos para se poder ligar a dispositivos MXChip ou Raspberry Pi. Se for programador de dispositivos, utilize este modelo para experimentar um destes dispositivos. |
    | Aplicação personalizada   | Cria uma aplicação vazia que pode preencher com os seus próprios modelos de dispositivo e dispositivos. |

1. Se estiver a criar uma aplicação **Pay As You Go**, tem de selecionar o *Diretório*, a *Subscrição do Azure* e a *Região*. 

    - *Diretório* é o Azure Active Directory (AD) para criar a sua aplicação. Contém identidades de utilizadores, credenciais e outras informações organizacionais. Se não tiver um Azure AD, é criada uma para quando cria uma subscrição do Azure.

    - A *Subscrição do Azure* permite-lhe criar instâncias de serviços do Azure. O IoT Central aprovisiona os recursos na sua subscrição. Se não tiver uma subscrição do Azure, pode criá-la na [página de inscrição do Azure](https://aka.ms/createazuresubscription). Depois de criar a subscrição, regresse à página **Create Application** (Criar Aplicação). A subscrição nova aparece no menu pendente **Azure Subscription** (Subscrição do Azure).

    - A *região* é a localização física na qual pretende criar a aplicação. Por norma, deve escolher a região que está fisicamente mais perto dos dispositivos, de modo a obter um desempenho ideal. Pode ver as regiões em que o Azure IoT Central está disponível na página [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

    > [!Note]
    > Depois de escolher a região, não pode mover a aplicação para outra posteriormente.

1. Clique em **Criar**.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou uma aplicação do IoT Central. O passo seguinte sugerido é:

> [!div class="nextstepaction"]
> [Apresentação do IoT Central](https://docs.microsoft.com/azure/iot-central/overview-iot-central-tour)
