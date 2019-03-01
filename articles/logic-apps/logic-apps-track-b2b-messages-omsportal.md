---
title: Monitorizar mensagens B2B com os registos do Azure Monitor - Azure Logic Apps | Documentos da Microsoft
description: Controlar a comunicação de B2B para contas de integração e o Azure Logic Apps com o Azure Log Analytics
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 8cf5d9f3ee1503769a2ec199847175899bcd86bf
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193216"
---
# <a name="track-b2b-messages-with-azure-monitor-logs"></a>Monitorizar mensagens B2B com os registos do Azure Monitor

Depois de definir a comunicação de B2B entre parceiros comerciais na sua conta de integração, os parceiros podem trocar mensagens com protocolos, como o AS2, X12 e EDIFACT. Para verificar que estas mensagens são processadas corretamente, pode controlar estas mensagens com [registos do Azure Monitor](../log-analytics/log-analytics-overview.md). Por exemplo, pode utilizar estas capacidades de controle baseado na web para mensagens de controlo:

* Contagem de mensagens e o Estado
* Estado de agradecimentos
* Correlacionar mensagens com os agradecimentos
* Descrições de erro detalhadas para falhas
* Capacidades de pesquisa

> [!NOTE]
> Passos para saber como realizar estas tarefas com o Microsoft Operations Management Suite (OMS), que é descrito anteriormente, esta página [extinguir em Janeiro de 2019](../azure-monitor/platform/oms-portal-transition.md), substitui esses passos com o Azure Log Analytics em vez disso. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma aplicação de lógica que está configurada com o registo de diagnósticos. Saiba mais [como criar uma aplicação lógica](quickstart-create-first-logic-app-workflow.md) e [como configurar o registo para essa aplicação lógica](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Uma conta de integração que está configurada com a monitorização e registo. Saiba mais [como criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e [como configurar a monitorização e registo para essa conta](../logic-apps/logic-apps-monitor-b2b-message.md).

* Se ainda não o fez, [publicar dados de diagnóstico para os registos do Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

* Depois de cumprir os requisitos anteriores, também precisa de uma área de trabalho do Log Analytics, que são utilizados para comunicação de B2B através da análise de registo de controlo. Se não tiver uma área de trabalho do Log Analytics, saiba [como criar uma área de trabalho do Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

## <a name="install-logic-apps-b2b-solution"></a>Instalar a solução de aplicações lógicas B2B

Antes de pode ter registos do Azure Monitor monitorizar mensagens B2B para a aplicação lógica, adicione a **Logic Apps B2B/EDI** solução para os registos do Azure Monitor. Saiba mais sobre [adicionar soluções para os registos do Azure Monitor](../azure-monitor/learn/quick-create-workspace.md).

1. No [portal do Azure](https://portal.azure.com), selecione **Todos os serviços**. Na caixa de pesquisa, encontre "log analytics" e selecione **do Log Analytics**.

   ![Selecione o Log Analytics](media/logic-apps-track-b2b-messages-omsportal/find-log-analytics.png)

1. Sob **do Log Analytics**, localize e selecione a sua área de trabalho do Log Analytics. 

   ![Selecione a área de trabalho do Log Analytics](media/logic-apps-track-b2b-messages-omsportal/select-log-analytics-workspace.png)

1. Sob **introdução ao Log Analytics** > **configurar soluções de monitorização**, escolha **ver soluções**.

   ![Escolha "Ver soluções"](media/logic-apps-track-b2b-messages-omsportal/log-analytics-workspace.png)

1. Na página Descrição geral, escolha **Add**, que abre o **soluções de gestão** lista. A partir dessa lista, selecione **Logic Apps B2B/EDI**. 

   ![Selecione a solução de aplicações lógicas B2B](media/logic-apps-track-b2b-messages-omsportal/add-b2b-solution.png)

   Se não conseguir encontrar a solução, na parte inferior da lista, escolha **carregar mais** até que apareça a solução.

1. Escolher **Create**, confirme a área de trabalho do Log Analytics onde pretende instalar a solução e, em seguida, escolha **criar** novamente.   

   ![Escolha "Criar" para aplicações lógicas B2B](media/logic-apps-track-b2b-messages-omsportal/create-b2b-solution.png)

   Se não pretender utilizar uma área de trabalho existente, também pode criar uma nova área de trabalho neste momento.

1. Quando tiver terminado, volte para a área de trabalho **descrição geral** página. 

   A solução de aplicações lógicas B2B agora aparece na página de descrição geral. 
   Quando são processadas mensagens B2B, a contagem de mensagens nesta página é atualizada.

<a name="message-status-details"></a>

## <a name="view-b2b-message-information"></a>Ver informações de mensagens B2B

Após o processamento de mensagens B2B, pode ver o estado e os detalhes para essas mensagens no **Logic Apps B2B/EDI** mosaico.

1. Vá para a área de trabalho de análise de lógica e abrir a página de descrição geral. Escolher **Logic Apps B2B/EDI**.

   ![Contagem de mensagens atualizado](media/logic-apps-track-b2b-messages-omsportal/b2b-overview-tile.png)

   > [!NOTE]
   > Por predefinição, o **Logic Apps B2B/EDI** mosaico mostra os dados com base num único dia. Para alterar o âmbito de dados para um intervalo diferente, escolha o controlo de âmbito na parte superior da página:
   > 
   > ![Intervalo de alteração](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)

1. Após a mensagem de estado é apresentado o dashboard, pode ver mais detalhes sobre um tipo de mensagem específica, que mostra os dados com base num único dia. Escolha o mosaico da **AS2**, **X12**, ou **EDIFACT**.

   ![Ver o estado de mensagem](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   É apresentada uma lista de mensagens para o seu mosaico escolhido. 
   Para saber mais sobre as propriedades para cada tipo de mensagem, consulte estas descrições de propriedade da mensagem:

   * [Propriedades da mensagem AS2](#as2-message-properties)
   * [Propriedades da mensagem X12](#x12-message-properties)
   * [Propriedades da mensagem EDIFACT](#EDIFACT-message-properties)

   Por exemplo, aqui está uma lista de mensagens AS2 possível aparência:

   ![Ver mensagens AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Para ver ou exportar as entradas e saídas de mensagens específicas, selecione essas mensagens e escolha **transferir**. Quando lhe for pedido, guarde o ficheiro. zip no computador local e, em seguida, extrair esse arquivo. 

   A pasta extraída inclui uma pasta para cada mensagem selecionada. 
   Se configurou as confirmações, a pasta de mensagem também inclui arquivos com detalhes de confirmação. 
   Cada pasta de mensagem tem, pelo menos, estes ficheiros: 
   
   * Ficheiros legível por humanos com o payload de entrada e os detalhes do payload de saída
   * Ficheiros codificados com as entradas e saídas

   Para cada tipo de mensagem, pode encontrar a pasta e formatos de nome de ficheiro aqui:

   * [Formatos de nome de pasta e ficheiro de AS2](#as2-folder-file-names)
   * [X12 formatos de nome de pasta e ficheiro](#x12-folder-file-names)
   * [Formatos de nome de pasta e ficheiro EDIFACT](#edifact-folder-file-names)

   ![Transferir ficheiros de mensagens](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Para ver todas as ações que têm o mesmo ID de execução, diante da **pesquisa de registos** página, selecione uma mensagem a partir da lista de mensagem.

   Pode ordenar estas ações por coluna ou pesquisar resultados específicos.

   ![Ações com o mesmo ID de execução](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Para pesquisar resultados com consultas pré-criadas, escolha **Favoritos**.

   * Saiba mais [como criar consultas, adicione filtros](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   Ou obtenha mais informações [como encontrar dados com pesquisas de registos nos registos do Azure Monitor](../log-analytics/log-analytics-log-searches.md).

   * Para alterar a consulta na caixa de pesquisa, atualize a consulta com as colunas e os valores que pretende utilizar como filtros.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Descrições das propriedades e formatos de nome de AS2, X12 e mensagens EDIFACT

Para cada tipo de mensagem, aqui estão as descrições de propriedade e formatos de nome para ficheiros de mensagens transferido.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Descrições de propriedade de mensagem AS2

Aqui estão as descrições de propriedade para cada mensagem AS2.

| Propriedade | Descrição |
| --- | --- |
| Remetente | O parceiro convidado especificado na **receber definições**, ou o parceiro do anfitrião especificado na **enviar definições** para um contrato de AS2 |
| Recetor | O parceiro do anfitrião especificado na **receber definições**, ou o parceiro convidado especificadas **enviar definições** para um contrato de AS2 |
| Aplicação Lógica | A aplicação lógica em que são configuradas as ações de AS2 |
| Estado | O estado de mensagem AS2 <br>Êxito = recebida ou enviada uma mensagem AS2 válida. Não existem MDN está definida. <br>Êxito = recebida ou enviada uma mensagem AS2 válida. MDN foi definida e recebido ou MDN é enviada. <br>Falha ao = recebida uma mensagem AS2 inválida. Não existem MDN está definida. <br>Pendente = recebida ou enviada uma mensagem AS2 válida. MDN está configurado e é esperado o MDN. |
| ACK | O estado da mensagem MDN <br>Aceite = recebidos ou enviados um MDN positivo. <br>Pendente = esperando para receber ou enviar um MDN. <br>Rejeitado = recebidos ou enviados um MDN negativo. <br>Não é necessário = MDN não está definido no contrato. |
| Direção | A direção de mensagem AS2 |
| ID de Correlação | O ID que correlaciona todos os acionadores e ações numa aplicação lógica |
| ID da Mensagem | O ID da mensagem AS2 dos cabeçalhos de mensagem AS2 |
| Carimbo de data/hora | A hora quando a ação de AS2 processado a mensagem |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>Formatos de nome de AS2 para ficheiros de mensagens transferido

Aqui estão os formatos de nome para cada pasta de mensagem AS2 transferida e ficheiros.

| Ficheiro ou pasta | Formato de nome |
| :------------- | :---------- |
| Pasta de mensagem | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Entrada, saída e se a configurar, arquivos de confirmação | **Payload de entrada**: [remetente]\_[recetor]\_AS2\_[ID de correlação]\_input_payload.txt </p>**Payload de saída**: [remetente]\_[recetor]\_AS2\_[ID de correlação]\_saída\_payload.txt </p></p>**Entradas**: [remetente]\_[recetor]\_AS2\_[ID de correlação]\_inputs.txt </p></p>**Saídas**: [remetente]\_[recetor]\_AS2\_[ID de correlação]\_outputs.txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Descrições das propriedades da mensagem X12

Aqui estão as descrições de propriedade para cada X12 mensagem.

| Propriedade | Descrição |
| --- | --- |
| Remetente | O parceiro convidado especificado na **receber definições**, ou o parceiro do anfitrião especificado na **enviar definições** para um X12 contrato |
| Recetor | O parceiro do anfitrião especificado na **receber definições**, ou o parceiro convidado especificadas **enviar definições** para um X12 contrato |
| Aplicação Lógica | A aplicação lógica em que as ações são configuradas de X12 |
| Estado | O estado da mensagem X12 <br>Êxito = recebidos ou enviados um X12 válido mensagem. Não existem ack funcional é configurado. <br>Êxito = recebidos ou enviados um X12 válido mensagem. Ack funcional foi definida e recebido, ou um ack funcional é enviado. <br>Falha ao = recebidos ou enviados um X12 inválido mensagem. <br>Pendente = recebidos ou enviados um X12 válido mensagem. Ack funcional é configurado e prevê-se uma ack funcional. |
| ACK | Estado funcional do Ack (997) <br>Aceite = recebidos ou enviados uma ACK positivo. funcionais <br>Rejeitado = recebidos ou enviados uma ACK negativo. funcionais <br>Pendente = esperando um ack funcional, mas não foi recebido. <br>Pendente = gerado um ack funcional, mas não é possível enviar para o parceiro. <br>Não é necessário = funcional ack não está configurado. |
| Direção | A direção da mensagem X12 |
| ID de Correlação | O ID que correlaciona todos os acionadores e ações numa aplicação lógica |
| Tipo de mensagem | O tipo de mensagem 12 X de EDI |
| ICN | O número de controlo de intercâmbio de X12 mensagem |
| TSCN | O número de controlo do transação definido para o X12 mensagem |
| Carimbo de data/hora | A hora quando o X12 ação processado a mensagem |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>Formatos de nome de X12 para ficheiros de mensagens transferido

Aqui estão os formatos de nome para cada transferido X12 pasta e ficheiros de mensagens.

| Ficheiro ou pasta | Formato de nome |
| :------------- | :---------- |
| Pasta de mensagem | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Entrada, saída e se a configurar, arquivos de confirmação | **Payload de entrada**: [remetente]\_[recetor]\_X12\_[número de controlo de intercâmbio]\_input_payload.txt </p>**Payload de saída**: [remetente]\_[recetor]\_X12\_[número de controlo de intercâmbio]\_saída\_payload.txt </p></p>**Entradas**: [remetente]\_[recetor]\_X12\_[número de controlo de intercâmbio]\_inputs.txt </p></p>**Saídas**: [remetente]\_[recetor]\_X12\_[número de controlo de intercâmbio]\_outputs.txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Descrições de propriedade de mensagem EDIFACT

Aqui estão as descrições de propriedade para cada mensagem EDIFACT.

| Propriedade | Descrição |
| --- | --- |
| Remetente | O parceiro convidado especificado na **receber definições**, ou o parceiro do anfitrião especificado na **enviar definições** para um contrato EDIFACT |
| Recetor | O parceiro do anfitrião especificado na **receber definições**, ou o parceiro convidado especificadas **enviar definições** para um contrato EDIFACT |
| Aplicação Lógica | A aplicação lógica em que são configuradas as ações de EDIFACT |
| Estado | O estado de mensagem EDIFACT <br>Êxito = recebida ou enviada uma mensagem EDIFACT válida. Não existem ack funcional é configurado. <br>Êxito = recebida ou enviada uma mensagem EDIFACT válida. Ack funcional foi definida e recebido, ou um ack funcional é enviado. <br>Falha ao = recebida ou enviada uma mensagem EDIFACT inválida <br>Pendente = recebida ou enviada uma mensagem EDIFACT válida. Ack funcional é configurado e prevê-se uma ack funcional. |
| ACK | Estado funcional do Ack (997) <br>Aceite = recebidos ou enviados uma ACK positivo. funcionais <br>Rejeitado = recebidos ou enviados uma ACK negativo. funcionais <br>Pendente = esperando um ack funcional, mas não foi recebido. <br>Pendente = gerado um ack funcional, mas não é possível enviar para o parceiro. <br>Não é necessário = Ack funcional não está configurado. |
| Direção | A direção de mensagem de EDIFACT |
| ID de Correlação | O ID que correlaciona todos os acionadores e ações numa aplicação lógica |
| Tipo de mensagem | O tipo de mensagem EDIFACT |
| ICN | O número de controlo de intercâmbio para a mensagem EDIFACT |
| TSCN | O número de controlo do transações definido para a mensagem EDIFACT |
| Carimbo de data/hora | A hora quando a ação de EDIFACT processado a mensagem |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>Formatos de nome EDIFACT para ficheiros de mensagens transferido

Aqui estão os formatos de nome para cada pasta de mensagem EDIFACT transferida e ficheiros.

| Ficheiro ou pasta | Formato de nome |
| :------------- | :---------- |
| Pasta de mensagem | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Entrada, saída e se a configurar, arquivos de confirmação | **Payload de entrada**: [remetente]\_[recetor]\_EDIFACT\_[número de controlo de intercâmbio]\_input_payload.txt </p>**Payload de saída**: [remetente]\_[recetor]\_EDIFACT\_[número de controlo de intercâmbio]\_saída\_payload.txt </p></p>**Entradas**: [remetente]\_[recetor]\_EDIFACT\_[número de controlo de intercâmbio]\_inputs.txt </p></p>**Saídas**: [remetente]\_[recetor]\_EDIFACT\_[número de controlo de intercâmbio]\_outputs.txt |
|          |             |

## <a name="next-steps"></a>Passos Seguintes

* [Consulta para mensagens B2B nos registos do Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [Esquemas de controlo de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de controlo de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de controlo personalizado](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
