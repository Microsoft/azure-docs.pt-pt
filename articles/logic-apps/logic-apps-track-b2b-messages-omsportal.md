---
title: Acompanhar mensagens B2B com logs de Azure Monitor-aplicativos lógicos do Azure | Microsoft Docs
description: Acompanhe a comunicação B2B para contas de integração e aplicativos lógicos do Azure com o Azure Log Analytics
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 33c4efb2b783b5071513f069beac9cdf73c373a8
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997848"
---
# <a name="track-b2b-messages-with-azure-monitor-logs"></a>Controlar mensagens B2B com os registos do Azure Monitor

Depois de configurar a comunicação B2B entre parceiros comerciais em sua conta de integração, esses parceiros podem trocar mensagens com protocolos como AS2, X12 e EDIFACT. Para verificar se essas mensagens são processadas corretamente, você pode acompanhar essas mensagens com [logs de Azure monitor](../log-analytics/log-analytics-overview.md). Por exemplo, você pode usar esses recursos de acompanhamento baseados na Web para rastrear mensagens:

* Status e contagem de mensagens
* Status de confirmações
* Correlacionar mensagens com confirmações
* Descrições de erro detalhadas para falhas
* Recursos de pesquisa

> [!NOTE]
> Esta página descreveu anteriormente as etapas para executar essas tarefas com o Microsoft Operations Management Suite (OMS), que está sendo [desativado em janeiro de 2019](../azure-monitor/platform/oms-portal-transition.md), substitui essas etapas com o Azure log Analytics em vez disso. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Um aplicativo lógico que é configurado com o log de diagnóstico. Saiba [como criar um aplicativo lógico](quickstart-create-first-logic-app-workflow.md) e [como configurar o log para esse aplicativo lógico](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Uma conta de integração que é configurada com monitoramento e registro em log. Saiba [como criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e [como configurar o monitoramento e o registro em log para essa conta](../logic-apps/logic-apps-monitor-b2b-message.md).

* Se você ainda não fez isso, [publique dados de diagnóstico em logs de Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

* Depois de atender aos requisitos anteriores, você também precisa de um espaço de trabalho Log Analytics, que você usa para controlar a comunicação B2B por meio de Log Analytics. Se você não tiver um espaço de trabalho Log Analytics, saiba [como criar um espaço de trabalho do log Analytics](../azure-monitor/learn/quick-create-workspace.md).

## <a name="install-logic-apps-b2b-solution"></a>Instalar Aplicativos Lógicos B2B solução

Antes que você possa fazer com que os logs de Azure Monitor acompanhem mensagens B2B para seu aplicativo lógico, adicione a solução **aplicativos lógicos B2B** aos logs de Azure monitor. Saiba mais sobre como [Adicionar soluções a logs de Azure monitor](../azure-monitor/learn/quick-create-workspace.md).

1. No [portal do Azure](https://portal.azure.com), selecione **Todos os serviços**. Na caixa de pesquisa, localize "log Analytics" e selecione **log Analytics**.

   ![Selecionar Log Analytics](media/logic-apps-track-b2b-messages-omsportal/find-log-analytics.png)

1. Em **log Analytics**, localize e selecione seu espaço de trabalho do log Analytics. 

   ![Selecionar Log Analytics espaço de trabalho](media/logic-apps-track-b2b-messages-omsportal/select-log-analytics-workspace.png)

1. Em introdução **ao log Analytics** > **configurar soluções de monitoramento**, escolha **Exibir soluções**.

   ![Escolha "Exibir soluções"](media/logic-apps-track-b2b-messages-omsportal/log-analytics-workspace.png)

1. Na página Visão geral, escolha **Adicionar**, que abre a lista **soluções de gerenciamento** . Nessa lista, selecione **aplicativos lógicos B2B**. 

   ![Selecionar solução de Aplicativos Lógicos B2B](media/logic-apps-track-b2b-messages-omsportal/add-b2b-solution.png)

   Se você não encontrar a solução, na parte inferior da lista, escolha **carregar mais** até que a solução seja exibida.

1. Escolha **criar**, confirme o espaço de trabalho log Analytics onde você deseja instalar a solução e, em seguida, escolha **criar** novamente.   

   ![Escolha "criar" para Aplicativos Lógicos B2B](media/logic-apps-track-b2b-messages-omsportal/create-b2b-solution.png)

   Se você não quiser usar um espaço de trabalho existente, também poderá criar um novo espaço de trabalho no momento.

1. Quando terminar, volte para a página de **visão geral** do espaço de trabalho. 

   A solução Aplicativos Lógicos B2B agora aparece na página Visão geral. 
   Quando as mensagens B2B são processadas, a contagem de mensagens nessa página é atualizada.

<a name="message-status-details"></a>

## <a name="view-b2b-message-information"></a>Exibir informações da mensagem B2B

Depois que as mensagens B2B são processadas, você pode exibir o status e os detalhes dessas mensagens no bloco **aplicativos lógicos B2B** .

1. Vá para seu espaço de trabalho de análise lógica e abra a página Visão geral. Escolha **aplicativos lógicos B2B**.

   ![Contagem de mensagens atualizadas](media/logic-apps-track-b2b-messages-omsportal/b2b-overview-tile.png)

   > [!NOTE]
   > Por padrão, o bloco **aplicativos lógicos B2B** mostra dados com base em um único dia. Para alterar o escopo de dados para um intervalo diferente, escolha o controle de escopo na parte superior da página:
   > 
   > ![Alterar intervalo](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)

1. Depois que o painel status da mensagem for exibido, você poderá exibir mais detalhes de um tipo de mensagem específico, que mostra os dados com base em um único dia. Escolha o bloco para **AS2**, **X12**ou **EDIFACT**.

   ![Exibir status da mensagem](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   Uma lista de mensagens é exibida para o bloco escolhido. 
   Para saber mais sobre as propriedades de cada tipo de mensagem, consulte estas descrições de propriedade de mensagem:

   * [Propriedades da mensagem AS2](#as2-message-properties)
   * [Propriedades da mensagem X12](#x12-message-properties)
   * [Propriedades da mensagem EDIFACT](#EDIFACT-message-properties)

   Por exemplo, veja como seria uma lista de mensagens AS2:

   ![Exibir mensagens AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Para exibir ou exportar as entradas e saídas de mensagens específicas, selecione essas mensagens e escolha **baixar**. Quando for solicitado, salve o arquivo. zip em seu computador local e, em seguida, extraia esse arquivo. 

   A pasta extraída inclui uma pasta para cada mensagem selecionada. 
   Se você configurar confirmações, a pasta de mensagens também incluirá arquivos com detalhes de confirmação. 
   Cada pasta de mensagens tem pelo menos estes arquivos: 
   
   * Arquivos legíveis com a carga de entrada e detalhes da carga de saída
   * Arquivos codificados com as entradas e saídas

   Para cada tipo de mensagem, você pode encontrar os formatos de nome de pasta e arquivo aqui:

   * [Formatos de nome de arquivo e pasta AS2](#as2-folder-file-names)
   * [Formatos de nome de arquivo e pasta X12](#x12-folder-file-names)
   * [Formatos de nome de arquivo e pasta EDIFACT](#edifact-folder-file-names)

   ![Baixar arquivos de mensagem](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Para exibir todas as ações que têm a mesma ID de execução, na página **pesquisa de logs** , escolha uma mensagem na lista de mensagens.

   Você pode classificar essas ações por coluna ou Pesquisar resultados específicos.

   ![Ações com a mesma ID de execução](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Para Pesquisar resultados com consultas predefinidas, escolha **favoritos**.

   * Saiba [como criar consultas adicionando filtros](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   Ou saiba mais sobre [como localizar dados com pesquisas de log em logs de Azure monitor](../log-analytics/log-analytics-log-searches.md).

   * Para alterar a consulta na caixa de pesquisa, atualize a consulta com as colunas e os valores que você deseja usar como filtros.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Descrições de propriedade e formatos de nome para mensagens AS2, X12 e EDIFACT

Para cada tipo de mensagem, aqui estão as descrições de propriedade e os formatos de nome dos arquivos de mensagem baixados.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Descrições de propriedade de mensagem AS2

Aqui estão as descrições de propriedade para cada mensagem AS2.

| Propriedade | Descrição |
| --- | --- |
| Remetente | O parceiro convidado especificado em **configurações de recebimento**ou o parceiro host especificado em **configurações de envio** para um contrato AS2 |
| Recetor | O parceiro host especificado em **configurações de recebimento**ou o parceiro convidado especificado em **configurações de envio** para um contrato AS2 |
| Aplicação Lógica | O aplicativo lógico em que as ações AS2 estão configuradas |
| State | O status da mensagem AS2 <br>Êxito = recebeu ou enviou uma mensagem AS2 válida. Nenhum MDN está configurado. <br>Êxito = recebeu ou enviou uma mensagem AS2 válida. MDN é configurado e recebido, ou MDN é enviado. <br>Falha = recebeu uma mensagem AS2 inválida. Nenhum MDN está configurado. <br>Pendente = recebeu ou enviou uma mensagem AS2 válida. MDN está configurado e MDN é esperado. |
| Pacote | O status da mensagem MDN <br>Aceito = recebeu ou enviou um MDN positivo. <br>Pendente = aguardando para receber ou enviar um MDN. <br>Rejeitado = recebeu ou enviou um MDN negativo. <br>Não obrigatório = MDN não está configurado no contrato. |
| Direction | A direção da mensagem AS2 |
| ID de Correlação | A ID que correlaciona todos os gatilhos e ações em um aplicativo lógico |
| ID da mensagem | A ID da mensagem AS2 dos cabeçalhos de mensagem AS2 |
| Timestamp | A hora em que a ação AS2 processou a mensagem |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>Formatos de nome AS2 para arquivos de mensagem baixados

Aqui estão os formatos de nome para cada pasta e arquivos de mensagem AS2 baixados.

| Pasta ou arquivo | Formato do nome |
| :------------- | :---------- |
| Pasta de mensagens | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Entrada, saída e, se configurado, arquivos de confirmação | **Carga de entrada**: [remetente\_] [destinatário\_]\_AS2 [Correlation-\_ID] input_payload. txt </p>**Carga de saída**: [remetente\_] [destinatário\_]\_AS2 [Correlation-\_ID\_] output Payload. txt </p></p>**Entradas**: [Sender]\_[Receiver]\_AS2\_[Correlation-ID\_] inputs. txt </p></p>**Saídas**: [remetente]\_[destinatário]\_AS2\_[Correlation-ID\_] Outputs. txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Descrições da propriedade da mensagem X12

Aqui estão as descrições de propriedade para cada mensagem X12.

| Propriedade | Descrição |
| --- | --- |
| Remetente | O parceiro convidado especificado em **configurações de recebimento**ou o parceiro host especificado em **configurações de envio** para um contrato X12 |
| Recetor | O parceiro host especificado em **configurações de recebimento**ou o parceiro convidado especificado em **configurações de envio** para um contrato X12 |
| Aplicação Lógica | O aplicativo lógico em que as ações de X12 estão configuradas |
| State | O status da mensagem X12 <br>Êxito = recebeu ou enviou uma mensagem X12 válida. Nenhuma confirmação funcional está configurada. <br>Êxito = recebeu ou enviou uma mensagem X12 válida. A confirmação funcional é configurada e recebida ou uma confirmação funcional é enviada. <br>Falha = recebeu ou enviou uma mensagem X12 inválida. <br>Pendente = recebeu ou enviou uma mensagem X12 válida. A confirmação funcional está configurada e uma confirmação funcional é esperada. |
| Pacote | Status de confirmação funcional (997) <br>Aceito = recebeu ou enviou uma confirmação funcional positiva. <br>Rejeitado = recebeu ou enviou uma confirmação funcional negativa. <br>Pendente = esperando uma confirmação funcional, mas não recebida. <br>Pendente = gerou uma confirmação funcional, mas não pode enviar para o parceiro. <br>Não obrigatório = a confirmação funcional não está configurada. |
| Direction | A direção da mensagem X12 |
| ID de Correlação | A ID que correlaciona todos os gatilhos e ações em um aplicativo lógico |
| Tipo de MSG | O tipo de mensagem EDI X12 |
| ICN | O número de controle de intercâmbio para a mensagem X12 |
| TSCN | O número de controle do conjunto de transações para a mensagem X12 |
| Timestamp | A hora em que a ação X12 processou a mensagem |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>Formatos de nome X12 para arquivos de mensagem baixados

Aqui estão os formatos de nome para cada pasta e arquivos de mensagem X12 baixados.

| Pasta ou arquivo | Formato do nome |
| :------------- | :---------- |
| Pasta de mensagens | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Entrada, saída e, se configurado, arquivos de confirmação | **Carga de entrada**: [remetente\_] [destinatário\_]\_X12 [Interchange-Control-Number\_] input_payload. txt </p>**Carga de saída**: [remetente\_] [destinatário\_]\_X12 [intercâmbio de controle-número\_]\_payload de saída. txt </p></p>**Entradas**: [Sender]\_[destinatário]\_X12\_[Interchange-Control-number]\_inputs. txt </p></p>**Saídas**: [Sender]\_[destinatário]\_X12\_[Interchange-Control-number]\_Outputs. txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Descrições da propriedade da mensagem EDIFACT

Aqui estão as descrições de propriedade para cada mensagem EDIFACT.

| Propriedade | Descrição |
| --- | --- |
| Remetente | O parceiro convidado especificado em **configurações de recebimento**ou o parceiro host especificado em **configurações de envio** para um contrato EDIFACT |
| Recetor | O parceiro host especificado em **configurações de recebimento**ou o parceiro convidado especificado em **configurações de envio** para um contrato EDIFACT |
| Aplicação Lógica | O aplicativo lógico em que as ações de EDIFACT estão configuradas |
| State | O status da mensagem EDIFACT <br>Êxito = recebeu ou enviou uma mensagem EDIFACT válida. Nenhuma confirmação funcional está configurada. <br>Êxito = recebeu ou enviou uma mensagem EDIFACT válida. A confirmação funcional é configurada e recebida ou uma confirmação funcional é enviada. <br>Falha = recebeu ou enviou uma mensagem EDIFACT inválida <br>Pendente = recebeu ou enviou uma mensagem EDIFACT válida. A confirmação funcional está configurada e uma confirmação funcional é esperada. |
| Pacote | Status de ACK funcional (CONTRL) <br>Aceito = recebeu ou enviou uma confirmação funcional positiva. <br>Rejeitado = recebeu ou enviou uma confirmação funcional negativa. <br>Pendente = esperando uma confirmação funcional, mas não recebida. <br>Pendente = gerou uma confirmação funcional, mas não pode enviar para o parceiro. <br>Não obrigatório = a confirmação funcional não está configurada. |
| Direction | A direção da mensagem EDIFACT |
| ID de Correlação | A ID que correlaciona todos os gatilhos e ações em um aplicativo lógico |
| Tipo de MSG | O tipo de mensagem EDIFACT |
| ICN | O número de controle de intercâmbio para a mensagem EDIFACT |
| TSCN | O número de controle do conjunto de transações para a mensagem EDIFACT |
| Timestamp | A hora em que a ação EDIFACT processou a mensagem |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>Formatos de nome EDIFACT para arquivos de mensagem baixados

Aqui estão os formatos de nome para cada pasta e arquivos de mensagem EDIFACT baixados.

| Pasta ou arquivo | Formato do nome |
| :------------- | :---------- |
| Pasta de mensagens | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Entrada, saída e, se configurado, arquivos de confirmação | **Carga de entrada**: [remetente\_] [destinatário\_]\_EDIFACT [Interchange-Control-Number\_] input_payload. txt </p>**Carga de saída**: [remetente\_] [destinatário\_]\_EDIFACT [intercâmbio de controle-número\_]\_payload de saída. txt </p></p>**Entradas**: [Sender]\_[destinatário]\_EDIFACT\_[Interchange-Control-number]\_inputs. txt </p></p>**Saídas**: [Sender]\_[destinatário]\_EDIFACT\_[Interchange-Control-number]\_Outputs. txt |
|          |             |

## <a name="next-steps"></a>Passos Seguintes

* [Consulta de mensagens B2B em logs de Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [Esquemas de controlo de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de controlo de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de acompanhamento personalizados](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
