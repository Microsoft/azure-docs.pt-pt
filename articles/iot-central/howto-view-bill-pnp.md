---
title: Exibir sua fatura e converter a avaliação para pagamento conforme o uso no aplicativo de IoT Central do Azure | Microsoft Docs
description: Como administrador, saiba como exibir sua fatura e o aplicativo de avaliação para pagamento à medida que você estiver usando o aplicativos de IoT Central do Azure
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: d27288718b7f224c78c9cf1f15d05198f628fd15
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879646"
---
# <a name="view-your-bill-in-an-iot-central-application"></a>Exibir sua fatura em um aplicativo IoT Central

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Este artigo descreve como, como administrador, você pode exibir sua fatura no aplicativo IoT Central do Azure na seção Administração e também como você pode converter sua versão de avaliação para pré-pago.

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

    ![Estado de avaliação](media/howto-view-bill-pnp/freetrialbilling.png)

1. Selecione **converter para pago conforme o uso**.

    ![Converter avaliação](media/howto-view-bill-pnp/convert.png)

1. Selecione o Azure Active Directory apropriado e, em seguida, a assinatura do Azure a ser usada para seu aplicativo pago pelo uso.

1. Depois de selecionar **converter**, seu aplicativo agora é um aplicativo pago conforme o uso e você começa a ser cobrado.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu sobre como exibir sua fatura no aplicativo IoT Central do Azure, a próxima etapa sugerida é saber mais sobre como [Personalizar a interface do usuário do aplicativo](howto-customize-ui-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) no Azure IOT central.