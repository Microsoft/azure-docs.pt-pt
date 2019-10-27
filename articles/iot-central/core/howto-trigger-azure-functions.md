---
title: Disparar Azure Functions usando WebHooks no Azure IoT Central
description: Crie um aplicativo de funções que é executado cada vez que uma regra é disparada no Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a9590e5554956418859a07b43fb57724783343fe
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952852"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Disparar Azure Functions usando WebHooks no Azure IoT Central

*Este tópico se aplica a construtores e administradores.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Use Azure Functions para executar o código sem servidor na saída do webhook de regras de IoT Central. Você não precisa provisionar uma VM ou publicar um aplicativo Web para usar Azure Functions, mas, em vez disso, você pode executar esse código sem servidor. Use Azure Functions para transformar o conteúdo do webhook antes de enviá-lo para seu destino final, como um banco de dados SQL ou uma grade de eventos.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="how-to-connect-azure-functions"></a>Como se conectar Azure Functions

1. [Crie um novo aplicativo de funções no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Criar um novo aplicativo de funções no portal do Azure](media/howto-trigger-azure-functions/createfunction.png)

2. Expanda seu aplicativo de funções e selecione o **botão +** ao lado de funções. Se essa função for a primeira em seu aplicativo de funções, selecione **no portal** como ambiente de desenvolvimento e selecione **continuar**.

    ![Escolha a função personalizada no aplicativo de funções](media/howto-trigger-azure-functions/customfunction.png)

3. Escolha modelo de **webhook + API** e selecione **criar**. Este tópico usa o Azure function baseado no .NET.

    ![Selecionar gatilho de webhook genérico](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. Em sua nova função, selecione **</> obter URL de função**, em seguida, copie e salve o valor. Utilize este valor para configurar o webhook.

    ![Obter a URL da função](media/howto-trigger-azure-functions/getfunctionurl.png)

4. Em IoT Central, navegue até a regra que você deseja conectar ao seu aplicativo de funções.

5. Adicionar uma ação de webhook. Insira um **nome de exibição** e cole na URL da função que você copiou anteriormente para a **URL de retorno de chamada**.

    ![Insira a URL da função no campo URL de retorno de chamada](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Salve a regra. Agora, quando a regra é disparada, o webhook invoca o aplicativo de funções para ser executado. Em seu aplicativo de funções, você pode selecionar **Monitor** para ver o histórico de invocação da função. Você pode usar o app insights ou a exibição clássica para examinar o histórico.

    ![Monitorar o histórico de invocação da função](media/howto-trigger-azure-functions/monitorfunction.PNG)

Para obter mais informações, visite o artigo Azure Functions sobre como [criar uma função disparada por um webhook genérico](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function).

## <a name="next-steps"></a>Passos seguintes
Agora que você aprendeu como configurar e usar WebHooks, a próxima etapa sugerida é explorar a [criação de fluxos de trabalho em Microsoft Flow](howto-add-microsoft-flow.md).
