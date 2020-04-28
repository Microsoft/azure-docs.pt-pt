---
title: Monitorize o estado, veja o histórico e instale alertas
description: Aplicações lógicas de resolução de problemas verificando o estado de execução, analisando o histórico do gatilho e permitindo alertas em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 495877f1c839de2cf3583a37180054c91bd9f139
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76907776"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>Monitorize o estado da execução, reveja o histórico do gatilho e instale alertas para aplicações lógicas do Azure

Depois de [criar e executar uma aplicação lógica,](../logic-apps/quickstart-create-first-logic-app-workflow.md)pode verificar o estado de execução da aplicação lógica, executar [história,](#review-runs-history) [desencadear histórico](#review-trigger-history)e desempenho. Para obter notificações sobre falhas ou outros problemas possíveis, instale [alertas](#add-azure-alerts). Por exemplo, pode criar um alerta que deteta "quando mais de cinco corridas falham numa hora".

Para monitorização de eventos em tempo real e depuração mais rica, configura o registo de diagnósticos para a sua aplicação lógica utilizando [registos do Monitor Azure](../azure-monitor/overview.md). Este serviço Azure ajuda-o a monitorizar os ambientes de nuvem e no local para que possa manter mais facilmente a sua disponibilidade e desempenho. Você pode então encontrar e ver eventos, tais como eventos de desencadeamento, eventos de execução e eventos de ação. Ao armazenar esta informação nos [registos do Monitor Azure,](../azure-monitor/platform/data-platform-logs.md)pode criar consultas de [registo](../azure-monitor/log-query/log-query-overview.md) que o ajudam a encontrar e analisar esta informação. Também pode utilizar estes dados de diagnóstico com outros serviços Azure, como o Azure Storage e o Azure Event Hubs. Para mais informações, consulte [aplicações lógicas do Monitor utilizando o Monitor Azure](../logic-apps/monitor-logic-apps-log-analytics.md).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>A revisão corre história

Cada vez que o gatilho dispara para um item ou evento, o motor Logic Apps cria e executa uma instância separada de fluxo de trabalho para cada item ou evento. Por padrão, cada instância de fluxo de trabalho corre paralelamente para que nenhum fluxo de trabalho tenha de esperar antes de iniciar uma corrida. Pode rever o que aconteceu durante essa execução, incluindo o estado de cada passo no fluxo de trabalho mais as entradas e saídas para cada passo.

1. No [portal Azure,](https://portal.azure.com)encontre e abra a sua aplicação lógica no Logic App Designer.

   Para encontrar a sua aplicação lógica , `logic apps`na caixa de pesquisa principal do Azure, introduza, e depois selecione **Aplicações Lógicas**.

   ![Localizar e selecionar o serviço "Logic Apps"](./media/monitor-logic-apps/find-your-logic-app.png)

   O portal Azure mostra todas as aplicações lógicas que estão associadas às suas subscrições do Azure. Pode filtrar esta lista com base no nome, subscrição, grupo de recursos, localização, e assim por diante.

   ![Ver aplicativos lógicos associados a subscrições](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Selecione a sua aplicação lógica e, em seguida, selecione **visão geral**.

   No painel de visão geral, sob **a história de Runs**, todo o passado, atual, e quaisquer corridas de espera para a sua aplicação lógica aparecem. Se a lista mostrar muitas corridas, e não encontrar a entrada que deseja, tente filtrar a lista. Se não encontrar os dados que espera, tente selecionar **Refresh** na barra de ferramentas.

   ![Visão geral, corre história e outras informações de aplicações lógicas](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   Aqui estão os possíveis status para uma aplicação lógica executada:

   | Estado | Descrição |
   |--------|-------------|
   | **Cancelada** | O fluxo de trabalho estava a funcionar, mas recebeu um pedido de cancelamento. |
   | **Falhou** | Pelo menos uma ação falhou, e nenhuma ação posterior no fluxo de trabalho foi criada para lidar com a falha |
   | **A executar** | O fluxo de trabalho está a funcionar. <p>Este estatuto também pode aparecer para fluxos de trabalho estrangulados ou devido ao plano de preços atual. Para mais informações, consulte os [limites](https://azure.microsoft.com/pricing/details/logic-apps/)de ação na página de preços . Se configurar o [registo de diagnósticos,](../logic-apps/monitor-logic-apps.md)pode obter informações sobre quaisquer eventos de aceleração que ocorram. |
   | **Bem-sucedido** | Todas as ações foram bem sucedidas. <p>**Nota:** Se ocorrerem falhas numa ação específica, uma ação posterior no fluxo de trabalho tratou dessa falha. |
   | **A aguardar** | O fluxo de trabalho ainda não começou ou é interrompido, por exemplo, devido a um fluxo de trabalho anterior que ainda está em execução. |
   |||

1. Para rever os passos e outras informações para uma execução específica, sob a história de **Runs,** selecione essa execução.

   ![Selecione uma execução específica para rever](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   O painel de execução da **aplicação Logic** mostra cada passo na execução selecionada, o estado de execução de cada passo, e o tempo decorrido para cada passo correr, por exemplo:

   ![Cada ação na execução específica](./media/monitor-logic-apps/logic-app-run-pane.png)

   Para visualizar esta informação no formulário da lista, na barra de ferramentas de execução da **aplicação Logic,** selecione **Executar Detalhes**.

   ![Na barra de ferramentas, selecione "Executar Detalhes"](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   A visão 'Dados de Execução' mostra cada passo, o seu estado e outras informações.

   ![Reveja detalhes sobre cada passo na corrida](./media/monitor-logic-apps/review-logic-app-run-details.png)

   Por exemplo, você pode obter a propriedade **Correlation ID** da corrida, que você pode precisar quando você usar a [API REST para Aplicações Lógicas](https://docs.microsoft.com/rest/api/logic).

1. Para obter mais informações sobre um passo específico, selecione qualquer uma das opções:

   * No painel de execução da **aplicação Logic,** selecione o passo para que a forma se expanda. Pode agora visualizar informações como inputs, saídas e quaisquer erros que ocorreram nesse passo, por exemplo:

     ![Na aplicação lógica executar painel, vista passo falhado](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * Na **aplicação Lógica executar detalhes** painel, selecione o passo que deseja.

     ![Em painel de detalhes de execução, vista passo falhado](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     Pode agora visualizar informações como inputs e saídas para esse passo, por exemplo:

   > [!NOTE]
   > Todos os detalhes e eventos de tempo de execução são encriptados dentro do serviço De Aplicações Lógicas. Só são desencriptados quando um utilizador pede para visualizar esses dados. Pode [ocultar inputs e saídas no histórico](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate) de execução ou controlar o acesso dos utilizadores a estas informações utilizando o Controlo de [Acesso baseado em Funções Azure (RBAC)](../role-based-access-control/overview.md).

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>Rever o histórico do gatilho

Cada aplicação lógica começa com um gatilho. O histórico do gatilho lista todas as tentativas de gatilho que a sua aplicação lógica fez e informações sobre as inputs e saídas para cada tentativa de gatilho.

1. No [portal Azure,](https://portal.azure.com)encontre e abra a sua aplicação lógica no Logic App Designer.

   Para encontrar a sua aplicação lógica , `logic apps`na caixa de pesquisa principal do Azure, introduza, e depois selecione **Aplicações Lógicas**.

   ![Localizar e selecionar o serviço "Logic Apps"](./media/monitor-logic-apps/find-your-logic-app.png)

   O portal Azure mostra todas as aplicações lógicas que estão associadas às suas subscrições do Azure. Pode filtrar esta lista com base no nome, subscrição, grupo de recursos, localização, e assim por diante.

   ![Ver aplicativos lógicos associados a subscrições](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Selecione a sua aplicação lógica e, em seguida, selecione **visão geral**.

1. No menu da sua aplicação lógica, selecione **Visão Geral**. Na secção **Resumo,** em **Avaliação,** selecione **Ver histórico**do gatilho .

   ![Veja o histórico do gatilho para a sua aplicação lógica](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   O painel de história do gatilho mostra todas as tentativas de gatilho que a sua aplicação lógica fez. Cada vez que o gatilho dispara para um item ou evento, o motor Logic Apps cria uma instância lógica separada que executa o fluxo de trabalho. Por padrão, cada instância corre paralelamente para que nenhum fluxo de trabalho tenha de esperar antes de iniciar uma corrida. Assim, se a sua aplicação lógica disparar em vários itens ao mesmo tempo, aparece um gatilho com a mesma data e hora para cada item.

   ![Múltiplas tentativas de gatilho para diferentes itens](./media/monitor-logic-apps/logic-app-trigger-history.png)

   Aqui estão os possíveis estatutos para uma tentativa de gatilho:

   | Estado | Descrição |
   |--------|-------------|
   | **Falhou** | Ocorreu um erro. Para rever quaisquer mensagens de erro geradas para um gatilho falhado, selecione a tentativa de disparo e escolha **saídas**. Por exemplo, pode encontrar inputs que não são válidos. |
   | **Ignorado** | O gatilho verificou o ponto final, mas não encontrou dados. |
   | **Bem-sucedido** | O gatilho verificou o ponto final e encontrou dados disponíveis. Normalmente, um estado de "Despedido" também aparece ao lado deste estado. Se não, a definição do `SplitOn` gatilho pode ter uma condição ou comando que não foi cumprido. <p>Este estado pode aplicar-se a um gatilho manual, gatilho de recorrência ou gatilho de sondagens. Um gatilho pode funcionar com sucesso, mas a corrida em si ainda pode falhar quando as ações geram erros não manipulados. |
   |||

   > [!TIP]
   > Pode voltar a verificar o gatilho sem esperar pela próxima recorrência. Na barra de ferramentas de visão geral, selecione **'Executar gatilho**' e selecione o gatilho, que obriga a uma verificação. Ou, selecione **Executar** em Logic Apps Designer barra de ferramentas.

1. Para visualizar informações sobre uma tentativa específica de gatilho, no painel do gatilho, selecione o evento do gatilho. Se a lista mostrar muitas tentativas de disparo, e não encontrar a entrada que deseja, tente filtrar a lista. Se não encontrar os dados que espera, tente selecionar **Refresh** na barra de ferramentas.

   ![Ver tentativa específica de gatilho](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   Agora pode rever informações sobre o evento de gatilho selecionado, por exemplo:

   ![Ver informações específicas sobre o gatilho](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>Configurar alertas de monitorização

Para obter alertas com base em métricas específicas ou limiares ultrapassados para a sua aplicação lógica, instale [alertas no Monitor Azure](../azure-monitor/platform/alerts-overview.md). Conheça [as métricas em Azure.](../monitoring-and-diagnostics/monitoring-overview-metrics.md) Para configurar alertas sem utilizar o [Monitor Azure,](../log-analytics/log-analytics-overview.md)siga estes passos.

1. No menu de aplicações lógicas, sob **monitorização,** selecione **Alertas** > **Nova regra de alerta**.

   ![Adicione um alerta para a sua aplicação lógica](./media/monitor-logic-apps/add-new-alert-rule.png)

1. No painel de **regras Create,** no âmbito **do Recurso,** selecione a sua aplicação lógica, se ainda não for selecionada. Em **Condições,** **selecione Adicionar** para que possa definir a condição que desencadeia o alerta.

   ![Adicione uma condição para a regra](./media/monitor-logic-apps/add-condition-for-rule.png)

1. No painel de lógica de **sinal Configure,** encontre e selecione o sinal para o qual pretende obter um alerta. Pode utilizar a caixa de pesquisa ou ordenar os sinais alfabeticamente, selecione o cabeçalho da coluna **de nome signal.**

   Por exemplo, se quiser enviar um alerta quando um gatilho falha, siga estes passos:

   1. Na coluna **de nome sinal,** encontre e selecione o sinal falhado dos **gatilhos.**

      ![Selecione sinal para criar alerta](./media/monitor-logic-apps/find-and-select-signal.png)

   1. No painel de informações que se abre para o sinal selecionado, sob **a lógica de Alerta,** configurar a sua condição, por exemplo:

   1. Para **o operador,** selecione **Maior ou igual a**.

   1. Para **o tipo de agregação,** selecione **Contar**.

   1. Para o valor `1` **limiar,** insira .

   1. Sob **pré-visualização**da Condição, confirme se a sua condição parece correta.

   1. Em **avaliação com base em**, configurar o intervalo e a frequência para executar a regra de alerta. Para a **agregação da granularidade (Período)**, selecione o período para agrupar os dados. Para a **frequência de avaliação,** selecione quantas vezes pretende verificar a condição.

   1. Quando estiver pronto, selecione **Done**.

   Aqui está a condição final:

   ![Configurar condição para alerta](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   A página **de regras Create** mostra agora a condição que criou e o custo para executar esse alerta.

   ![Novo alerta na página "Criar regra"](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. Especifique um nome, descrição opcional e nível de gravidade para o seu alerta. Ou deixe a regra Enable sobre a definição de **criação** ligada, ou desligue até estar pronto para ativar a regra.

1. Quando terminar, selecione **Criar regra**de alerta .

> [!TIP]
> Para executar uma aplicação lógica a partir de um alerta, pode incluir o gatilho de [pedido](../connectors/connectors-native-reqres.md) no seu fluxo de trabalho, que permite executar tarefas como estes exemplos:
> 
> * [Posto para Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Enviar um SMS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Adicione uma mensagem a uma fila](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>Passos seguintes

* [Monitorize aplicações lógicas utilizando o Monitor Azure](../logic-apps/monitor-logic-apps-log-analytics.md)