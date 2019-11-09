---
title: Gerencie sua fatura e converta a avaliação para pagamento conforme o uso no aplicativo de IoT Central do Azure | Microsoft Docs
description: Como administrador, saiba como gerenciar sua fatura e o seu aplicativo de avaliação para pagamento à medida que você estiver em sua aplicação de IoT Central do Azure
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 11bcdaab34cf4d82b688a578ab2d9ee686ffe07e
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73894815"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Gerenciar sua fatura em um aplicativo IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este artigo descreve como, como administrador, você pode gerenciar sua fatura no aplicativo IoT Central do Azure na seção Administração e também como você pode converter sua versão de avaliação para pré-pago.

Para acessar e usar a seção **Administração** , você deve estar na função **administrador** para um aplicativo IOT central do Azure. Se você criar um aplicativo de IoT Central do Azure, você será automaticamente atribuído à função de **administrador** para esse aplicativo.

## <a name="view-your-bill"></a>Ver a sua fatura

Para exibir sua fatura, vá para a página de **cobrança** na seção **Administração** . A página de cobrança do Azure é aberta em uma nova guia, na qual você pode ver a fatura de cada um dos seus aplicativos de IoT Central do Azure.

## <a name="convert-your-trial-to-pay-as-you-go"></a>Converta sua avaliação para pré-paga

- Os aplicativos de **avaliação** são gratuitos por sete dias antes de expirarem. Podem ser convertidas em Pay As You Go a qualquer momento antes de expirarem.
- Os aplicativos **pagos conforme o uso** são cobrados por dispositivo, com os primeiros cinco dispositivos gratuitos, por assinatura.

Saiba mais sobre os preços na [página de preços do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

Na seção de cobrança, você pode converter seus aplicativos de avaliação em pré-pago.

Para concluir esse processo de autoatendimento, siga estas etapas:

1. Vá para a página de **cobrança** na seção **Administração** .

    ![Estado de avaliação](media/howto-view-bill/freetrialbilling.png)

1. Selecione **converter para pago conforme o uso**.

    ![Converter avaliação](media/howto-view-bill/convert.png)

1. Selecione o Azure Active Directory apropriado e, em seguida, a assinatura do Azure a ser usada para seu aplicativo pago pelo uso.

1. Depois de selecionar **converter**, seu aplicativo agora é um aplicativo pago conforme o uso e você começa a ser cobrado.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu sobre como gerenciar sua fatura no aplicativo IoT Central do Azure, a próxima etapa sugerida é aprender a [Personalizar a interface do usuário do aplicativo](howto-customize-ui.md) no Azure IOT central.