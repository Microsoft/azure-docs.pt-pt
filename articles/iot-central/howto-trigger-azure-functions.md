---
title: Funções do Azure de Acionador com webhooks no Azure IoT Central
description: Crie uma aplicação de função que será executada sempre que é acionada uma regra no Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 512956d2de0f9a838cc6378345a334e489d1d120
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57306872"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Funções do Azure de Acionador com webhooks no Azure IoT Central

*Este tópico aplica-se para criadores e administradores.*

Utilize as funções do Azure para executar código sem servidor o webhook de saída do Centro de IoT regras. Não precisa de aprovisionar uma VM ou publicar uma aplicação web para utilizar as funções do Azure, mas em vez disso, pode executar esse código serverlessly. Utilize as funções do Azure para transformar o payload do webook antes de os enviar para o destino final, como uma base de dados SQL ou o Event Grid.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="how-to-connect-azure-functions"></a>Como ligar as funções do Azure

1. [Criar uma nova aplicação de função no Portal do Azure](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Criar uma nova aplicação de função no Portal do Azure](media/howto-trigger-azure-functions/createfunction.png)

2. Expanda a sua aplicação de funções e selecione o **botão +** ao lado de funções. Se esta função for a primeira na sua aplicação de funções, selecione **Função personalizada**. É apresentado o conjunto completo de modelos de função.

    ![Escolha a função personalizada na aplicação de função](media/howto-trigger-azure-functions/customfunction.png)

3. No campo de pesquisa, escreva **"genérico"** e, em seguida, escolha o idioma pretendido para o modelo de Acionador de webhook genérico. Este tópico utiliza uma função C#. 

    ![Selecione o acionador de webhook genérico](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. Na função nova, selecione **<> / obter URL da função**e, em seguida, copie e guarde o valor. Utilize este valor para configurar o webhook.

    ![Obter o URL da função](media/howto-trigger-azure-functions/getfunctionurl.png)

4. No Centro de IoT, navegue para a regra que pretende ligar à sua aplicação de função.

5. Adicione uma ação do webhook. Introduza um **nome a apresentar** e cole o URL de função que copiou anteriormente para **URL de retorno de chamada**.

    ![Introduza o URL de função no campo URL de retorno de chamada](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Guarde a regra. Agora quando a regra é acionada, o webhook invoca a aplicação de funções para executar. Na sua aplicação de função, pode selecionar **Monitor** para ver o histórico de invocação de função. Pode utilizar o App Insights ou a vista clássica para ver o histórico.

    ![Monitorizar o histórico de invocação da função](media/howto-trigger-azure-functions/monitorfunction.PNG)

Para obter mais informações, visite o artigo de funções do Azure sobre [criar uma função acionada por um webhook genérico](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function).

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu a configurar e utilizar webhooks, a próxima etapa sugerida é explorar [criação de fluxos de trabalho no Microsoft Flow](howto-add-microsoft-flow.md).
