---
title: Solucionar problemas e diagnosticar falhas - Azure Logic Apps | Documentos da Microsoft
description: Saiba como resolver problemas e diagnosticar falhas de fluxo de trabalho no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.topic: article
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.date: 10/15/2017
ms.openlocfilehash: 62a74364939fffb6e06f51f1c0cabb6cce8c10e1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60999809"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Resolução de problemas e diagnosticar falhas de fluxo de trabalho no Azure Logic Apps

A aplicação lógica gera as informações que podem ajudá-lo a diagnosticar e depurar problemas na sua aplicação. Pode diagnosticar uma aplicação lógica ao rever cada passo no fluxo de trabalho através do portal do Azure. Ou pode adicionar alguns passos para um fluxo de trabalho para a depuração de tempo de execução.

## <a name="review-trigger-history"></a>Histórico de acionadores de revisão

Cada aplicação lógica começa com acionador. Se o acionador não dispara, verifique primeiro o histórico do acionador. Esse histórico apresenta uma lista de todas as tentativas de Acionador que fez a sua aplicação lógica e os detalhes sobre as entradas e saídas de cada tentativa de Acionador.

1. Para verificar se o acionador ativado, no menu da aplicação lógica, escolha **descrição geral**. Sob **histórico de Acionadores**, rever o estado do acionador.

   > [!TIP]
   > Se não vir o menu da aplicação lógica, experimente regressar ao dashboard do Azure e reabra-a.

   ![Histórico de acionadores de revisão](./media/logic-apps-diagnosing-failures/logic-app-trigger-history-overview.png)

   > [!TIP]
   > * Se não encontrar os dados que esperava, experimente selecionar **atualizar** na barra de ferramentas.
   > * Se a lista mostra disparar muitas tentativas e não é possível localizar a entrada do que mesmo, tente a filtrar a lista.

   Aqui estão os Estados possíveis para uma tentativa de Acionador:

   | Estado | Descrição | 
   | ------ | ----------- | 
   | **Foi efetuada com êxito** | O acionador selecionado o ponto final e encontrar dados disponíveis. Normalmente, um Estado de "Fired" também é apresentada juntamente com este estado. Se não, a definição de Acionador pode ter uma condição ou `SplitOn` comando que não foi cumprido. <p>Este estado pode aplicar a um acionador manual, o acionador de periodicidade ou o acionador de consulta. Um acionador pode ser executado com êxito, mas a execução propriamente dito ainda poderá falhar quando as ações geram erros de não processados. | 
   | **Ignorado** | O acionador verificado o ponto final, mas foi encontrado nenhum dado. | 
   | **Falhou** | Ocorreu um erro. Para rever as mensagens de erro gerado para um acionador com falha, selecione essa tentativa de Acionador e escolha **saídas**. Por exemplo, poderá encontrar entradas que não são válidas. | 
   ||| 

   Pode ter várias entradas de Acionador com o mesmo data e hora, o que acontece quando a aplicação lógica encontrar vários itens. 
   Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica para executar o fluxo de trabalho. Por predefinição, cada instância é executado em paralelo de modo a que nenhum fluxo de trabalho tem de aguardar antes de iniciar uma execução.

   > [!TIP]
   > Pode verificar novamente o acionador sem aguardar a próxima periodicidade. Na barra de ferramentas de descrição geral, escolha **executar o acionador**e selecione o acionador, que força uma verificação. Em alternativa, selecione **executar** na barra de ferramentas do estruturador de aplicações lógicas.

3. Para examinar os detalhes de uma tentativa de Acionador, em **histórico de Acionadores**, selecione essa tentativa de Acionador. 

   ![Selecione uma tentativa de Acionador](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

4. Reveja as entradas e saídas que gerou o acionador. Saídas de Acionador mostram os dados que vieram com o acionador. Essas saídas podem ajudar a determinar se todas as propriedades devolvido conforme esperado.

   > [!NOTE]
   > Se encontrar qualquer conteúdo que não entenda, saiba como o Azure Logic Apps [lida com diferentes tipos de conteúdo](../logic-apps/logic-apps-content-type.md).

   ![Saídas de Acionador](./media/logic-apps-diagnosing-failures/trigger-outputs.png)

## <a name="review-run-history"></a>Rever o histórico de execuções

Cada acionador desencadeado inicia um fluxo de trabalho a executar. Pode rever o que aconteceu durante que executar, incluindo o estado de cada passo no fluxo de trabalho, bem como as entradas e saídas de cada passo.

1. No menu da aplicação lógica, escolha **Descrição geral**. Sob **histórico de execuções**, reveja a execução do acionador desencadeado.

   > [!TIP]
   > Se não vir o menu da aplicação lógica, experimente regressar ao dashboard do Azure e reabra-a.

   ![Histórico de execuções de revisão](./media/logic-apps-diagnosing-failures/logic-app-runs-history-overview.png)

   > [!TIP]
   > * Se não encontrar os dados que esperava, experimente selecionar **atualizar** na barra de ferramentas.
   > * Se a lista mostra várias execuções, e não é possível localizar a entrada que desejar, tente filtrar a lista.

   Aqui estão os Estados possíveis para uma execução:

   | Estado | Descrição | 
   | ------ | ----------- | 
   | **Foi efetuada com êxito** | Todas as ações com êxito. <p>Se a quaisquer falhas ocorreram numa ação específica, uma ação seguinte no fluxo de trabalho processadas essa falha. | 
   | **Falhou** | Pelo menos uma ação falhou e não existem ações posteriores no fluxo de trabalho foram configuradas para processar a falha. | 
   | **Cancelada** | O fluxo de trabalho estava em execução, mas foi recebido um pedido de cancelamento. | 
   | **Em execução** | O fluxo de trabalho está atualmente em execução. <p>Este estado pode acontecer para fluxos de trabalho otimizados, ou devido ao plano de preços atual. Para obter mais informações, consulte a [limites de ação na página de preços](https://azure.microsoft.com/pricing/details/logic-apps/). Se configurou [registo de diagnósticos](../logic-apps/logic-apps-monitor-your-logic-apps.md), também pode obter informações sobre quaisquer eventos de limitação que aconteça. | 
   ||| 

2. Reveja os detalhes para cada passo numa execução específica. Sob **histórico de execuções**, selecione a execução que pretende examinar.

   ![Histórico de execuções de revisão](./media/logic-apps-diagnosing-failures/logic-app-run-history.png)

   Se a execução propriamente dito foi concluída com êxito ou falha, a vista de detalhes da execução mostra cada passo e com êxito ou falhadas.

   ![Ver detalhes da execução de uma aplicação lógica](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

3. Para examinar as entradas, saídas e quaisquer mensagens de erro para um passo específico, escolha esse passo para que se expande de forma e mostra os detalhes. Por exemplo:

   ![Ver detalhes do passo](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Efetuar a depuração de tempo de execução

Para ajudar a depurar, pode adicionar os passos para um fluxo de trabalho, juntamente com a rever o acionador e histórico de execuções do diagnóstico. Por exemplo, pode adicionar passos que utilizam o [Testador de Webhook](https://webhook.site/) para que possa inspecionar os pedidos HTTP e determinar seu tamanho exato, forma e formato de serviço.

1. Visite [Testador de Webhook](https://webhook.site/) e copie o URL exclusivo criado

2. Na sua aplicação lógica, adicione uma ação de HTTP POST com o conteúdo do corpo que pretende testar, por exemplo, uma expressão ou outro resultado de passo.

3. Cole o URL para o Testador de Webhook para a ação de HTTP POST.

4. Para rever como um pedido é formado quando gerados a partir do motor do Logic Apps, executar a aplicação lógica e ver o Testador de Webhook para obter detalhes.

## <a name="next-steps"></a>Passos Seguintes

[Monitorizar a sua aplicação lógica](../logic-apps/logic-apps-monitor-your-logic-apps.md)
