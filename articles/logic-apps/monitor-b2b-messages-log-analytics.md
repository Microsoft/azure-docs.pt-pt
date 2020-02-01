---
title: Monitorize as mensagens B2B utilizando o Monitor Azure
description: Mensagens AS2, X12 e EDIFACT, através da criação de registos do Monitor Do Azure e da recolha de dados de diagnóstico para aplicações lógicas do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: e9ba5a516293eb72a715dc9d0df7db4d5a4ea3c5
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907984"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>Configurar registos do Monitor Azure e recolher dados de diagnóstico para mensagens B2B em Aplicações Lógicas Azure

Depois de configurar a comunicação B2B entre parceiros comerciais na sua conta de integração, esses parceiros podem trocar mensagens utilizando protocolos como AS2, X12 e EDIFACT. Para verificar se esta comunicação funciona da forma que espera, pode configurar [registos do Monitor Azure](../azure-monitor/platform/data-platform-logs.md) para a sua conta de integração. [O Azure Monitor](../azure-monitor/overview.md) ajuda-o a monitorizar os ambientes de nuvem e no local para que possa manter mais facilmente a sua disponibilidade e desempenho. Ao utilizar registos do Monitor Azure, pode registar e armazenar dados sobre dados e eventos de tempo de execução, tais como eventos de desencadeamento, eventos de execução e eventos de ação num espaço de [trabalho do Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md). Para mensagens, o registo também recolhe informações como:

* Contagem de mensagens e estado
* Estatuto de reconhecimento
* Correlações entre mensagens e agradecimentos
* Descrições detalhadas de erros para falhas

O Azure Monitor permite-lhe criar consultas de [registo](../azure-monitor/log-query/log-query-overview.md) para o ajudar a encontrar e rever esta informação. Também pode utilizar estes dados de [diagnóstico com outros serviços Azure,](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data)como o Azure Storage e o Azure Event Hubs.

Para configurar o registo da sua conta de integração, [instale a solução Logic Apps B2B](#install-b2b-solution) no portal Azure. Esta solução fornece informações agregadas para eventos de mensagens B2B. Em seguida, para permitir a exploração madeireira e criar consultas para estas informações, [crie registos do Monitor Azure](#set-up-resource-logs).

Este artigo mostra como permitir o registo do Azure Monitor para a sua conta de integração.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Log Analytics. Se não tiver um espaço de trabalho de Log Analytics, aprenda [a criar um espaço](../azure-monitor/learn/quick-create-workspace.md)de trabalho log Analytics .

* Uma aplicação lógica que é configurada com o registo do Monitor Azure e envia essa informação para um espaço de trabalho do Log Analytics. Aprenda [a configurar registos do Monitor Azure para a sua aplicação lógica](../logic-apps/monitor-logic-apps.md).

* Uma conta de integração que está ligada à sua aplicação lógica. Saiba como ligar a sua conta de [integração à sua aplicação lógica.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>Instalar aplicações lógicas B2B solução

Antes de os registos do Azure Monitor poderem rastrear as mensagens B2B para a sua aplicação lógica, adicione a solução **Logic Apps B2B** ao seu espaço de trabalho Log Analytics.

1. Na caixa de pesquisa do [portal Azure,](https://portal.azure.com)introduza `log analytics workspaces`e, em seguida, selecione espaços de **trabalho Log Analytics**.

   ![Selecione "Log Analytics workspaces"](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. Em espaços de **trabalho Log Analytics,** selecione o seu espaço de trabalho.

   ![Selecione seu espaço de trabalho Log Analytics](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. No painel overview, em **Iniciar-se com log analytics** > **Configurar soluções**de monitorização , selecione **soluções de visualização**.

   ![No painel de visão geral, selecione "Ver soluções"](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. No painel de visão geral, selecione **Adicionar**.

   ![No painel de visão geral, adicione nova solução](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. Depois do **Marketplace** abrir, na caixa de pesquisa, insira `logic apps b2b`e selecione **Logic Apps B2B**.

   ![Do Marketplace, selecione "Logic Apps Management"](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. No painel de descrição da solução, selecione **Criar**.

   ![Selecione "Create" para adicionar solução "Logic Apps B2B"](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. Reveja e confirme o espaço de trabalho do Log Analytics onde pretende instalar a solução e selecione **Criar** novamente.

   ![Selecione "Create" para "Logic Apps B2B"](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   Depois de o Azure implementar a solução para o grupo de recursos Azure que contém o seu espaço de trabalho Log Analytics, a solução aparece no painel de resumo do seu espaço de trabalho. Quando as mensagens B2B são processadas, a contagem de mensagens neste painel é atualizada.

   ![Painel de resumo do espaço de trabalho](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Configurar logs de Azure Monitor

Pode ativar o registo do Monitor Azure diretamente da sua conta de integração.

1. No [portal Azure,](https://portal.azure.com)encontre e selecione a sua conta de integração.

   ![Encontre e selecione a sua conta de integração](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. No menu da sua conta de integração, em **Monitorização,** selecione **definições**de diagnóstico . Selecione **Adicionar configuração de diagnóstico**.

   ![Em "Monitorização", selecione "Definições de Diagnóstico"](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. Para criar a definição, siga estes passos:

   1. Forneça um nome para a definição.

   1. Selecione **Enviar para log Analytics**.

   1. Para **subscrição,** selecione a subscrição Azure que esteja associada ao seu espaço de trabalho Log Analytics.

   1. Para **log analytics workspace,** selecione o espaço de trabalho que pretende utilizar.

   1. Em **registo,** selecione a categoria **IntegrationAccountTrackingEvents,** que especifica a categoria de evento que pretende gravar.

   1. Quando tiver terminado, selecione **Guardar**.

   Por exemplo: 

   ![Configurar registos do Monitor Azure para recolher dados de diagnóstico](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>Ver estado da mensagem

Após a sua aplicação lógica, pode visualizar o estado e os dados sobre essas mensagens no seu espaço de trabalho Log Analytics.

1. Na caixa de pesquisa do [portal Azure,](https://portal.azure.com) encontre e abra o seu espaço de trabalho Log Analytics.

1. No menu do seu espaço de trabalho, selecione resumo do **Workspace** > **Logic Apps B2B**.

   ![Painel de resumo do espaço de trabalho](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > Se o azulejo Logic Apps B2B não mostrar imediatamente os resultados após uma corrida, tente selecionar **Refresh** ou esperar por um curto período de tempo antes de tentar novamente.

   Por padrão, o azulejo **Logic Apps B2B** mostra dados baseados num único dia. Para alterar o âmbito de dados para um intervalo diferente, selecione o controlo de âmbito na parte superior da página:

   ![Alterar intervalo](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. Após a visualização do painel de dados do estado da mensagem, pode visualizar mais detalhes para um tipo específico de mensagem, que mostra dados baseados num único dia. Selecione o azulejo para **AS2,** **X12**, ou **EDIFACT**.

   ![Ver estados para mensagens](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

   Uma lista de mensagens aparece para o seu azulejo escolhido. Por exemplo, aqui está o que uma lista de mensagens AS2 pode parecer:

   ![Estados e detalhes para mensagens AS2](./media/monitor-b2b-messages-log-analytics/as2-message-results-list.png)

   Para saber mais sobre as propriedades para cada tipo de mensagem, consulte estas descrições da propriedade da mensagem:

   * [Propriedades de mensagem AS2](#as2-message-properties)
   * [Propriedades da mensagem X12](#x12-message-properties)
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

   * Learn [how to build queries by adding filters](../logic-apps/create-monitoring-tracking-queries.md). Or learn more about [how to find data with log searches in Azure Monitor logs](../log-analytics/log-analytics-log-searches.md).

   * To change query in the search box, update the query with the columns and values that you want to use as filters.
-->

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Descrições de propriedades e formatos de nome para mensagens AS2, X12 e EDIFACT

Para cada tipo de mensagem, aqui estão as descrições da propriedade e formatos de nome para ficheiros de mensagens descarregados.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Descrições da propriedade da mensagem AS2

Aqui estão as descrições da propriedade para cada mensagem AS2.

| Propriedade | Descrição |
|----------|-------------|
| **Remetente** | O parceiro convidado especificado em **Definições de Receção**, ou o parceiro anfitrião especificado em **Definições** de Envio para um acordo AS2 |
| **Recetor** | O parceiro anfitrião especificado nas Definições de **Receção,** ou o parceiro convidado especificado em **Definições** de Envio para um acordo AS2 |
| **Aplicação Lógica** | A aplicação lógica onde as ações AS2 são configuradas |
| **Estado** | O estado da mensagem AS2 <br>Sucesso = Recebido ou enviado uma mensagem AS2 válida. Não está preparada nenhuma MDN. <br>Sucesso = Recebido ou enviado uma mensagem AS2 válida. A MDN é configurada e recebida, ou a MDN é enviada. <br>Failed = Recebeu uma mensagem AS2 inválida. Não está preparada nenhuma MDN. <br>Pendente = Recebido ou enviado uma mensagem AS2 válida. A MDN está preparada e espera-se a MDN. |
| **ACK** | O estado da mensagem MDN <br>Aceite = Recebida ou enviada um MDN positivo. <br>Pendente = À espera de receber ou enviar um MDN. <br>Rejeitado = Recebido ou enviado um MDN negativo. <br>Não é necessário = MDN não está estabelecido no acordo. |
| **Direção** | A direção da mensagem AS2 |
| **ID de rastreio** | O ID que correlaciona todos os gatilhos e ações numa aplicação lógica |
| **ID da mensagem** | O ID de mensagem AS2 dos cabeçalhos de mensagem AS2 |
| **Carimbo de tempo** | O momento em que a ação AS2 processou a mensagem |
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

### <a name="x12-message-property-descriptions"></a>Descrições da propriedade da mensagem X12

Aqui estão as descrições da propriedade para cada mensagem X12.

| Propriedade | Descrição |
|----------|-------------|
| **Remetente** | O parceiro convidado especificado em **Definições de Receção**, ou o parceiro anfitrião especificado em **Definições** de Envio para um acordo X12 |
| **Recetor** | O parceiro anfitrião especificado nas Definições de **Receção,** ou o parceiro convidado especificado em **Definições** de Envio para um acordo X12 |
| **Aplicação Lógica** | A aplicação lógica onde as ações X12 são configuradas |
| **Estado** | O estado da mensagem X12 <br>Sucesso = Recebido ou enviado uma mensagem X12 válida. Não está preparado nenhum ack funcional. <br>Sucesso = Recebido ou enviado uma mensagem X12 válida. O ack funcional é configurado e recebido, ou um ack funcional é enviado. <br>Failed = Recebido ou enviado uma mensagem X12 inválida. <br>Pendente = Recebido ou enviado uma mensagem X12 válida. O ack funcional está configurado, e espera-se um ack funcional. |
| **ACK** | Estado funcional de Ack (997) <br>Aceite = Recebida ou enviada um ack funcional positivo. <br>Rejeitado = Recebido ou enviado um ack funcional negativo. <br>Pendente = Esperando um ack funcional mas não recebido. <br>Pendente = Gerou um ack funcional mas não pode enviar para o parceiro. <br>Não é necessário = O ack funcional não está configurado. |
| **Direção** | A direção da mensagem X12 |
| **ID de rastreio** | O ID que correlaciona todos os gatilhos e ações numa aplicação lógica |
| **Tipo Msg** | O tipo de mensagem EDI X12 |
| **ICN** | O Número de Controlo de Intercâmbio para a mensagem X12 |
| **TSCN** | O número de controlo de conjunto de transações para a mensagem X12 |
| **Carimbo de tempo** | O momento em que a ação X12 processou a mensagem |
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

### <a name="edifact-message-property-descriptions"></a>Descrições de propriedades de mensagens EDIFACT

Aqui estão as descrições da propriedade para cada mensagem EDIFACT.

| Propriedade | Descrição |
|----------|-------------|
| **Remetente** | O parceiro convidado especificado em **Definições de Receção**, ou o parceiro anfitrião especificado em **Definições de Envio** para um acordo EDIFACT |
| **Recetor** | O parceiro anfitrião especificado em **Definições de Receção**, ou o parceiro convidado especificado em **Definições** de Envio para um acordo EDIFACT |
| **Aplicação Lógica** | A aplicação lógica onde as ações edifact são criadas |
| **Estado** | O estado da mensagem EDIFACT <br>Sucesso = Recebido ou enviado uma mensagem EDIFACT válida. Não está preparado nenhum ack funcional. <br>Sucesso = Recebido ou enviado uma mensagem EDIFACT válida. O ack funcional é configurado e recebido, ou um ack funcional é enviado. <br>Falhado = Recebido ou enviado uma mensagem EDIFACT inválida <br>Pendente = Recebido ou enviado uma mensagem EDIFACT válida. O ack funcional está configurado, e espera-se um ack funcional. |
| **ACK** | Estado funcional de Ack (CONTRL) <br>Aceite = Recebida ou enviada um ack funcional positivo. <br>Rejeitado = Recebido ou enviado um ack funcional negativo. <br>Pendente = Esperando um ack funcional mas não recebido. <br>Pendente = Gerou um ack funcional mas não pode enviar para o parceiro. <br>Não é necessário = Ack funcional não está configurado. |
| **Direção** | A direção da mensagem EDIFACT |
| **ID de rastreio** | O ID que correlaciona todos os gatilhos e ações numa aplicação lógica |
| **Tipo Msg** | O tipo de mensagem EDIFACT |
| **ICN** | O Número de Controlo de Intercâmbio para a mensagem EDIFACT |
| **TSCN** | O número de controlo de conjunto de transações para a mensagem EDIFACT |
| **Carimbo de tempo** | O momento em que a ação EDIFACT processou a mensagem |
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