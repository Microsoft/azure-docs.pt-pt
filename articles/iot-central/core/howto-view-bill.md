---
title: Gerencie a sua conta e converta-se do plano de preços gratuitos na aplicação Azure IoT Central / Microsoft Docs
description: Como administrador, aprenda a gerir a sua conta e passe do plano de preços gratuitos para um plano de preços padrão na sua aplicação Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 515f5f665e947ff9594cce6dbbaea9b5e0c50ebf
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999704"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Gerencie a sua conta numa aplicação IoT Central

Este artigo descreve como, como administrador, pode gerir a sua faturação Azure IoT Central. Pode mover a sua aplicação do plano de preços gratuitos para um plano de preços padrão, e também atualizar ou desvalorizar o seu plano de preços.

Para aceder à secção **Administração,** tem de estar na função *de Administrador* ou ter uma *função de utilizador personalizada* que lhe permite visualizar a faturação. Se criar uma aplicação Azure IoT Central, é automaticamente atribuída à função **administrador.**

## <a name="move-from-free-to-standard-pricing-plan"></a>Passar de plano de preços gratuitos para padrão

- As aplicações que utilizam o plano de preços gratuitos são gratuitas durante sete dias antes de expirarem. Para evitar perder dados, pode movê-los para um plano de preços padrão a qualquer momento antes de expirarem.
- As aplicações que utilizam um plano de preços padrão são carregadas por dispositivo, com os dois primeiros dispositivos gratuitos, por aplicação.

Saiba mais sobre os preços na [página de preços do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

Na secção de preços, pode mover a sua aplicação do free para um plano de preços padrão.

Para completar este processo de self-service, siga estes passos:

1. Vá à página **de preços** na secção **Administração.**

    ![Estado do julgamento](media/howto-view-bill/freetrialbilling.png)

1. Selecione **Converter para um plano pago.**

    ![Converter julgamento](media/howto-view-bill/convert.png)

1. Selecione o Diretório Ativo Azure apropriado e, em seguida, a subscrição Azure para usar para a sua aplicação que usa um plano pago.

1. Depois de selecionar **Converte,** a sua aplicação utiliza agora um plano pago e começa a ser faturada.

> [!Note]
> Por padrão, é convertido para um plano de preços *Standard 2.*

## <a name="how-to-change-your-application-pricing-plan"></a>Como alterar o seu plano de preços de aplicação

As aplicações que utilizam um plano de preços padrão são carregadas por dispositivo, com os dois primeiros dispositivos gratuitos, por aplicação.

Na secção de preços, pode atualizar ou desvalorizar o seu plano de preços Azure IoT a qualquer momento.

1. Vá à página **de preços** na secção **Administração.**

    ![Plano de atualização](media/howto-view-bill/pricing.png)

1. Selecione o **Plano** e, em seguida, selecione **Guardar** para atualizar ou downgrade.

## <a name="view-your-bill"></a>Ver a sua fatura

1. Selecione o Diretório Ativo Azure apropriado e, em seguida, a subscrição Azure para usar para a sua aplicação que usa um plano pago.

1. Depois de selecionar **Converte,** a sua aplicação utiliza agora um plano pago e começa a ser faturada.

> [!Note]
> Por padrão, é convertido para um plano de preços *Standard 2.*

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu como gerir a sua conta na aplicação Azure IoT Central, o próximo passo sugerido é aprender sobre [a aplicação Customize UI](howto-customize-ui.md) em Azure IoT Central.