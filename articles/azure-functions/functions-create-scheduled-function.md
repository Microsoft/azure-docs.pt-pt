---
title: Create a function that runs on a schedule in Azure
description: Saiba como criar uma função no Azure que é executada com base numa agenda definida por si.
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: quickstart
ms.date: 03/28/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 8e89c3923daab15793707ff99dbbed6deeb6a0b0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227180"
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Criar uma função no Azure que é acionada por um temporizador

Learn how to use Azure Functions to create a [serverless](https://azure.microsoft.com/solutions/serverless/) function that runs based on a schedule that you define.

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

1. Expanda a aplicação de funções e clique no botão **+** , junto a **Funções**. Se esta for a primeira função na sua aplicação de funções, selecione **No portal** e **Continuar**. Otherwise, go to step 3.

   ![Início rápido das funções no portal do Azure](./media/functions-create-scheduled-function/function-app-quickstart-choose-portal.png)

2. Escolha **Mais modelos** e **Terminar e ver os modelos**.

    ![Início Rápido das funções, escolher mais modelos](./media/functions-create-scheduled-function/add-first-function.png)

3. In the search field, type `timer` and configure the new trigger with the settings as specified in the table below the image.

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

Criou uma função que é executada com base numa agenda. For more information about timer triggers, see [Schedule code execution with Azure Functions](functions-bindings-timer.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
