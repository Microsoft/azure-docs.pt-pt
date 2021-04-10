---
title: Monitorizar estado, ver histórico e configurar alertas
description: Resolução de aplicativos de lógica de resolução de problemas, verificando o estado de execução, revendo o histórico do gatilho e permitindo alertas em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 3c3d1930234c178a56227830ef0702450ddf4a8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580664"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>Monitorizar o estado de execução, rever o histórico de acionadores e configurar alertas do Azure Logic Apps

Depois de [criar e executar uma aplicação lógica,](../logic-apps/quickstart-create-first-logic-app-workflow.md)pode verificar o estado de execução da aplicação lógica, executar o [histórico,](#review-runs-history) [desencadear a história](#review-trigger-history)e o desempenho. Para obter notificações sobre falhas ou outros possíveis [problemas,](#add-azure-alerts)crie alertas . Por exemplo, pode criar um alerta que detete "quando mais de cinco corridas falham numa hora".

Para monitorização de eventos em tempo real e depuração mais rica, confiem de diagnósticos para a sua aplicação lógica utilizando [registos do Azure Monitor](../azure-monitor/overview.md). Este serviço Azure ajuda-o a monitorizar os ambientes da nuvem e do local para que possa manter mais facilmente a sua disponibilidade e desempenho. Você pode então encontrar e ver eventos, tais como eventos de desencadeamento, eventos de execução e eventos de ação. Ao armazenar estas informações nos [registos do Azure Monitor,](../azure-monitor/logs/data-platform-logs.md)pode criar [consultas de registo](../azure-monitor/logs/log-query-overview.md) que o ajudam a encontrar e analisar estas informações. Também pode utilizar estes dados de diagnóstico com outros serviços Azure, como o Azure Storage e o Azure Event Hubs. Para obter mais informações, consulte [as aplicações lógicas do Monitor utilizando o Azure Monitor](../logic-apps/monitor-logic-apps-log-analytics.md).

> [!NOTE]
> Se as suas aplicações lógicas funcionarem num [ambiente de serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) criado para utilizar um ponto final de acesso [interno,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)pode ver e aceder a entradas e saídas a partir do histórico da aplicação lógica *apenas a partir de dentro da sua rede virtual.* Certifique-se de que tem conectividade de rede entre os pontos finais privados e o computador de onde pretende aceder ao histórico. Por exemplo, o seu computador cliente pode existir dentro da rede virtual do ISE ou dentro de uma rede virtual que está ligada à rede virtual do ISE, por exemplo, através de um espreitamento ou de uma rede privada virtual. Para mais informações, consulte [o acesso ao ponto final do ISE.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access) 

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>Rever o histórico de execuções

Cada vez que o gatilho dispara para um item ou evento, o motor De Aplicações Lógicas cria e executa uma instância de fluxo de trabalho separada para cada item ou evento. Por predefinição, cada caso de fluxo de trabalho funciona em paralelo para que nenhum fluxo de trabalho tenha de esperar antes de iniciar uma execução. Pode rever o que aconteceu durante essa execução, incluindo o estado de cada passo no fluxo de trabalho, além das entradas e saídas para cada passo.

1. No [portal Azure,](https://portal.azure.com)encontre e abra a sua aplicação lógica no Logic App Designer.

   Para encontrar a sua aplicação lógica , na caixa de pesquisa principal do Azure, insira e, em `logic apps` seguida, selecione **Logic Apps**.

   ![Localizar e selecionar o serviço "Aplicações lógicas"](./media/monitor-logic-apps/find-your-logic-app.png)

   O portal Azure mostra todas as aplicações lógicas associadas às suas subscrições Azure. Pode filtrar esta lista com base no nome, subscrição, grupo de recursos, localização e assim por diante.

   ![Ver aplicativos lógicos associados a subscrições](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Selecione a sua aplicação lógica e, em seguida, selecione **Overview**.

   No painel de visão geral, na **história de Runs**, todos os casos passados, atuais e quaisquer corridas de espera para a sua aplicação lógica aparecem. Se a lista mostrar muitas corridas e não conseguir encontrar a entrada que deseja, tente filtrar a lista.

   > [!TIP]
   > Se o estado de execução não aparecer, tente refrescar a página geral selecionando **Refresh**. Nenhuma corrida acontece para um gatilho que é ignorado devido a critérios não atendidos ou não encontrar dados.

   ![Visão geral, executa a história e outras informações lógicas de aplicativos](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   Aqui estão os possíveis estados de execução:

   | Estado de execução | Description |
   |------------|-------------|
   | **Abortada** | A execução parou ou não terminou devido a problemas externos, por exemplo, uma falha do sistema ou subscrição do Azure caducado. |
   | **Cancelado** | A corrida foi desencadeada e iniciada, mas recebeu um pedido de cancelamento. |
   | **Com falhas** | Pelo menos uma ação na corrida falhou. Não foram criadas ações subsequentes no fluxo de trabalho para lidar com a falha. |
   | **Em Execução** | A corrida foi desencadeada e está em curso, mas este estatuto também pode aparecer para uma corrida que é acelerada devido a [limites](logic-apps-limits-and-config.md) de ação ou ao [atual plano de preços.](https://azure.microsoft.com/pricing/details/logic-apps/) <p><p>**Dica**: Se configurar [o registo de diagnósticos,](monitor-logic-apps-log-analytics.md)pode obter informações sobre quaisquer eventos de aceleração que ocorram. |
   | **Com êxito** | A corrida conseguiu. Se alguma ação falhou, uma ação subsequente no fluxo de trabalho tratou dessa falha. |
   | **Esgotado** | O tempo de execução foi esgotado porque a duração atual excedeu o limite de duração de execução, que é controlado pela [ **retenção**](logic-apps-limits-and-config.md#run-duration-retention-limits)do histórico run em dias de fixação . A duração de uma corrida é calculada utilizando o tempo de início da execução e o limite de duração da execução nessa hora de início. <p><p>**Nota:** Se a duração da execução também exceder o limite atual de retenção do histórico de *execução*, que também é controlado pela [ **retenção**](logic-apps-limits-and-config.md#run-duration-retention-limits)do histórico run em dias de fixação , a execução é limpa do histórico de execuções por um trabalho de limpeza diário. Quer os tempos de funcionação se esgotam ou completem, o período de retenção é sempre calculado utilizando o tempo de início da execução e o limite de retenção *atual.* Então, se reduzir o limite de duração para uma corrida de voo, o tempo de execução se esgota. No entanto, a execução permanece ou é retirada do histórico de corridas com base no facto de a duração da corrida ter excedido o limite de retenção. |
   | **Em espera** | A execução não começou ou está interrompida, por exemplo, devido a uma instância de fluxo de trabalho anterior que ainda está em funcionamento. |
   |||

1. Para rever os passos e outras informações para uma execução específica, no **histórico de Runs,** selecione que executar.

   ![Selecione uma corrida específica para rever](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   O painel **de execução da aplicação Logic** mostra cada passo na corrida selecionada, o estado de execução de cada passo e o tempo necessário para cada passo correr, por exemplo:

   ![Cada ação na corrida específica](./media/monitor-logic-apps/logic-app-run-pane.png)

   Para ver estas informações no formulário de lista, na barra de ferramentas **executada por aplicações Logic,** selecione **'Detalhes de execução'.**

   ![Na barra de ferramentas, selecione "Executar detalhes"](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   A vista 'Detalhes de Execução' mostra cada passo, o seu estado e outras informações.

   ![Reveja detalhes sobre cada passo na corrida](./media/monitor-logic-apps/review-logic-app-run-details.png)

   Por exemplo, pode obter a propriedade **de ID de correlação da execução,** que poderá necessitar quando utilizar a [API REST para aplicações lógicas.](/rest/api/logic)

1. Para obter mais informações sobre um passo específico, selecione qualquer uma das opções:

   * No **painel de execução** da aplicação Logic selecione o passo para que a forma se expanda. Agora pode ver informações como entradas, saídas e quaisquer erros que ocorreram nesse passo, por exemplo:

     ![Na lógica app executar painel, ver passo falhado](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * No painel de detalhes da **aplicação Logic,** selecione o passo que deseja.

     ![No painel de detalhes de execução, ver passo falhado](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     Agora pode ver informações como entradas e saídas para esse passo, por exemplo:

   > [!NOTE]
   > Todos os detalhes e eventos de tempo de execução são encriptados dentro do serviço De aplicações lógicas. Só são desencriptados quando um utilizador solicita visualizar esses dados. Pode [ocultar entradas e saídas no histórico de execução](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate) ou controlar o acesso do utilizador a estas informações utilizando [o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md).

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>Rever o histórico de acionadores

Cada aplicação lógica começa com um gatilho. O histórico do gatilho lista todas as tentativas de gatilho que a sua aplicação lógica fez e informações sobre as entradas e saídas para cada tentativa de gatilho.

1. No [portal Azure,](https://portal.azure.com)encontre e abra a sua aplicação lógica no Logic App Designer.

   Para encontrar a sua aplicação lógica , na caixa de pesquisa principal do Azure, insira e, em `logic apps` seguida, selecione **Logic Apps**.

   ![Localizar e selecionar o serviço "Aplicações lógicas"](./media/monitor-logic-apps/find-your-logic-app.png)

   O portal Azure mostra todas as aplicações lógicas associadas às suas subscrições Azure. Pode filtrar esta lista com base no nome, subscrição, grupo de recursos, localização e assim por diante.

   ![Ver aplicativos lógicos associados a subscrições](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Selecione a sua aplicação lógica e, em seguida, selecione **Overview**.

1. No menu da sua aplicação lógica, selecione **Overview**. Na secção **Resumo,** em **Avaliação,** selecione **Ver histórico do gatilho**.

   ![Veja o histórico do gatilho para a sua aplicação lógica](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   O painel de histórico do gatilho mostra todas as tentativas de gatilho que a sua aplicação lógica fez. Cada vez que o gatilho dispara para um item ou evento, o motor De Aplicações Lógicas cria uma instância de aplicação lógica separada que executa o fluxo de trabalho. Por predefinição, cada instância corre em paralelo para que nenhum fluxo de trabalho tenha de esperar antes de iniciar uma corrida. Assim, se a sua aplicação lógica ativar vários itens ao mesmo tempo, aparece uma entrada de gatilho com a mesma data e hora para cada item.

   ![Múltiplas tentativas de gatilho para diferentes itens](./media/monitor-logic-apps/logic-app-trigger-history.png)

   Aqui estão os possíveis estados de tentativa de gatilho:

   | Estado do acionador | Description |
   |----------------|-------------|
   | **Com falhas** | Ocorreu um erro. Para rever quaisquer mensagens de erro geradas por um gatilho falhado, selecione a tentativa de gatilho e escolha **Saídas**. Por exemplo, pode encontrar entradas que não são válidas. |
   | **Ignorado** | O gatilho verificou o ponto final, mas não encontrou dados que satisfa por estes acordos não satisfaem os critérios especificados. |
   | **Com êxito** | O gatilho verificou o ponto final e encontrou dados disponíveis. Normalmente, um estado **de despedimento** também aparece ao lado deste estatuto. Se não, a definição do gatilho pode ter uma condição ou `SplitOn` comando que não foi cumprido. <p><p>Este estado pode aplicar-se a um gatilho manual, ao gatilho de recorrência ou ao gatilho das sondagens. Um gatilho pode ser executado com sucesso, mas a execução em si pode ainda falhar quando as ações geram erros não manipulados. |
   |||

   > [!TIP]
   > Pode verificar novamente o gatilho sem esperar pela próxima recorrência. Na barra de ferramentas de visão geral, selecione executar o **gatilho** e selecione o gatilho, que força uma verificação. Ou, selecione **Executar** na barra de ferramentas do Design de Aplicações Lógicas.

1. Para visualizar informações sobre uma tentativa específica de gatilho, no painel de gatilho, selecione o evento do gatilho. Se a lista mostrar muitas tentativas de gatilho, e não conseguir encontrar a entrada que deseja, tente filtrar a lista. Se não encontrar os dados que espera, tente selecionar **Refresh** na barra de ferramentas.

   ![Ver tentativa de gatilho específica](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   Pode agora rever informações sobre o evento de gatilho selecionado, por exemplo:

   ![Ver informações específicas do gatilho](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>Configurar alertas de monitorização

Para obter alertas baseados em métricas específicas ou limiares excededos para a sua aplicação lógica, crie [alertas no Azure Monitor](../azure-monitor/alerts/alerts-overview.md). Saiba mais [sobre as métricas em Azure.](../azure-monitor/data-platform.md) Para configurar alertas sem utilizar [o Azure Monitor,](../azure-monitor/logs/log-query-overview.md)siga estes passos.

1. No menu de aplicativos logic, em **Monitorização,** selecione **Alertas**  >  **Nova regra de alerta**.

   ![Adicione um alerta para a sua aplicação lógica](./media/monitor-logic-apps/add-new-alert-rule.png)

1. No painel **de regras Create,** em **Recurso,** selecione a sua aplicação lógica, se ainda não selecionada. Em **Condições**, **selecione Adicione** para que possa definir a condição que desencadeia o alerta.

   ![Adicione uma condição para a regra](./media/monitor-logic-apps/add-condition-for-rule.png)

1. No painel de lógica de **sinal de configuração,** encontre e selecione o sinal para o qual deseja obter um alerta. Pode utilizar a caixa de pesquisa ou separar os sinais alfabeticamente, selecione o cabeçalho da coluna **do nome Signal.**

   Por exemplo, se quiser enviar um alerta quando um gatilho falhar, siga estes passos:

   1. Na coluna **de nome sinal,** encontre e selecione o sinal **de falha de gatilhos.**

      ![Selecione sinal para criar alerta](./media/monitor-logic-apps/find-and-select-signal.png)

   1. No painel de informações que abre para o sinal selecionado, em **lógica de Alerta,** configurar a sua condição, por exemplo:

   1. Para **o operador,** selecione **Maior ou igual a**.

   1. Para **o tipo de agregação,** selecione **Count**.

   1. Para **o valor limiar,** insira `1` .

   1. Em **Pré-visualização da Condição,** confirme que a sua condição parece correta.

   1. Em **Avaliação com base em**, configurar o intervalo e a frequência para executar a regra de alerta. Para **a granularidade agregada (Período)**, selecione o período de agrupamento dos dados. Para **a frequência de avaliação,** selecione com que frequência pretende verificar a condição.

   1. Quando estiver pronto, selecione **'Fazer'.**

   Aqui está a condição final:

   ![Configurar condições para alerta](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   A página **de regras Create** mostra agora a condição que criou e o custo para executar esse alerta.

   ![Novo alerta na página "Criar regra"](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. Especifique um nome, descrição opcional e nível de gravidade para o seu alerta. Ou deixe a regra Enable após a **definição de criação** ligada ou desligue-a até estar pronto para ativar a regra.

1. Quando terminar, selecione **Criar a regra de alerta**.

> [!TIP]
> Para executar uma aplicação lógica a partir de um alerta, pode incluir o gatilho de [pedido](../connectors/connectors-native-reqres.md) no seu fluxo de trabalho, que lhe permite executar tarefas como estes exemplos:
> 
> * [Post to Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Enviar um SMS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Adicione uma mensagem a uma fila](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>Passos seguintes

* [Monitorize aplicações lógicas utilizando o Azure Monitor](../logic-apps/monitor-logic-apps-log-analytics.md)
