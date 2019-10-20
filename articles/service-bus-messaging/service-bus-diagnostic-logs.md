---
title: Logs de diagnóstico do barramento de serviço do Azure | Microsoft Docs
description: Saiba como configurar logs de diagnóstico para o barramento de serviço no Azure.
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 41e0bdc1f04c9491ebe939f46b59ae4eb2bc7ab6
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592432"
---
# <a name="enable-diagnostic-logs-for-service-bus"></a>Habilitar logs de diagnóstico para o barramento de serviço

Ao começar a usar o namespace do barramento de serviço do Azure, convém monitorar como e quando o namespace é criado, excluído ou acessado. Este artigo fornece uma visão geral de todos os logs operacionais/de diagnóstico que estão disponíveis.

Atualmente, o barramento de serviço do Azure dá suporte a logs de atividade/operacionais que capturam **operações de gerenciamento** executadas no namespace do barramento de serviço do Azure. Especificamente, esses logs capturam o tipo de operação, incluindo a criação da fila, os recursos usados e o status da operação.

## <a name="operational-logs-schema"></a>Esquema de logs operacionais

Todos os logs são armazenados no formato JavaScript Object Notation (JSON) nos dois locais abaixo.

- **AzureActivity** -exibe logs de operações/ações realizadas em relação ao namespace no portal ou por meio de implantações de modelo de Azure Resource Manager.
- **AzureDiagnostics** -exibe logs de operações/ações realizadas em relação ao namespace usando a API ou por meio de clientes de gerenciamento no SDK do idioma.

As cadeias de caracteres JSON do log operacional incluem elementos listados na tabela a seguir:

| Nome | Descrição |
| ------- | ------- |
| ActivityId | ID interna, usada para identificar a atividade especificada |
| eventName | Nome da operação |
| ResourceId | ID do recurso de Azure Resource Manager |
| SubscriptionId | ID de Subscrição |
| EventTimeString | Tempo de operação |
| Eventoproperties | Propriedades da operação |
| Estado | Estado da operação |
| Autor da chamada | Chamador de operação (portal do Azure ou cliente de gerenciamento) |
| Categoria | OperationalLogs |

Aqui está um exemplo de uma cadeia de caracteres JSON do log operacional:

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="what-eventsoperations-are-captured-in-operational-logs"></a>Quais eventos/operações são capturados em logs operacionais?

Logs de operação capturam todas as operações de gerenciamento executadas no namespace do barramento de serviço do Azure. As operações de dados não são capturadas devido ao alto volume de operações de dados que são realizadas no barramento de serviço do Azure.

> [!NOTE]
> Para acompanhar melhor as operações de dados, é recomendável usar o rastreamento do lado do cliente.

As operações de gerenciamento abaixo são capturadas em logs operacionais- 

| Âmbito | Operação|
|-------| -------- |
| Namespace | <ul> <li> Criar namespace</li> <li> Atualizar namespace </li> <li> Excluir namespace </li>  </ul> | 
| Fila | <ul> <li> Criar fila</li> <li> Atualizar fila</li> <li> Excluir fila </li> </ul> | 
| Tópico | <ul> <li> Criar tópico </li> <li> Atualizar tópico </li> <li> Excluir tópico </li> </ul> |
| Subscrição | <ul> <li> Criar Subscrição </li> <li> Atualizar assinatura </li> <li> Excluir assinatura </li> </ul> |

> [!NOTE]
> Atualmente, as operações de **leitura** não são acompanhadas nos logs operacionais.

## <a name="how-to-enable-operational-logs"></a>Como habilitar logs operacionais?

Os logs operacionais são desabilitados por padrão. Para habilitar os logs de diagnóstico, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com), navegue até o namespace do barramento de serviço do Azure e, em **monitoramento**, clique em **configurações de diagnóstico**.

   ![navegação de folha para logs de diagnóstico](./media/service-bus-diagnostic-logs/image1.png)

2. Clique em **Adicionar configuração de diagnóstico** para definir as configurações de diagnóstico.  

   ![ativar logs de diagnóstico](./media/service-bus-diagnostic-logs/image2.png)

3. Definir as configurações de diagnóstico
   1. Digite um **nome** para identificar as configurações de diagnóstico.
   2. Escolha um destino para o diagnóstico.
      - Se você escolher a **conta de armazenamento**, precisará configurar a conta de armazenamento na qual o diagnóstico será armazenado.
      - Se você escolher os **hubs de eventos**, precisará configurar o Hub de eventos apropriado para o qual as configurações de diagnóstico serão transmitidas.
      - Se você escolher **log Analytics**, precisará especificar qual instância do log Analytics o diagnóstico será enviado.
    3. Verifique **OperationalLogs**.

       ![alterar logs de diagnóstico de status](./media/service-bus-diagnostic-logs/image3.png)

4. Clique em **Guardar**.


As novas configurações entram em vigor em cerca de 10 minutos. Depois disso, os logs aparecem no destino de arquivamento configurado, na folha **logs de diagnóstico** .

Para obter mais informações sobre como configurar o diagnóstico, consulte a [visão geral dos logs de diagnóstico do Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="next-steps"></a>Passos seguintes

Consulte os links a seguir para saber mais sobre o barramento de serviço:

* [Introdução ao barramento de serviço](service-bus-messaging-overview.md)
* [Introdução ao barramento de serviço](service-bus-dotnet-get-started-with-queues.md)
