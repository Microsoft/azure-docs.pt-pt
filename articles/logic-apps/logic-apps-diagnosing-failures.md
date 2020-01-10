---
title: Solucionar problemas e diagnosticar falhas de fluxo de trabalho
description: Saiba como solucionar e diagnosticar problemas, erros e falhas em seus fluxos de trabalho em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: 79cc9d1bf7aa9e8848197525646b0a3646a558d2
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666810"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Solucionar problemas e diagnosticar falhas de fluxo de trabalho no aplicativo lógico do Azure

Seu aplicativo lógico gera informações que podem ajudá-lo a diagnosticar e depurar problemas em seu aplicativo. Você pode diagnosticar um aplicativo lógico examinando cada etapa no fluxo de trabalho por meio do portal do Azure. Ou você pode adicionar algumas etapas a um fluxo de trabalho para depuração em tempo de execução.

## <a name="review-trigger-history"></a>Examinar o histórico de gatilho

Cada aplicativo lógico começa com o gatilho. Se o gatilho não for acionado, primeiro verifique o histórico do gatilho. Esse histórico lista todas as tentativas de gatilho que seu aplicativo lógico fez e detalhes sobre entradas e saídas para cada tentativa de gatilho.

1. Para verificar se o gatilho foi acionado, no menu do aplicativo lógico, escolha **visão geral**. Em **histórico de gatilho**, examine o status do gatilho.

   > [!TIP]
   > Se não vir o menu da aplicação lógica, experimente regressar ao dashboard do Azure e reabra-a.

   ![Examinar o histórico de gatilho](./media/logic-apps-diagnosing-failures/logic-app-trigger-history-overview.png)

   > [!TIP]
   > * Se você não encontrar os dados esperados, tente selecionar **Atualizar** na barra de ferramentas.
   > * Se a lista mostrar muitas tentativas de gatilho e você não conseguir encontrar a entrada desejada, tente filtrar a lista.

   Estes são os status possíveis para uma tentativa de gatilho:

   | Estado | Descrição | 
   | ------ | ----------- | 
   | **Foi** | O gatilho verificou o ponto de extremidade e encontrou dados disponíveis. Normalmente, um status "disparado" também aparece junto com esse status. Caso contrário, a definição do gatilho pode ter uma condição ou `SplitOn` comando que não foi atendido. <p>Esse status pode ser aplicado a um gatilho manual, gatilho de recorrência ou gatilho de sondagem. Um gatilho pode ser executado com êxito, mas a execução em si ainda pode falhar quando as ações geram erros sem tratamento. | 
   | **Ignorado** | O gatilho verificou o ponto de extremidade, mas não encontrou dados. | 
   | **Falhou** | Ocorreu um erro. Para examinar todas as mensagens de erro geradas para um gatilho com falha, selecione essa tentativa de disparo e escolha **saídas**. Por exemplo, você pode encontrar entradas que não são válidas. | 
   ||| 

   Você pode ter várias entradas de gatilho com a mesma data e hora, o que acontece quando seu aplicativo lógico localiza vários itens. 
   Cada vez que o gatilho é acionado, o mecanismo de aplicativos lógicos cria uma instância de aplicativo lógico para executar o fluxo de trabalho. Por padrão, cada instância é executada em paralelo para que nenhum fluxo de trabalho precise aguardar antes de iniciar uma execução.

   > [!TIP]
   > Você pode verificar novamente o gatilho sem aguardar a próxima recorrência. Na barra de ferramentas visão geral, escolha **executar gatilho**e selecione o gatilho, que força uma verificação. Ou então, selecione **executar** na barra de ferramentas do designer de aplicativos lógicos.

3. Para examinar os detalhes de uma tentativa de gatilho, em **histórico de gatilho**, selecione essa tentativa de disparo. 

   ![Selecione uma tentativa de gatilho](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

4. Examine as entradas e as saídas geradas pelo gatilho. As saídas do gatilho mostram os dados provenientes do gatilho. Essas saídas podem ajudá-lo a determinar se todas as propriedades retornaram conforme o esperado.

   > [!NOTE]
   > Se você encontrar algum conteúdo que não entende, saiba como os aplicativos lógicos do Azure [lidam com diferentes tipos de conteúdo](../logic-apps/logic-apps-content-type.md).

   ![Saídas de gatilho](./media/logic-apps-diagnosing-failures/trigger-outputs.png)

## <a name="review-run-history"></a>Rever o histórico de execuções

Cada gatilho acionado inicia uma execução de fluxo de trabalho. Você pode examinar o que aconteceu durante essa execução, incluindo o status de cada etapa no fluxo de trabalho, além das entradas e saídas de cada etapa.

1. No menu da aplicação lógica, escolha **Descrição geral**. Em **histórico de execuções**, examine a execução do gatilho acionado.

   > [!TIP]
   > Se não vir o menu da aplicação lógica, experimente regressar ao dashboard do Azure e reabra-a.

   ![Revisar histórico de execuções](./media/logic-apps-diagnosing-failures/logic-app-runs-history-overview.png)

   > [!TIP]
   > * Se você não encontrar os dados esperados, tente selecionar **Atualizar** na barra de ferramentas.
   > * Se a lista mostrar muitas execuções e você não conseguir encontrar a entrada desejada, tente filtrar a lista.

   Estes são os status possíveis para uma execução:

   | Estado | Descrição | 
   | ------ | ----------- | 
   | **Foi** | Todas as ações foram bem-sucedidas. <p>Se ocorrerem falhas em uma ação específica, uma ação a seguir no fluxo de trabalho tratou dessa falha. | 
   | **Falhou** | Pelo menos uma ação falhou e nenhuma ação posterior no fluxo de trabalho foi configurada para lidar com a falha. | 
   | **Cancelar** | O fluxo de trabalho estava em execução, mas recebeu uma solicitação de cancelamento. | 
   | **Executado** | O fluxo de trabalho está em execução no momento. <p>Esse status pode ocorrer para fluxos de trabalho limitados ou devido ao plano de preços atual. Para obter mais informações, consulte os [limites de ação na página de preços](https://azure.microsoft.com/pricing/details/logic-apps/). Se você configurar o [log de diagnóstico](../logic-apps/logic-apps-monitor-your-logic-apps.md), também poderá obter informações sobre os eventos de restrição que acontecerem. | 
   ||| 

2. Examine os detalhes de cada etapa em uma execução específica. Em **histórico de execuções**, selecione a execução que você deseja examinar.

   ![Revisar histórico de execuções](./media/logic-apps-diagnosing-failures/logic-app-run-history.png)

   Se a execução foi bem-sucedida ou falhou, a exibição detalhes da execução mostra cada etapa e se ela teve êxito ou falhou.

   ![Ver detalhes da execução de uma aplicação lógica](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

3. Para examinar as entradas, as saídas e as mensagens de erro de uma etapa específica, escolha essa etapa para que a forma se expanda e mostre os detalhes. Por exemplo:

   ![Ver detalhes do passo](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Executar depuração de tempo de execução

Para ajudar na depuração, você pode adicionar etapas de diagnóstico a um fluxo de trabalho, juntamente com a revisão do gatilho e do histórico de execuções. Por exemplo, você pode adicionar etapas que usam o serviço [testador de webhook](https://webhook.site/) para que você possa inspecionar solicitações HTTP e determinar o tamanho, a forma e o formato exatos.

1. Visite o [testador de webhook](https://webhook.site/) e copie a URL exclusiva criada

2. Em seu aplicativo lógico, adicione uma ação HTTP POST com o conteúdo do corpo que você deseja testar, por exemplo, uma expressão ou outra saída de etapa.

3. Cole a URL para o testador do webhook na ação HTTP POST.

4. Para examinar como uma solicitação é formada quando gerada a partir do mecanismo de aplicativos lógicos, execute o aplicativo lógico e veja o testador de webhook para obter detalhes.

## <a name="next-steps"></a>Passos seguintes

[Monitorar seu aplicativo lógico](../logic-apps/logic-apps-monitor-your-logic-apps.md)
