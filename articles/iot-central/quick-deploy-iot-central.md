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
ms.openlocfilehash: 6b53f091b63c79493f6bed78e111b50d33adbf13
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442158"
---
# <a name="create-an-azure-iot-central-application"></a>Criar uma aplicação do Azure IoT Central

Como _construtor_, utilize a IU do Azure IoT Central para definir a sua aplicação do Microsoft Azure IoT Central. Este início rápido mostra como criar uma aplicação do Azure IoT Central que contém um exemplo do _modelo de dispositivo_ e _dispositivos_ simulados.

## <a name="create-an-application"></a>Criar uma aplicação

Navegue para a página do [Gestor de Aplicações](https://aka.ms/iotcentral) do Azure IoT Central. Tem de iniciar sessão com uma conta Microsoft pessoal ou escolar ou profissional.

Para começar a criar uma nova aplicação do Azure IoT Central, selecione **New Application** (Nova Aplicação). É encaminhado para a página **Create Application** (Criar Aplicação).

![Página da Aplicação do Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate.png)

Para criar uma nova aplicação do Azure IoT Central:

1. Escolha um plano de pagamento.
   - As aplicações de **avaliação** são gratuitas durante sete dias, até que expiram. Podem ser convertidas em Pay As You Go a qualquer momento antes de expirarem. Se criar um **versão de avaliação** aplicação, terá de introduzir as informações de contacto e escolha se pretende receber informações e sugestões da Microsoft.
   - As aplicações **Pay As You Go** são cobradas por dispositivo, sendo os primeiros cinco gratuitos. Se criar um **pay as you go** aplicação, tem de selecionar sua *diretório*, *subscrição do Azure*, e *região*:
      - *Diretório* é o Azure Active Directory (AD) para criar a sua aplicação. Contém identidades de utilizadores, credenciais e outras informações organizacionais. Se não tiver um Azure AD, é criada uma para quando cria uma subscrição do Azure.
      - A *Subscrição do Azure* permite-lhe criar instâncias de serviços do Azure. O IoT Central aprovisiona os recursos na sua subscrição. Se não tiver uma subscrição do Azure, pode criá-la na [página de inscrição do Azure](https://aka.ms/createazuresubscription). Depois de criar a subscrição, regresse à página **Create Application** (Criar Aplicação). A subscrição nova aparece no menu pendente **Azure Subscription** (Subscrição do Azure).
      - A *região* é a localização física na qual pretende criar a aplicação. Por norma, deve escolher a região que está fisicamente mais perto dos dispositivos, de modo a obter um desempenho ideal. Pode ver as regiões em que o Azure IoT Central está disponível na página [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/). Assim que escolher uma região, não pode mais tarde mudar a sua aplicação para uma região diferente.

      Saiba mais sobre os preços na [página de preços do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

1. Escolha um nome simpático para a aplicação, como **Contoso IoT**. O Azure IoT Central gera um prefixo de URL exclusivo para si. Pode alterar este prefixo de URL para algo mais memorável.

1. Escolha um modelo de aplicação. Os modelos de aplicação podem conter itens predefinidos, como modelos de dispositivo e dashboards, que o ajudam a começar.

    | Modelo de aplicação | Descrição |
    | -------------------- | ----------- |
    | Amostra Contoso       | Cria uma aplicação que inclui um modelo de dispositivo já criado para uma máquina dispensadora de bebidas. Utilize este modelo para começar a explorar o Azure IoT Central. |
    | Exemplo da Devkits       | Cria uma aplicação com modelos de dispositivo prontos para se poder ligar a dispositivos MXChip ou Raspberry Pi. Se for programador de dispositivos, utilize este modelo para experimentar um destes dispositivos. |
    | Aplicação personalizada   | Cria uma aplicação vazia que pode preencher com os seus próprios modelos de dispositivo e dispositivos. |

1. Selecione **Criar**.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou uma aplicação do IoT Central. O passo seguinte sugerido é:

> [!div class="nextstepaction"]
> [Apresentação do IoT Central](overview-iot-central-tour.md)
