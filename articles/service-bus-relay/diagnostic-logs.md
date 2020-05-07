---
title: Registos de diagnóstico para ligações híbridas
description: Este artigo fornece uma visão geral de todas as atividades e registos de diagnóstico que estão disponíveis para o Azure Relay.
services: service-bus-messaging
author: spelluru
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.date: 04/27/2020
ms.author: spelluru
ms.openlocfilehash: b7ac5f1da70352115bf05df1a61120f46a85ec5d
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783464"
---
# <a name="enable-diagnostics-logs-for-azure-relay-hybrid-connections"></a>Ativar registos de diagnóstico para ligações híbridas de retransmissão azure
Quando começar a utilizar as suas Ligações Híbridas Azure Relay, é melhor monitorizar como e quando os seus ouvintes e remetentes são abertos e fechados, e como as suas Ligações Híbridas são criadas e as mensagens são enviadas. Este artigo fornece uma visão geral da atividade e dos registos de diagnóstico fornecidos pelo serviço Azure Relay. 

Pode ver dois tipos de troncos para o Relé Azure:

- [Registos de atividades](../azure-monitor/platform/platform-logs-overview.md): Estes registos têm informações sobre operações realizadas contra o seu espaço de nome no portal Azure ou através do modelo Do Gestor de Recursos Azure. Estes registos estão sempre ativados. Por exemplo: "Criar ou atualizar o espaço de nome", "Criar ou atualizar a ligação híbrida". 
- [Registos de diagnóstico](../azure-monitor/platform/platform-logs-overview.md): Pode configurar registos de diagnóstico para uma visão mais rica de tudo o que acontece com operações e ações que são conduzidas contra o seu espaço de nome utilizando a API, ou através da linguagem SDK.

## <a name="view-activity-logs"></a>Ver registos de atividades
Para visualizar os registos de atividade do seu espaço de nome Sinuoso Azure, mude para a página de **registo de atividade** no portal Azure.

![Azure Relay - registo de atividade](./media/diagnostic-logs/activity-log.png)

## <a name="enable-diagnostic-logs"></a>Ativar registos de diagnóstico

> [!NOTE]
> Os registos de diagnóstico estão disponíveis apenas para Ligações Híbridas, e não para o Relé da Fundação de Comunicação do Windows (WCF).

Para ativar os registos de diagnóstico, faça os seguintes passos:

1. No [portal Azure,](https://portal.azure.com)vá ao seu espaço de nome sinuoso Azure Relay e, em seguida, sob **monitorização,** selecione **definições**de diagnóstico .
1. Na página de **definições** de Diagnóstico, selecione **Adicionar definição de diagnóstico**.  

   ![O link "Adicionar definição de diagnóstico"](./media/diagnostic-logs/add-diagnostic-setting.png)

1. Configure as definições de diagnóstico fazendo os seguintes passos:
    1. Na caixa **Nome,** introduza um nome para as definições de diagnóstico.  
    2. Selecione **HybridConnectionsEvent** para o tipo de registo. 
    3. Selecione um dos três **destinos seguintes** para os seus registos de diagnóstico:  
        1. Se selecionar o Arquivo para uma conta de **armazenamento,** configure a conta de armazenamento onde os registos de diagnóstico serão armazenados.  
        2. Se selecionar **o Stream para um centro**de eventos, configure o centro de eventos para o quais pretende transmitir os registos de diagnóstico.
        3. Se selecionar **Enviar para Registar Analytics,** especifique a que instância de Log Analytics os diagnósticos serão enviados.  

        ![Definições de diagnóstico da amostra](./media/diagnostic-logs/sample-diagnostic-settings.png)
1. Selecione **Guardar** na barra de ferramentas para guardar as definições.

As novas definições têm efeito em cerca de 10 minutos. Os registos são apresentados no alvo de arquivo configurado, no painel de registos de **diagnóstico.** Para obter mais informações sobre a configuração das definições de diagnóstico, consulte a [visão geral dos registos de diagnóstico sinuoso](../azure-monitor/platform/diagnostic-logs-overview.md)do Azure .


## <a name="schema-for-hybrid-connections-events"></a>Schema para eventos de conexões híbridas
As cordas JSON de registo de conexões híbridas incluem os elementos listados na tabela seguinte:

| Name | Descrição |
| ------- | ------- |
| ResourceId | ID de recurso do Gestor de Recursos Azure |
| Atividadeid | Identificação interna, usada para identificar a operação especificada. Também pode ser conhecido como "TrackingId" |
| Ponto Final | O endereço do recurso Retransmissor |
| OperationName | O tipo de operação ligações híbridas que está a ser registada |
| String de Tempo de Evento | O carimbo de tempo UTC do registo de registos |
| Mensagem | A mensagem detalhada do evento |
| Categoria | Categoria do evento. Atualmente, só `HybridConnectionsEvents`existe. 


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

| Operação | Descrição | 
| --------- | ----------- | 
| AutorizaçãoFalhada | A autorização falhou.|
| InvalidSasToken | Ficha SAS inválida. | 
| ListenerAcceptingConnection | O ouvinte está a aceitar a ligação. |
| OuvinteAceitandoConnectionTimeout | A ligação aceitadora do ouvinte tem um tempo esgotado. |
| OuvinteAceitandoHttpRequestFailed | O pedido de aceitação do ouvinte em HTTP falhou devido a uma exceção. |
| OuvinteAceitando RequestTimeout | O pedido de aceitação do ouvinte tem esgotado. |  
| ListenerClosingFromExpiredToken | O ouvinte está a fechar porque o sinal de segurança expirou. | 
| ListenerRejectedConnection | O ouvinte rejeitou a ligação. |
| OuvinteReturningHttpResponse | O ouvinte está a devolver uma resposta HTTP. |  
| OuvinteReReReNdoHttpResponseFailed | O ouvinte está a devolver uma resposta HTTP com um código de falha. | 
 OuvinteSentHttpResponse | O serviço de retransmissão recebeu uma resposta HTTP do ouvinte. | 
| OuvinteNão registado | O ouvinte não está registado. | 
| OuvinteSSSSSSNão | O ouvinte não responde quando responde. | 
| MensagemEnvioToListener | A mensagem está a ser enviada para o ouvinte. |
| MensagemSentToListener | A mensagem é enviada para o ouvinte. | 
| NewListenerRegistered | Novo ouvinte registado. |
| Registo newSender | O novo remetente está a registar-se. | 
| Pedido de processamento Falhado | O processamento de uma operação de ligação híbrida falhou. | 
| SenderConnectionClosed | A ligação do remetente está fechada. |
| SenderListenerConnectionEstabelecido | O remetente e o ouvinte estabeleceram uma ligação com sucesso. |
| SenderSentHttpRequest | O remetente enviou um pedido http. | 


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Azure Relay, consulte:

* [Introdução ao Relé Azure](relay-what-is-it.md)
* [Introdução às Ligações Híbridas de Reencaminhamento](relay-hybrid-connections-dotnet-get-started.md)
