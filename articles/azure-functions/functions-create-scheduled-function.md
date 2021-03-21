---
title: Criar uma função em Azure que funciona em um horário
description: Aprenda a usar o portal Azure para criar uma função que funcione com base num horário que define.
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: how-to
ms.date: 04/16/2020
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 14d918cc41f49b954f5cabf48572db5df829fd10
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98035194"
---
# <a name="create-a-function-in-the-azure-portal-that-runs-on-a-schedule"></a>Criar uma função no portal Azure que funciona em um horário

Aprenda a usar o portal Azure para criar uma função que funciona [sem servidor](https://azure.microsoft.com/solutions/serverless/) no Azure com base num horário que define.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

+ Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-function-app"></a>Criar uma aplicação de funções

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

A sua nova aplicação de função está pronta a ser utilizada. Em seguida, criará uma função na nova aplicação de funções.

:::image type="content" source="./media/functions-create-scheduled-function/function-app-create-success.png" alt-text="Aplicação Function App criada com êxito." border="true":::

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Criar uma função acionada por temporizador

1. Na sua aplicação de funções, selecione **Funções** e, em seguida, selecione **+ Adicionar** 

   :::image type="content" source="./media/functions-create-scheduled-function/function-add-function.png" alt-text="Adicione uma função no portal Azure." border="true":::

1. Selecione o modelo **de gatilho do temporizador.** 

    :::image type="content" source="./media/functions-create-scheduled-function/function-select-timer-trigger.png" alt-text="Selecione o gatilho do temporizador no portal Azure." border="true":::

1. Configure o novo gatilho com as definições especificadas na tabela abaixo da imagem e, em seguida, selecione **Criar Função**.

    :::image type="content" source="./media/functions-create-scheduled-function/function-configure-timer-trigger.png" alt-text="A screenshot mostra a página Nova Função com o modelo de gatilho do temporizador selecionado." border="true":::
    
    | Definição | Valor sugerido | Descrição |
    |---|---|---|
    | **Nome** | Predefinição | Define o nome da sua função acionada por temporizador. |
    | **Agenda** | 0 \* /1 \* \* \*\* | Uma [expressão CRON](functions-bindings-timer.md#ncrontab-expressions) de seis campos que agenda a função para ser executada todos os minutos. |

## <a name="test-the-function"></a>Testar a função

1. Na sua função, selecione **Código + Teste** e expanda os registos.

    :::image type="content" source="./media/functions-create-scheduled-function/function-test-timer-trigger.png" alt-text="Teste o gatilho do temporizador no portal Azure." border="true":::

1. Verifique a execução visualizando as informações escritas nos registos.

    :::image type="content" source="./media/functions-create-scheduled-function/function-view-timer-logs.png" alt-text="Veja o gatilho do temporizador no portal Azure." border="true":::

Agora, altere a agenda da função, para que seja executada a cada hora, em vez de cada minuto.

## <a name="update-the-timer-schedule"></a>Atualizar a agenda do temporizador

1. Na sua função, **selecione Integração**. Aqui, define as ligações de entrada e saída para a sua função e também define o horário. 

1. Selecione **Timer (myTimer)**.

    :::image type="content" source="./media/functions-create-scheduled-function/function-update-timer-schedule.png" alt-text="Atualize o horário no portal Azure." border="true":::

1. Atualize o valor **da Agenda** para `0 0 */1 * * *` , e, em seguida, selecione **Guardar**.  

    :::image type="content" source="./media/functions-create-scheduled-function/function-edit-timer-schedule.png" alt-text="Atualizar o calendário do temporizador da função no portal Azure." border="true":::

Agora tem uma função que funciona uma vez a cada hora, à hora.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Criaste uma função baseada num horário. Para obter mais informações sobre os gatilhos do temporizador, consulte [a execução do código de programação com funções Azure](functions-bindings-timer.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
