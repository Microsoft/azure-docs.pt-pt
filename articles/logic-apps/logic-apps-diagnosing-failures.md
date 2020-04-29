---
title: Resolução de problemas e diagnóstico de falhas de fluxo de trabalho
description: Aprenda a resolver problemas e diagnosticar problemas, erros e falhas nos seus fluxos de trabalho em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76905039"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Problemas de resolução e diagnóstico de falhas de fluxo de trabalho em Aplicações Lógicas Azure

A sua aplicação lógica gera informações que podem ajudá-lo a diagnosticar e depurar problemas na sua aplicação. Pode diagnosticar uma aplicação lógica revendo cada passo no fluxo de trabalho através do portal Azure. Ou, pode adicionar alguns passos a um fluxo de trabalho para depuração em tempo de funcionamento.

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>Verifique o histórico do gatilho

Cada aplicação lógica começa com uma tentativa de gatilho, por isso, se o gatilho não disparar, siga estes passos:

1. Verifique o estado do gatilho [verificando o histórico do gatilho](../logic-apps/monitor-logic-apps.md#review-trigger-history). Para ver mais informações sobre a tentativa de gatilho, selecione o evento de disparo, por exemplo:

   ![Ver estado do gatilho e história](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. Verifique as inputs do gatilho para confirmar que aparecem como espera. Sob a **ligação de entradas,** selecione o link, que mostra o painel de **entradas.**

   As inputs do gatilho incluem os dados que o gatilho espera e exige minicioso para iniciar o fluxo de trabalho. Rever estas inputs pode ajudá-lo a determinar se as inputs do gatilho estão corretas e se a condição foi satisfeita para que o fluxo de trabalho possa continuar.

   Por exemplo, `feedUrl` a propriedade aqui tem um valor de alimentação RSS incorreto:

   ![Rever as inputs do gatilho para erros](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. Verifique as saídas dos gatilhos, se houver, para confirmar que aparecem como espera. No **link Saídas,** selecione o link, que mostra o painel **de saídas.**

   As saídas do gatilho incluem os dados que o gatilho passa para o próximo passo no seu fluxo de trabalho. Rever estas saídas pode ajudá-lo a determinar se os valores corretos ou esperados passaram para o próximo passo no seu fluxo de trabalho, por exemplo:

   ![Rever saídas de gatilho para erros](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > Se encontrar algum conteúdo que não reconheça, saiba mais sobre [diferentes tipos](../logic-apps/logic-apps-content-type.md) de conteúdo em Aplicações Lógicas Azure.

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>Check runs histórico

Cada vez que o gatilho dispara para um item ou evento, o motor Logic Apps cria e executa uma instância separada de fluxo de trabalho para cada item ou evento. Se uma corrida falhar, siga estes passos para rever o que aconteceu durante essa execução, incluindo o estado de cada passo no fluxo de trabalho mais as entradas e saídas para cada passo.

1. Verifique o estado de funcionamento do fluxo de trabalho [verificando o histórico de execução](../logic-apps/monitor-logic-apps.md#review-runs-history). Para ver mais informações sobre uma execução falhada, incluindo todos os passos que correm no seu estado, selecione a execução falhada.

   ![Ver histórico de execução e selecionar execução falhada](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. Depois de todos os passos na corrida aparecerem, expanda o primeiro passo falhado.

   ![Expandir primeiro passo falhado](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. Verifique as inputs do passo falhado para confirmar se aparecem como espera.

1. Reveja os detalhes para cada passo numa corrida específica. Sob a história de **Runs,** selecione a execução que pretende examinar.

   ![A revisão corre história](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![Ver detalhes da execução de uma aplicação lógica](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. Para examinar as inputs, saídas e quaisquer mensagens de erro para um passo específico, escolha esse passo para que a forma se expanda e mostre os detalhes. Por exemplo:

   ![Ver detalhes do passo](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Executar a depuração do runtime

Para ajudar na depuração, pode adicionar passos de diagnóstico a um fluxo de trabalho de aplicações lógicas, juntamente com a revisão do gatilho e executa o histórico. Por exemplo, pode adicionar passos que utilizam o serviço [Webhook Tester](https://webhook.site/) para que possa inspecionar os pedidos http e determinar o seu tamanho, forma e forma exata.

1. Vá ao site [Webhook Tester](https://webhook.site/) e copie o URL único gerado.

1. Na sua aplicação lógica, adicione uma ação HTTP POST mais o conteúdo corporal que pretende testar, por exemplo, uma expressão ou outra saída de passo.

1. Colhe o seu URL do Teste de Webhook na ação HTTP POST.

1. Para rever como um pedido é formado quando gerado a partir do motor Logic Apps, executar a aplicação lógica e revisitar o site Webhook Tester para mais detalhes.

## <a name="next-steps"></a>Passos seguintes

* [Monitorize a sua aplicação lógica](../logic-apps/monitor-logic-apps.md)
