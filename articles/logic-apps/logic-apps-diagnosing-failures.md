---
title: Resolução de problemas e diagnóstico de falhas de fluxo de trabalho
description: Saiba como resolver problemas e diagnosticar problemas, erros e falhas nos seus fluxos de trabalho em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76905039"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Resolver problemas e diagnosticar falhas do fluxo de trabalho no Azure Logic Apps

A sua aplicação lógica gera informações que podem ajudá-lo a diagnosticar e depurar problemas na sua aplicação. Pode diagnosticar uma aplicação lógica analisando cada passo do fluxo de trabalho através do portal Azure. Ou, pode adicionar alguns passos a um fluxo de trabalho para depurar o tempo de execução.

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>Verifique o histórico do gatilho

Cada aplicação lógica começa com uma tentativa de gatilho, por isso, se o gatilho não disparar, siga estes passos:

1. Verifique o estado do gatilho [verificando o histórico do gatilho](../logic-apps/monitor-logic-apps.md#review-trigger-history). Para visualizar mais informações sobre a tentativa de gatilho, selecione o evento do gatilho, por exemplo:

   ![Ver estado do gatilho e história](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. Verifique as entradas do gatilho para confirmar que aparecem como espera. No **link Entradas,** selecione o link, que mostra o painel **de entradas.**

   As entradas de gatilho incluem os dados que o gatilho espera e requer iniciar o fluxo de trabalho. Rever estas entradas pode ajudá-lo a determinar se as entradas do gatilho estão corretas e se a condição foi satisfeita para que o fluxo de trabalho possa continuar.

   Por exemplo, a `feedUrl` propriedade aqui tem um valor de feed RSS incorreto:

   ![Rever entradas de gatilho para erros](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. Verifique as saídas dos gatilhos, se houver, para confirmar que aparecem como se espera. No **link Outputs**, selecione o link, que mostra o painel **de saídas.**

   As saídas do gatilho incluem os dados que o gatilho passa para o próximo passo no seu fluxo de trabalho. Rever estas saídas pode ajudá-lo a determinar se os valores corretos ou esperados passaram para o passo seguinte no seu fluxo de trabalho, por exemplo:

   ![Rever saídas de gatilho para erros](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > Se encontrar algum conteúdo que não reconheça, saiba mais sobre [diferentes tipos de conteúdo](../logic-apps/logic-apps-content-type.md) em Azure Logic Apps.

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>Verifique o histórico de execuções

Cada vez que o gatilho dispara para um item ou evento, o motor De Aplicações Lógicas cria e executa uma instância de fluxo de trabalho separada para cada item ou evento. Se uma corrida falhar, siga estes passos para rever o que aconteceu durante essa execução, incluindo o estado de cada passo no fluxo de trabalho mais as entradas e saídas para cada passo.

1. Verifique o estado de funcionamento do fluxo de trabalho [verificando o histórico de execuções](../logic-apps/monitor-logic-apps.md#review-runs-history). Para ver mais informações sobre uma corrida falhada, incluindo todos os passos que correm no seu estado, selecione a corrida falhada.

   ![Ver histórico de execução e selecionar corrida falhada](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. Depois de todos os passos na corrida aparecerem, expanda o primeiro passo falhado.

   ![Expandir primeiro passo falhado](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. Verifique as entradas falhadas do passo para confirmar se elas aparecem como espera.

1. Reveja os detalhes de cada passo numa corrida específica. Under **Runs history**, selecione a execução que deseja examinar.

   ![Rever o histórico de execuções](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![Ver detalhes da execução de uma aplicação lógica](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. Para examinar as entradas, saídas e quaisquer mensagens de erro para um passo específico, escolha esse passo para que a forma se expanda e mostre os detalhes. Por exemplo:

   ![Ver detalhes do passo](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Executar a depuração do runtime

Para ajudar na depuragem, pode adicionar passos de diagnóstico a um fluxo de trabalho de aplicações lógicas, juntamente com a revisão do gatilho e executa a história. Por exemplo, pode adicionar passos que utilizem o serviço [Webhook Tester](https://webhook.site/) para que possa inspecionar os pedidos HTTP e determinar o seu tamanho, forma e formato exatos.

1. Vá ao site [Webhook Tester](https://webhook.site/) e copie o URL único gerado.

1. Na sua aplicação lógica, adicione uma ação HTTP POST mais o conteúdo corporal que pretende testar, por exemplo, uma expressão ou outra saída de passo.

1. Cole o seu URL do Webhook Tester para a ação HTTP POST.

1. Para rever como um pedido é formado quando gerado a partir do motor De Aplicações Lógicas, executar a aplicação lógica e revisitar o site webhook Tester para obter mais detalhes.

## <a name="next-steps"></a>Passos seguintes

* [Monitorize a sua aplicação lógica](../logic-apps/monitor-logic-apps.md)
