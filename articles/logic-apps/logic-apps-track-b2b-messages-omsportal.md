---
title: Controlar mensagens B2B com os registos do Azure Monitor
description: Track B2B comunicação para contas de integração e Aplicativos Azure Logic com Azure Log Analytics
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 6e66bdfcfe9e84c1095f03a41439b904c7cb96df
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773720"
---
# <a name="track-b2b-messages-with-azure-monitor-logs"></a>Controlar mensagens B2B com os registos do Azure Monitor

Depois de configurar a comunicação B2B entre parceiros comerciais na sua conta de integração, esses parceiros podem trocar mensagens com protocolos como AS2, X12 e EDIFACT. Para verificar se estas mensagens são processadas corretamente, pode rastrear estas mensagens com [registos do Monitor Azure](../log-analytics/log-analytics-overview.md). Por exemplo, pode utilizar estas capacidades de rastreio baseadas na Web para rastrear mensagens:

* Contagem de mensagens e estado
* Estatuto de reconhecimento
* Correlacionar mensagens com agradecimentos
* Descrições detalhadas de erros para falhas
* Capacidades de pesquisa

> [!NOTE]
> Esta página previamente descrevia passos para como executar estas tarefas com a Microsoft Operations Management Suite (OMS), que se vai reformar em janeiro de [2019,](../azure-monitor/platform/oms-portal-transition.md)substitui essas etapas por Azure Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma aplicação lógica que é configurada com registo de diagnósticos. Aprenda [a criar uma aplicação lógica](quickstart-create-first-logic-app-workflow.md) e como configurar o registo para essa [aplicação lógica.](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)

* Uma conta de integração que é criada com monitorização e exploração madeireira. Aprenda [a criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e como [configurar a monitorização e o registo dessa conta.](../logic-apps/logic-apps-monitor-b2b-message.md)

* Se ainda não o fez, [publique dados de diagnóstico nos registos do Monitor Do Azure](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

* Depois de cumprir os requisitos anteriores, também precisa de um espaço de trabalho log Analytics, que utiliza para rastrear a comunicação B2B através do Log Analytics. Se não tiver um espaço de trabalho de Log Analytics, aprenda [a criar um espaço](../azure-monitor/learn/quick-create-workspace.md)de trabalho log Analytics .

## <a name="install-logic-apps-b2b-solution"></a>Instalar aplicações lógicas B2B solução

Antes de poder ter registos do Monitor Do Azure, rastreia as mensagens B2B para a sua aplicação lógica, adicione a solução **Logic Apps B2B** aos registos do Monitor Azure. Saiba mais sobre a adição de [soluções aos registos do Monitor Azure](../azure-monitor/learn/quick-create-workspace.md).

1. No [portal do Azure](https://portal.azure.com), selecione **Todos os serviços**. Na caixa de pesquisa, encontre "log analytics", e selecione **Log Analytics**.

   ![Selecione Log Analytics](media/logic-apps-track-b2b-messages-omsportal/find-log-analytics.png)

1. Em **log Analytics**, localize e selecione seu espaço de trabalho do log Analytics. 

   ![Selecione log Analytics espaço de trabalho](media/logic-apps-track-b2b-messages-omsportal/select-log-analytics-workspace.png)

1. Em **Iniciar-se com log analytics** > Configurar soluções de **monitorização,** escolha **soluções View**.

   ![Escolha "Ver soluções"](media/logic-apps-track-b2b-messages-omsportal/log-analytics-workspace.png)

1. Na página 'Visão Geral', escolha **Adicionar**, que abre a lista de Soluções de **Gestão.** A partir dessa lista, selecione **Logic Apps B2B**. 

   ![Selecione lógica apps B2B solução](media/logic-apps-track-b2b-messages-omsportal/add-b2b-solution.png)

   Se não encontrar a solução, na parte inferior da lista, escolha **carregar mais** até que a solução apareça.

1. Escolha **Criar**, confirme o espaço de trabalho do Log Analytics onde pretende instalar a solução e, em seguida, **escolha** criar novamente.   

   ![Escolha "Criar" para Aplicações Lógicas B2B](media/logic-apps-track-b2b-messages-omsportal/create-b2b-solution.png)

   Se não quiser utilizar um espaço de trabalho existente, também pode criar um novo espaço de trabalho neste momento.

1. Quando terminar, volte para a página **geral** do seu espaço de trabalho. 

   A solução Logic Apps B2B aparece agora na página 'Visão Geral'. 
   Quando as mensagens B2B são processadas, a contagem de mensagens nesta página é atualizada.

<a name="message-status-details"></a>

## <a name="view-b2b-message-information"></a>Ver informações de mensagem B2B

Depois de as mensagens B2B serem processadas, pode visualizar o estado e os detalhes dessas mensagens no azulejo **Logic Apps B2B.**

1. Vá ao seu espaço de trabalho Logic Analytics e abra a página de visão geral. Escolha **aplicativos lógicos B2B**.

   ![Contagem de mensagens atualizada](media/logic-apps-track-b2b-messages-omsportal/b2b-overview-tile.png)

   > [!NOTE]
   > Por padrão, o azulejo **Logic Apps B2B** mostra dados baseados num único dia. Para alterar o âmbito de dados para um intervalo diferente, escolha o controlo de âmbito na parte superior da página:
   > 
   > ![Alterar intervalo](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)

1. Após a visualização do painel de dados do estado da mensagem, pode visualizar mais detalhes para um tipo específico de mensagem, que mostra dados baseados num único dia. Escolha o azulejo para **AS2,** **X12**ou **EDIFACT**.

   ![Ver estado da mensagem](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   Uma lista de mensagens aparece para o seu azulejo escolhido. 
   Para saber mais sobre as propriedades para cada tipo de mensagem, consulte estas descrições da propriedade da mensagem:

   * [Propriedades de mensagem AS2](#as2-message-properties)
   * [Propriedades da mensagem X12](#x12-message-properties)
   * [Propriedades de mensagens EDIFACT](#EDIFACT-message-properties)

   Por exemplo, aqui está o que uma lista de mensagens AS2 pode parecer:

   ![Ver mensagens AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Para visualizar ou exportar as inputs e saídas para mensagens específicas, selecione essas mensagens e escolha **o Download**. Quando for solicitado, guarde o ficheiro .zip para o seu computador local e, em seguida, extraio esse ficheiro. 

   A pasta extraída inclui uma pasta para cada mensagem selecionada. 
   Se configurar agradecimentos, a pasta de mensagens também inclui ficheiros com detalhes de reconhecimento. 
   Cada pasta de mensagem tem pelo menos estes ficheiros: 
   
   * Ficheiros legíveis pelo homem com os detalhes da carga útil e da carga de saída
   * Ficheiros codificados com as inputs e saídas

   Para cada tipo de mensagem, pode encontrar os formatos de nome da pasta e do ficheiro aqui:

   * [Formatos de pasta AS2 e nome de ficheiro](#as2-folder-file-names)
   * [Formatos de pasta X12 e nome de ficheiro](#x12-folder-file-names)
   * [Formatos de pasta EDIFACT e nome de ficheiro](#edifact-folder-file-names)

   ![Descarregue ficheiros de mensagens](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Para visualizar todas as ações que tenham o mesmo ID de execução, na página de Pesquisa de **Registo,** escolha uma mensagem na lista de mensagens.

   Pode ordenar estas ações por coluna ou procurar resultados específicos.

   ![Ações com o mesmo ID de execução](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Para pesquisar resultados com consultas pré-construídas, escolha **Favoritos**.

   * Aprenda [a construir consultas adicionando filtros](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   Ou saiba mais sobre [como encontrar dados com pesquisas de registo em registos do Monitor Azure](../log-analytics/log-analytics-log-searches.md).

   * Para alterar a consulta na caixa de pesquisa, atualize a consulta com as colunas e valores que pretende utilizar como filtros.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Descrições de propriedades e formatos de nome para mensagens AS2, X12 e EDIFACT

Para cada tipo de mensagem, aqui estão as descrições da propriedade e formatos de nome para ficheiros de mensagens descarregados.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Descrições da propriedade da mensagem AS2

Aqui estão as descrições da propriedade para cada mensagem AS2.

| Propriedade | Descrição |
| --- | --- |
| Remetente | O parceiro convidado especificado em **Definições de Receção**, ou o parceiro anfitrião especificado em **Definições** de Envio para um acordo AS2 |
| Recetor | O parceiro anfitrião especificado nas Definições de **Receção,** ou o parceiro convidado especificado em **Definições** de Envio para um acordo AS2 |
| Aplicação Lógica | A aplicação lógica onde as ações AS2 são configuradas |
| Estado | O estado da mensagem AS2 <br>Sucesso = Recebido ou enviado uma mensagem AS2 válida. Não está preparada nenhuma MDN. <br>Sucesso = Recebido ou enviado uma mensagem AS2 válida. A MDN é configurada e recebida, ou a MDN é enviada. <br>Failed = Recebeu uma mensagem AS2 inválida. Não está preparada nenhuma MDN. <br>Pendente = Recebido ou enviado uma mensagem AS2 válida. A MDN está preparada e espera-se a MDN. |
| Ack | O estado da mensagem MDN <br>Aceite = Recebida ou enviada um MDN positivo. <br>Pendente = À espera de receber ou enviar um MDN. <br>Rejeitado = Recebido ou enviado um MDN negativo. <br>Não é necessário = MDN não está estabelecido no acordo. |
| Direção | A direção da mensagem AS2 |
| ID de Correlação | O ID que correlaciona todos os gatilhos e ações numa aplicação lógica |
| ID da mensagem | O ID de mensagem AS2 dos cabeçalhos de mensagem AS2 |
| Carimbo de data/hora | O momento em que a ação AS2 processou a mensagem |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>Formatos de nome AS2 para ficheiros de mensagens descarregados

Aqui estão os formatos de nome para cada pasta de mensagens AS2 descarregada e ficheiros.

| Pasta ou ficheiro | Formato de nome |
| :------------- | :---------- |
| Pasta de mensagem | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Entrada, saída e, se configurado, ficheiros de reconhecimento | **Carga útil da entrada**: [remetente]\_[recetor]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Carga útil da saída:** [remetente]\_[recetor]\_AS2\_[correlation-ID]\_saída\_carga útil.txt </p></p>**Entradas**: [remetente]\_[recetor]\_AS2\_[correlation-ID]\_entradas.txt </p></p>**Saídas**: [remetente]\_[recetor]\_AS2\_[correlation-ID]\_saídas.txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Descrições da propriedade da mensagem X12

Aqui estão as descrições da propriedade para cada mensagem X12.

| Propriedade | Descrição |
| --- | --- |
| Remetente | O parceiro convidado especificado em **Definições de Receção**, ou o parceiro anfitrião especificado em **Definições** de Envio para um acordo X12 |
| Recetor | O parceiro anfitrião especificado nas Definições de **Receção,** ou o parceiro convidado especificado em **Definições** de Envio para um acordo X12 |
| Aplicação Lógica | A aplicação lógica onde as ações X12 são configuradas |
| Estado | O estado da mensagem X12 <br>Sucesso = Recebido ou enviado uma mensagem X12 válida. Não está preparado nenhum ack funcional. <br>Sucesso = Recebido ou enviado uma mensagem X12 válida. O ack funcional é configurado e recebido, ou um ack funcional é enviado. <br>Failed = Recebido ou enviado uma mensagem X12 inválida. <br>Pendente = Recebido ou enviado uma mensagem X12 válida. O ack funcional está configurado, e espera-se um ack funcional. |
| Ack | Estado funcional de Ack (997) <br>Aceite = Recebida ou enviada um ack funcional positivo. <br>Rejeitado = Recebido ou enviado um ack funcional negativo. <br>Pendente = Esperando um ack funcional mas não recebido. <br>Pendente = Gerou um ack funcional mas não pode enviar para o parceiro. <br>Não é necessário = O ack funcional não está configurado. |
| Direção | A direção da mensagem X12 |
| ID de Correlação | O ID que correlaciona todos os gatilhos e ações numa aplicação lógica |
| Tipo Msg | O tipo de mensagem EDI X12 |
| ICN | O Número de Controlo de Intercâmbio para a mensagem X12 |
| TSCN | O número de controlo de conjunto de transações para a mensagem X12 |
| Carimbo de data/hora | O momento em que a ação X12 processou a mensagem |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>Formatos de nome X12 para ficheiros de mensagens descarregados

Aqui estão os formatos de nome para cada pasta de mensagens X12 descarregada e ficheiros.

| Pasta ou ficheiro | Formato de nome |
| :------------- | :---------- |
| Pasta de mensagem | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Entrada, saída e, se configurado, ficheiros de reconhecimento | **Carga útil da entrada**: [remetente]\_[recetor]\_X12\_[número de controlo de permuta]\_input_payload.txt </p>**Carga útil da saída**: [remetente]\_[recetor]\_X12\_[número de controlo de permuta]\_saída\_load.txt </p></p>**Entradas**: [remetente]\_[recetor]\_X12\_[número de controlo de permuta]\_entradas.txt </p></p>**Saídas**: [remetente]\_[recetor]\_X12\_[número de controlo de intercâmbio]\_saídas.txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Descrições de propriedades de mensagens EDIFACT

Aqui estão as descrições da propriedade para cada mensagem EDIFACT.

| Propriedade | Descrição |
| --- | --- |
| Remetente | O parceiro convidado especificado em **Definições de Receção**, ou o parceiro anfitrião especificado em **Definições de Envio** para um acordo EDIFACT |
| Recetor | O parceiro anfitrião especificado em **Definições de Receção**, ou o parceiro convidado especificado em **Definições** de Envio para um acordo EDIFACT |
| Aplicação Lógica | A aplicação lógica onde as ações edifact são criadas |
| Estado | O estado da mensagem EDIFACT <br>Sucesso = Recebido ou enviado uma mensagem EDIFACT válida. Não está preparado nenhum ack funcional. <br>Sucesso = Recebido ou enviado uma mensagem EDIFACT válida. O ack funcional é configurado e recebido, ou um ack funcional é enviado. <br>Falhado = Recebido ou enviado uma mensagem EDIFACT inválida <br>Pendente = Recebido ou enviado uma mensagem EDIFACT válida. O ack funcional está configurado, e espera-se um ack funcional. |
| Ack | Estado funcional de Ack (CONTRL) <br>Aceite = Recebida ou enviada um ack funcional positivo. <br>Rejeitado = Recebido ou enviado um ack funcional negativo. <br>Pendente = Esperando um ack funcional mas não recebido. <br>Pendente = Gerou um ack funcional mas não pode enviar para o parceiro. <br>Não é necessário = Ack funcional não está configurado. |
| Direção | A direção da mensagem EDIFACT |
| ID de Correlação | O ID que correlaciona todos os gatilhos e ações numa aplicação lógica |
| Tipo Msg | O tipo de mensagem EDIFACT |
| ICN | O Número de Controlo de Intercâmbio para a mensagem EDIFACT |
| TSCN | O número de controlo de conjunto de transações para a mensagem EDIFACT |
| Carimbo de data/hora | O momento em que a ação EDIFACT processou a mensagem |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>Formatos de nome EDIFACT para ficheiros de mensagens descarregados

Aqui estão os formatos de nome para cada pasta de mensagens EDIFACT descarregada e ficheiros.

| Pasta ou ficheiro | Formato de nome |
| :------------- | :---------- |
| Pasta de mensagem | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Entrada, saída e, se configurado, ficheiros de reconhecimento | **Carga útil**da entrada : [remetente]\_[recetor]\_EDIFACT\_[número de controlo de permuta]\_input_payload.txt </p>**Carga útil da saída**: [remetente]\_[recetor]\_EDIFACT\_[número de controlo de permuta]\_saída\_carga útil.txt </p></p>**Entradas**: [remetente]\_[recetor]\_EDIFACT\_[número de controlo de intercâmbio]\_entradas.txt </p></p>**Saídas**: [remetente]\_[recetor]\_EDIFACT\_[número de controlo de intercâmbio]\_saídas.txt |
|          |             |

## <a name="next-steps"></a>Passos seguintes

* [Consulta de mensagens B2B nos registos do Monitor Azure](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [Esquemas de controlo de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de controlo de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de rastreio personalizados](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
