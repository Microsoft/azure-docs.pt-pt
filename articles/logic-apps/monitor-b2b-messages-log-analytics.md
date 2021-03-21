---
title: Monitorar mensagens B2B utilizando o Monitor Azure
description: Resolução de problemas as mensagens AS2, X12 e EDIFACT, criando registos do Azure Monitor e recolhendo dados de diagnóstico para apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 765c15897bd5d435503d3bef07e76a93b148971c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "100596854"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>Configurar os registos do Azure Monitor e recolher dados de diagnóstico para mensagens B2B no Azure Logic Apps

Depois de configurar a comunicação B2B entre parceiros comerciais na sua conta de integração, esses parceiros podem trocar mensagens utilizando protocolos como AS2, X12 e EDIFACT. Para verificar se esta comunicação funciona da forma que espera, pode configurar [registos do Azure Monitor](../azure-monitor/logs/data-platform-logs.md) para a sua conta de integração. [O Azure Monitor](../azure-monitor/overview.md) ajuda-o a monitorizar os ambientes da nuvem e do local para que possa manter mais facilmente a sua disponibilidade e desempenho. Ao utilizar registos do Azure Monitor, pode gravar e armazenar dados sobre dados e eventos de tempo de execução, tais como eventos de desencadeamento, eventos de execução e eventos de ação num [espaço de trabalho do Log Analytics.](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) Para mensagens, o registo também recolhe informações como:

* Contagem de mensagens e estado
* Estatuto de agradecimento
* Correlações entre mensagens e reconhecimentos
* Descrições detalhadas de erros para falhas

O Azure Monitor permite criar [consultas de registo](../azure-monitor/logs/log-query-overview.md) para o ajudar a encontrar e rever estas informações. Também pode [utilizar estes dados de diagnóstico com outros serviços Azure](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data), como o Azure Storage e o Azure Event Hubs.

Para configurar o registo da sua conta de integração, [instale a solução Logic Apps B2B](#install-b2b-solution) no portal Azure. Esta solução fornece informações agregadas para eventos de mensagens B2B. Em seguida, para permitir o registo e a criação de consultas para esta informação, crie [registos do Azure Monitor](#set-up-resource-logs).

Este artigo mostra como ativar o registo do Azure Monitor para a sua conta de integração.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Log Analytics. Se não tiver um espaço de trabalho log analytics, aprenda [a criar um espaço de trabalho Log Analytics](../azure-monitor/logs/quick-create-workspace.md).

* Uma aplicação lógica que foi criada com o registo do Azure Monitor e envia essa informação para um espaço de trabalho do Log Analytics. Saiba [como configurar os registos do Azure Monitor para a sua aplicação lógica.](../logic-apps/monitor-logic-apps.md)

* Uma conta de integração que está ligada à sua aplicação lógica. Saiba [como ligar a sua conta de integração à sua aplicação lógica.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>Instalar a solução B2B de Apps Lógicas

Antes que os registos do Azure Monitor possam rastrear as mensagens B2B para a sua aplicação lógica, adicione a solução **Logic Apps B2B** ao seu espaço de trabalho Log Analytics.

1. Na caixa de pesquisa do [portal Azure,](https://portal.azure.com)insira `log analytics workspaces` e, em seguida, selecione **log analytics espaços de trabalho**.

   ![Selecione "Log Analytics workspaces"](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. Nos **espaços de trabalho do Log Analytics,** selecione o seu espaço de trabalho.

   ![Selecione o seu espaço de trabalho Log Analytics](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. No painel de visão geral, em **Iniciar com**  >  **soluções de monitorização** de configuração log Analytics, selecione **Ver soluções**.

   ![No painel de visão geral, selecione "Ver soluções"](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. No painel de visão geral, **selecione Adicionar**.

   ![No painel geral, adicione nova solução](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. Depois de o **Marketplace** abrir, na caixa de pesquisa, insira `logic apps b2b` e selecione **As Aplicações Lógicas B2B**.

   ![A partir do Marketplace, selecione "Logic Apps Management"](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. No painel de descrição da solução, **selecione Criar**.

   ![Selecione "Criar" para adicionar solução "Logic Apps B2B"](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. Reveja e confirme o espaço de trabalho Log Analytics onde pretende instalar a solução e selecione **Criar** novamente.

   ![Selecione "Criar" para "Logic Apps B2B"](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   Depois de o Azure implementar a solução para o grupo de recursos Azure que contém o seu espaço de trabalho Log Analytics, a solução aparece no painel de resumo do seu espaço de trabalho. Quando as mensagens B2B são processadas, a contagem de mensagens neste painel é atualizada.

   ![Painel de resumo do espaço de trabalho](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Configurar registos do Monitor Azure

Pode ativar o registo do Azure Monitor diretamente a partir da sua conta de integração.

1. No [portal Azure,](https://portal.azure.com)encontre e selecione a sua conta de integração.

   ![Encontre e selecione a sua conta de integração](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. No menu da sua conta de integração, em **Monitorização,** selecione **Definições de Diagnóstico**. Selecione **Adicionar definição de diagnóstico**.

   ![Em "Monitorização", selecione "Definições de diagnóstico"](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. Para criar a definição, siga estes passos:

   1. Forneça um nome para a definição.

   1. Selecione **Enviar para Registar Análises**.

   1. Para **Subscrição**, selecione a subscrição Azure que está associada ao seu espaço de trabalho Log Analytics.

   1. Para **Log Analytics Workspace,** selecione o espaço de trabalho que pretende utilizar.

   1. No **registo**, selecione a categoria **IntegrationAccountTrackingEvents,** que especifica a categoria de evento que pretende gravar.

   1. Quando tiver terminado, selecione **Guardar**.

   Por exemplo: 

   ![Configurar registos do Azure Monitor para recolher dados de diagnóstico](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>Ver estado da mensagem

Após a execução da sua aplicação lógica, pode visualizar o estado e os dados sobre essas mensagens no seu espaço de trabalho Log Analytics.

1. Na caixa de pesquisa [do portal Azure,](https://portal.azure.com) encontre e abra o seu espaço de trabalho Log Analytics.

1. No menu do seu espaço de trabalho, selecione **Workspace Resumo**  >  **Logic Apps B2B**.

   ![Painel de resumo do espaço de trabalho](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > Se o azulejo das Aplicações Lógicas B2B não apresentar imediatamente resultados após uma execução, tente selecionar **Refresh** ou aguarde um curto período de tempo antes de tentar novamente.

   Por padrão, o azulejo **de Aplicações Lógicas B2B** mostra dados baseados num único dia. Para alterar o âmbito de dados para um intervalo diferente, selecione o controlo de âmbito no topo da página:

   ![Intervalo de alteração](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. Depois de aparecer o painel de estado da mensagem, pode ver mais detalhes para um tipo de mensagem específico, que mostra dados baseados num único dia. Selecione o azulejo para **AS2,** **X12** ou **EDIFACT**.

   ![Ver statuses para mensagens](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

   Aparece uma lista de mensagens para o seu azulejo escolhido. Por exemplo, aqui está o que uma lista de mensagens AS2 pode parecer:

   ![Estados e detalhes para mensagens AS2](./media/monitor-b2b-messages-log-analytics/as2-message-results-list.png)

   Para saber mais sobre as propriedades de cada tipo de mensagem, consulte estas descrições da propriedade da mensagem:

   * [Propriedades de mensagens AS2](#as2-message-properties)
   * [Propriedades de mensagens X12](#x12-message-properties)
   * [Propriedades de mensagens EDIFACT](#EDIFACT-message-properties)

<!--
1. To view or export the inputs and outputs for specific messages, select those messages, and select **Download**. When you're prompted, save the .zip file to your local computer, and then extract that file.

   The extracted folder includes a folder for each selected message. If you set up acknowledgements, the message folder also includes files with acknowledgement details. Each message folder has at least these files:
   
   * Human-readable files with the input payload and output payload details
   * Encoded files with the inputs and outputs

   For each message type, you can find the folder and file name formats here:

   * [AS2 folder and file name formats](#as2-folder-file-names)
   * [X12 folder and file name formats](#x12-folder-file-names)
   * [EDIFACT folder and file name formats](#edifact-folder-file-names)

   ![Download message files](./media/monitor-b2b-messages-log-analytics/download-messages.png)

1. To view all actions that have the same run ID, on the **Log Search** page, select a message from the message list.

   You can sort these actions by column, or search for specific results.

   * To search results with prebuilt queries, select **Favorites**.

   * Learn [how to build queries by adding filters](../logic-apps/create-monitoring-tracking-queries.md). Or learn more about [how to find data with log searches in Azure Monitor logs](../azure-monitor/logs/log-query-overview.md).

   * To change query in the search box, update the query with the columns and values that you want to use as filters.
-->

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Descrições de propriedades e formatos de nome para mensagens AS2, X12 e EDIFACT

Para cada tipo de mensagem, aqui estão as descrições da propriedade e formatos de nome para ficheiros de mensagens descarregados.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Descrições de propriedade de mensagens AS2

Aqui estão as descrições da propriedade para cada mensagem AS2.

| Propriedade | Descrição |
|----------|-------------|
| **Remetente** | O parceiro convidado especificado em **Definições de Receção**, ou o parceiro anfitrião especificado em **Definições de Envio** para um acordo AS2 |
| **Recetor** | O parceiro anfitrião especificado em **Definições de Receção**, ou o parceiro convidado especificado em **Definições de Envio** para um acordo AS2 |
| **Aplicação Lógica** | A aplicação lógica onde as ações do AS2 são criadas |
| **Estado** | O estado da mensagem AS2 <br>Sucesso = Recebeu ou enviou uma mensagem AS2 válida. Não está configurado nenhum MDN. <br>Sucesso = Recebeu ou enviou uma mensagem AS2 válida. A MDN é configurada e recebida, ou a MDN é enviada. <br>Falhou = Recebeu uma mensagem AS2 inválida. Não está configurado nenhum MDN. <br>Pendente = Recebeu ou enviou uma mensagem AS2 válida. A MDN está configurada, e a MDN é esperada. |
| **Rio ACK** | O estado da mensagem MDN <br>Aceite = Recebido ou enviado um MDN positivo. <br>Pendente = Esperando para receber ou enviar um MDN. <br>Rejeitado = Recebido ou enviado um MDN negativo. <br>Não É Necessário = MDN não está estabelecido no acordo. |
| **Direção** | A direção da mensagem AS2 |
| **ID de rastreio** | O ID que correlaciona todos os gatilhos e ações numa aplicação lógica |
| **ID da Mensagem** | O ID de mensagem AS2 dos cabeçalhos de mensagens AS2 |
| **Tempotamp** | O momento em que a ação AS2 processou a mensagem |
|||

<!--
<a name="as2-folder-file-names"></a>

### AS2 name formats for downloaded message files

Here are the name formats for each downloaded AS2 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|||
-->

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Descrições da propriedade de mensagens X12

Aqui estão as descrições da propriedade para cada mensagem X12.

| Propriedade | Descrição |
|----------|-------------|
| **Remetente** | O parceiro convidado especificado em **Configurações de Receção**, ou o parceiro anfitrião especificado em **Definições de Envio** para um acordo X12 |
| **Recetor** | O parceiro anfitrião especificado em **'Receber Definições'** ou o parceiro convidado especificado em **Definições de Envio** para um acordo X12 |
| **Aplicação Lógica** | A aplicação lógica onde as ações X12 são criadas |
| **Estado** | O estado da mensagem X12 <br>Sucesso = Recebeu ou enviou uma mensagem X12 válida. Não está configurado nenhum ack funcional. <br>Sucesso = Recebeu ou enviou uma mensagem X12 válida. Ack funcional é configurada e recebida, ou é enviada uma ack funcional. <br>Falhado = Recebeu ou enviou uma mensagem X12 inválida. <br>Pendente = Recebeu ou enviou uma mensagem X12 válida. Ack funcional é configurado, e espera-se um ack funcional. |
| **Rio ACK** | Estado funcional do Ack (997) <br>Aceite = Recebido ou enviado um ack funcional positivo. <br>Rejeitado = Recebido ou enviado um ack funcional negativo. <br>Pendente = Esperando um ack funcional mas não recebido. <br>Pendente = Gerou um ack funcional mas não pode enviar para o parceiro. <br>Não É Necessário = Ack funcional não é configurado. |
| **Direção** | A direção da mensagem X12 |
| **ID de rastreio** | O ID que correlaciona todos os gatilhos e ações numa aplicação lógica |
| **Tipo Msg** | O tipo de mensagem EDI X12 |
| **ICN** | O Número de Controlo de Intercâmbio para a mensagem X12 |
| **TSCN** | O número de controlo do conjunto de transações para a mensagem X12 |
| **Tempotamp** | O tempo em que a ação X12 processou a mensagem |
|||

<!--
<a name="x12-folder-file-names"></a>

### X12 name formats for downloaded message files

Here are the name formats for each downloaded X12 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|||
-->

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Descrições de propriedade de mensagens EDIFACT

Aqui estão as descrições da propriedade para cada mensagem EDIFACT.

| Propriedade | Descrição |
|----------|-------------|
| **Remetente** | O parceiro convidado especificado em **Definições de Receção,** ou o parceiro anfitrião especificado em **Definições de Envio** para um acordo EDIFACT |
| **Recetor** | O parceiro anfitrião especificado em **Definições de Receção,** ou o parceiro convidado especificado em **Definições de Envio** para um acordo EDIFACT |
| **Aplicação Lógica** | A aplicação lógica onde as ações do EDIFACT são criadas |
| **Estado** | O estado da mensagem EDIFACT <br>Sucesso = Recebeu ou enviou uma mensagem EDIFACT válida. Não está configurado nenhum ack funcional. <br>Sucesso = Recebeu ou enviou uma mensagem EDIFACT válida. Ack funcional é configurada e recebida, ou é enviada uma ack funcional. <br>Falhado = Recebido ou enviado uma mensagem EDIFACT inválida <br>Pendente = Recebeu ou enviou uma mensagem EDIFACT válida. Ack funcional é configurado, e espera-se um ack funcional. |
| **Rio ACK** | Estado funcional do Ack (CONTRL) <br>Aceite = Recebido ou enviado um ack funcional positivo. <br>Rejeitado = Recebido ou enviado um ack funcional negativo. <br>Pendente = Esperando um ack funcional mas não recebido. <br>Pendente = Gerou um ack funcional mas não pode enviar para o parceiro. <br>Não É Necessário = Ack funcional não está configurado. |
| **Direção** | A direção da mensagem EDIFACT |
| **ID de rastreio** | O ID que correlaciona todos os gatilhos e ações numa aplicação lógica |
| **Tipo Msg** | O tipo de mensagem EDIFACT |
| **ICN** | O Número de Controlo Intercambial da mensagem EDIFACT |
| **TSCN** | O número de controlo do conjunto de transações para a mensagem EDIFACT |
| **Tempotamp** | O tempo em que a ação EDIFACT processou a mensagem |
|||

<!--
<a name="edifact-folder-file-names"></a>

### EDIFACT name formats for downloaded message files

Here are the name formats for each downloaded EDIFACT message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|||
-->

## <a name="next-steps"></a>Passos seguintes

* [Criar consultas de monitorização e rastreio](../logic-apps/create-monitoring-tracking-queries.md)
