---
title: Criar uma função que funciona em um horário em Azure
description: Saiba como criar uma função no Azure que é executada com base numa agenda definida por si.
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: how-to
ms.date: 04/16/2020
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 9e542c493c02174364072f91d092f05ad9ec69cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90973100"
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Criar uma função no Azure que é acionada por um temporizador

Aprenda a usar as Funções Azure para criar uma função [sem servidor](https://azure.microsoft.com/solutions/serverless/) que funciona com base num horário que define.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

+ Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-function-app"></a>Criar uma aplicação de Funções do Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

A sua nova aplicação de função está pronta a ser utilizada. Em seguida, criará uma função na nova aplicação de funções.

:::image type="content" source="./media/functions-create-scheduled-function/function-app-create-success.png" alt-text="Aplicação Function App criada com êxito." border="true":::

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Criar uma função acionada por temporizador

1. Na sua aplicação de funções, selecione **Funções**e, em seguida, selecione **+ Adicionar** 

   :::image type="content" source="./media/functions-create-scheduled-function/function-add-function.png" alt-text="Aplicação Function App criada com êxito." border="true":::

1. Selecione o modelo **de gatilho do temporizador.** 

    :::image type="content" source="./media/functions-create-scheduled-function/function-select-timer-trigger.png" alt-text="Aplicação Function App criada com êxito." border="true":::

1. Configure o novo gatilho com as definições especificadas na tabela abaixo da imagem e, em seguida, selecione **Criar Função**.

    :::image type="content" source="./media/functions-create-scheduled-function/function-configure-timer-trigger.png" alt-text="Aplicação Function App criada com êxito." border="true":::
    
    | Definição | Valor sugerido | Descrição |
    |---|---|---|
    | **Nome** | Predefinição | Define o nome da sua função acionada por temporizador. |
    | **Agenda** | 0 \* /1 \* \* \*\* | Uma [expressão CRON](functions-bindings-timer.md#ncrontab-expressions) de seis campos que agenda a função para ser executada todos os minutos. |

## <a name="test-the-function"></a>Testar a função

1. Na sua função, selecione **Código + Teste** e expanda os registos.

    :::image type="content" source="./media/functions-create-scheduled-function/function-test-timer-trigger.png" alt-text="Aplicação Function App criada com êxito." border="true":::

1. Verifique a execução visualizando as informações escritas nos registos.

    :::image type="content" source="./media/functions-create-scheduled-function/function-view-timer-logs.png" alt-text="Aplicação Function App criada com êxito." border="true":::

Agora, altere a agenda da função, para que seja executada a cada hora, em vez de cada minuto.

## <a name="update-the-timer-schedule"></a>Atualizar a agenda do temporizador

1. Na sua função, **selecione Integração**. Aqui, define as ligações de entrada e saída para a sua função e também define o horário. 

1. Selecione **Timer (myTimer)**.

    :::image type="content" source="./media/functions-create-scheduled-function/function-update-timer-schedule.png" alt-text="Aplicação Function App criada com êxito." border="true":::

1. Atualize o valor **da Agenda** para `0 0 */1 * * *` , e, em seguida, selecione **Guardar**.  

    :::image type="content" source="./media/functions-create-scheduled-function/function-edit-timer-schedule.png" alt-text="Aplicação Function App criada com êxito." border="true":::

Agora tem uma função que funciona uma vez a cada hora, à hora.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Criaste uma função baseada num horário. Para obter mais informações sobre os gatilhos do temporizador, consulte [a execução do código de programação com funções Azure](functions-bindings-timer.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
