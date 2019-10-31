---
title: Criar uma função que é executada de acordo com uma agenda no Azure | Microsoft Docs
description: Saiba como criar uma função no Azure que é executada com base numa agenda definida por si.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/28/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 7fe84dd30099377af09b8100f5e5e7246a0d6328
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162405"
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Criar uma função no Azure que é acionada por um temporizador

Saiba como usar Azure Functions para criar uma função sem [servidor](https://azure.microsoft.com/solutions/serverless/) que é executada com base em um agendamento que você define.

![Criar uma aplicação de função no portal do Azure](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

+ Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-function-app"></a>Criar uma aplicação de Funções do Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Aplicação Function App criada com êxito.](./media/functions-create-first-azure-function/function-app-create-success.png)

Em seguida, vai criar uma função na aplicação Function App nova.

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Criar uma função acionada por temporizador

1. Expanda a aplicação de funções e clique no botão **+** , junto a **Funções**. Se esta for a primeira função na sua aplicação de funções, selecione **No portal** e **Continuar**. Caso contrário, avance para o passo três.

   ![Início rápido das funções no portal do Azure](./media/functions-create-scheduled-function/function-app-quickstart-choose-portal.png)

2. Escolha **Mais modelos** e **Terminar e ver os modelos**.

    ![Início Rápido das funções, escolher mais modelos](./media/functions-create-scheduled-function/add-first-function.png)

3. No campo de pesquisa, digite `timer` e configure o novo gatilho com as configurações, conforme especificado na tabela abaixo da imagem.

    ![Crie uma função acionada por um cronómetro no portal do Azure.](./media/functions-create-scheduled-function/functions-create-timer-trigger-2.png)

    | Definição | Valor sugerido | Descrição |
    |---|---|---|
    | **Nome** | Predefinição | Define o nome da sua função acionada por temporizador. |
    | **Agenda** | 0 \*/1 \* \* \* \* | Uma [expressão CRON](functions-bindings-timer.md#ncrontab-expressions) de seis campos que agenda a função para ser executada todos os minutos. |

4. Clique em **Criar**. É criada uma função na linguagem que escolheu e que é executada todos os minutos.

5. Veja as informações de rastreio escritas nos registos para verificar a execução.

    ![Visualizador de registo de funções no portal do Azure.](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

Agora, altere a agenda da função, para que seja executada a cada hora, em vez de cada minuto.

## <a name="update-the-timer-schedule"></a>Atualizar a agenda do temporizador

1. Expanda a função e clique em **Integrar**. É aqui que vai definir os enlaces de entrada e saída para a função, bem como a agenda. 

2. Introduza um novo valor por hora para a **Agenda** de `0 0 */1 * * *` e clique em **Guardar**.  

![Agenda de temporizador atualizado de funções no portal do Azure.](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

Tem agora uma função que é executada uma vez por hora. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Criou uma função que é executada com base numa agenda. Para obter mais informações sobre gatilhos de temporizador, consulte [agendar a execução de código com Azure Functions](functions-bindings-timer.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
