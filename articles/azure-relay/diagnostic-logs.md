---
title: Registos de diagnóstico para ligações híbridas
description: Este artigo fornece uma visão geral de todos os registos de atividade e diagnóstico que estão disponíveis para Azure Relay.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 7ca6dc95567a4867d3c58e0efad0fc74289e586c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079102"
---
# <a name="enable-diagnostics-logs-for-azure-relay-hybrid-connections"></a>Ativar registos de diagnóstico para ligações híbridas Azure Relay
Quando começar a utilizar as suas Ligações Híbridas Azure Relay, talvez queira monitorizar como e quando os seus ouvintes e remetentes são abertos e fechados, e como as suas Ligações Híbridas são criadas e as mensagens são enviadas. Este artigo fornece uma visão geral dos registos de atividade e diagnóstico fornecidos pelo serviço Azure Relay. 

Pode ver dois tipos de registos para Azure Relay:

- [Registos de atividades](../azure-monitor/essentials/platform-logs-overview.md): Estes registos têm informações sobre operações realizadas contra o seu espaço de nome no portal Azure ou através do modelo Azure Resource Manager. Estes registos estão sempre ativados. Por exemplo: "Criar ou atualizar o espaço de nomes", "Criar ou atualizar a ligação híbrida". 
- [Registos de diagnóstico](../azure-monitor/essentials/platform-logs-overview.md): Pode configurar registos de diagnóstico para uma visão mais rica de tudo o que acontece com operações e ações que são conduzidas contra o seu espaço de nome utilizando a API, ou através da linguagem SDK.

## <a name="view-activity-logs"></a>Ver registos de atividades
Para visualizar registos de atividade para o seu espaço de nomes Azure Relay, mude para a página **de registo de Atividade** no portal Azure.

![Azure Relay - log de atividade](./media/diagnostic-logs/activity-log.png)

## <a name="enable-diagnostic-logs"></a>Ativar registos de diagnóstico

> [!NOTE]
> Os registos de diagnóstico só estão disponíveis para ligações híbridas, e não para o Relé da Fundação de Comunicação do Windows (WCF).

Para ativar registos de diagnóstico, faça os seguintes passos:

1. No [portal Azure,](https://portal.azure.com)aceda ao seu espaço de nomes Azure Relay e, em seguida, em **Monitorização,** selecione  **definições de Diagnóstico**.
1. Na página de **definições de Diagnóstico,** selecione **Adicionar a definição de diagnóstico**.  

   ![A ligação "Adicionar definição de diagnóstico"](./media/diagnostic-logs/add-diagnostic-setting.png)

1. Configure as definições de diagnóstico fazendo os seguintes passos:
    1. Na caixa **Nome,** insira um nome para as definições de diagnóstico.  
    2. **Selecione HybridConnectionsEvent** para o tipo de registo. 
    3. Selecione um dos seguintes três **destinos** para os seus registos de diagnóstico:  
        1. Se selecionar **o Arquivo para uma conta de armazenamento,** configuure a conta de armazenamento onde os registos de diagnóstico serão armazenados.  
        2. Se selecionar **o Stream para um centro de eventos,** configuure o centro de eventos para o quais pretende transmitir os registos de diagnóstico.
        3. Se selecionar **Enviar para Registar Análises,** especifique qual a instância de Log Analytics para a qual os diagnósticos serão enviados.  

        ![Definições de diagnóstico de amostra](./media/diagnostic-logs/sample-diagnostic-settings.png)
1. **Selecione Guarde** na barra de ferramentas para guardar as definições.

As novas definições fazem efeito em cerca de 10 minutos. Os registos são apresentados no alvo de arquivo configurado, no painel de **registos de diagnóstico.** Para obter mais informações sobre a configuração das definições de diagnóstico, consulte a [visão geral dos registos de diagnósticos do Azure](../azure-monitor/essentials/platform-logs-overview.md).


## <a name="schema-for-hybrid-connections-events"></a>Schema para eventos de conexões híbridas
As cordas JSON do registo de eventos de ligações híbridas incluem os elementos listados na tabela seguinte:

| Nome | Descrição |
| ------- | ------- |
| ResourceId | Azure Resource Manager resource ID |
| ActivityId | Identificação interna, usada para identificar a operação especificada. Pode também ser conhecido como "TrackingId" |
| Ponto final | O endereço do recurso Relay |
| OperationName | O tipo de operação de conexões híbridas que está sendo registado |
| EventTimeString | O calendário utc do registo de registo |
| Mensagem | A mensagem detalhada do evento |
| Categoria | Categoria do evento. Atualmente, existe `HybridConnectionsEvents` apenas. 


## <a name="sample-hybrid-connections-event"></a>Evento de conexões híbridas de amostra
Aqui está um evento de conexões híbridas de amostra no formato JSON. 

```json
{
    "resourceId": "/SUBSCRIPTIONS/0000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/MyResourceGroup/PROVIDERS/MICROSOFT.RELAY/NAMESPACES/MyRelayNamespace",
    "ActivityId": "7006a0db-27eb-445c-939b-ce86133014cc",
    "endpoint": "sb://myrelaynamespace.servicebus.windows.net/myhybridconnection/7006a0db-27eb-445c-939b-ce86133014cc_G5",
    "operationName": "Microsoft.Relay/HybridConnections/NewSenderRegistering",
    "EventTimeString": "2020-04-27T20:27:57.3842810Z",
    "message": "A new sender is registering.",
    "category": "HybridConnectionsEvent"
}
```

## <a name="events-and-operations-captured-in-diagnostic-logs"></a>Eventos e operações capturados em registos de diagnóstico

| Operação                           | Description                                                     |
|-------------------------------------|-----------------------------------------------------------------|
| Autorizações Destruídas                 | A autorização falhou.                                           |
| InvalidSasToken                     | Ficha SAS inválida.                                              |
| EscutaAcceptingConnection         | O ouvinte está a aceitar a ligação.                           |
| ListenerAcceptingConnectionTimeout  | O ouvinte que aceita a ligação está esgotado.                |
| OuvinteAcceptingHttpRequestFailed  | O ouvinte que aceita o pedido HTTP falhou devido a uma exceção. |
| ListenerAcceptingRequestTimeout     | O ouvinte que aceita o pedido está esgotado.                   |
| ListenerClosingFromExpiredToken     | O ouvinte está a fechar porque o sinal de segurança expirou. |
| EscutarRejectedConnection          | O ouvinte rejeitou a ligação.                       |
| OuvinteReturningHttpResponse       | O ouvinte está a devolver uma resposta HTTP.                     |
| OuvinteReturningHttpResponseFailed | O ouvinte está a devolver uma resposta HTTP com um código de falha. |
| ListenerSentHttpResponse            | O serviço de retransmissão recebeu uma resposta HTTP do ouvinte.  |
| OuvinteUntrosserado                | O ouvinte não está registado.                                   |
| OuvinteUnresponsive                | O ouvinte não responde ao devolver uma resposta.         |
| MensagensEndingToListener            | A mensagem está a ser enviada ao ouvinte.                              |
| MensagensToListener               | A mensagem é enviada ao ouvinte.                                    |
| NewListenerRegistered               | Novo ouvinte registado.                                        |
| NewSenderRegistering                | O novo remetente está a registar-se.                                      |
| ProcessamentoRequestFailed             | O processamento de uma operação de ligação híbrida falhou.     |
| SenderConnectionClosed              | A ligação do remetente está fechada.                                |
| SenderListenerConnectionEstablished | O remetente e o ouvinte estabeleceram a ligação com sucesso.    |
| SenderSentHttpRequest               | O remetente enviou um pedido HTTP.                                |


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a Azure Relay, consulte:

* [Introdução ao Relé Azure](relay-what-is-it.md)
* [Introdução às Ligações Híbridas de Reencaminhamento](relay-hybrid-connections-dotnet-get-started.md)
