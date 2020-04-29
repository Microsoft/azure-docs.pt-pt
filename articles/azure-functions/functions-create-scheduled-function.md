---
title: Criar uma função que funciona em horário em Azure
description: Saiba como criar uma função no Azure que é executada com base numa agenda definida por si.
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: how-to
ms.date: 03/28/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: e100a2d3a3718b302a44cbdecf462a99d9c823e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756493"
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Criar uma função no Azure que é acionada por um temporizador

Aprenda a usar funções Azure para criar uma função [sem servidorque](https://azure.microsoft.com/solutions/serverless/) funciona com base num horário que define.

![Criar uma aplicação de função no portal do Azure](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

+ Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-function-app"></a>Criar uma aplicação de Funções do Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Aplicação Function App criada com êxito.](./media/functions-create-first-azure-function/function-app-create-success.png)

Em seguida, vai criar uma função na aplicação Function App nova.

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Criar uma função acionada por temporizador

1. Expanda a sua **+** aplicação de função e clique no botão ao lado das **Funções**. Se esta for a primeira função na sua aplicação de funções, selecione **No portal** e **Continuar**. Caso contrário, vá para o passo 3.

   ![Início rápido das funções no portal do Azure](./media/functions-create-scheduled-function/function-app-quickstart-choose-portal.png)

2. Escolha **Mais modelos** e **Terminar e ver os modelos**.

    ![Início Rápido das funções, escolher mais modelos](./media/functions-create-scheduled-function/add-first-function.png)

3. No campo de `timer` pesquisa, digite e configure o novo gatilho com as definições especificadas na tabela abaixo da imagem.

    ![Crie uma função acionada por um cronómetro no portal do Azure.](./media/functions-create-scheduled-function/functions-create-timer-trigger-2.png)

    | Definição | Valor sugerido | Descrição |
    |---|---|---|
    | **Nome** | Predefinição | Define o nome da sua função acionada por temporizador. |
    | **Agenda** | 0 \*/1 \* \* \*\* | Uma [expressão CRON](functions-bindings-timer.md#ncrontab-expressions) de seis campos que agenda a função para ser executada todos os minutos. |

4. Clique em **Criar**. Uma função é criada na sua linguagem escolhida que funciona a cada minuto, no minuto.

5. Veja as informações de rastreio escritas nos registos para verificar a execução.

    ![Visualizador de registo de funções no portal do Azure.](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

Agora, altere a agenda da função, para que seja executada a cada hora, em vez de cada minuto.

## <a name="update-the-timer-schedule"></a>Atualizar a agenda do temporizador

1. Expanda a função e clique em **Integrar**. É aqui que vai definir os enlaces de entrada e saída para a função, bem como a agenda. 

2. Introduza um novo valor por hora para a **Agenda** de `0 0 */1 * * *` e clique em **Guardar**.  

![Agenda de temporizador atualizado de funções no portal do Azure.](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

Agora tens uma função que funciona uma vez a cada hora, à hora.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Criou uma função que é executada com base numa agenda. Para obter mais informações sobre os gatilhos do temporizador, consulte a execução do [código de programação com funções Azure](functions-bindings-timer.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
