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
ms.openlocfilehash: 6443cb727573645792a4e6c929b80c3406d72025
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261808"
---
# <a name="service-bus-diagnostic-logs"></a>Logs de diagnóstico do barramento de serviço

Você pode exibir dois tipos de logs para o barramento de serviço do Azure:
* **[Logs de atividade](../azure-monitor/platform/activity-logs-overview.md)** . Esses logs contêm informações sobre as operações executadas em um trabalho. Os registos são sempre ativados.
* **[Logs de diagnóstico](../azure-monitor/platform/resource-logs-overview.md)** . Você pode configurar os logs de diagnóstico para obter informações mais detalhadas sobre tudo o que acontece em um trabalho. Atividades de capa registos de diagnóstico desde o momento, que a tarefa é criada até que o trabalho é eliminado, incluindo atualizações e as atividades que ocorrem enquanto a tarefa está em execução.

## <a name="turn-on-diagnostic-logs"></a>Ativar registos de diagnóstico

Os logs de diagnóstico estão desabilitados por padrão. Para habilitar os logs de diagnóstico, execute as seguintes etapas:

1.  Na [portal do Azure](https://portal.azure.com), em **monitorização + gestão**, clique em **registos de diagnóstico**.

    ![navegação de folha para logs de diagnóstico](./media/service-bus-diagnostic-logs/image1.png)

2. Clique em recursos que pretende monitorizar.  

3.  Clique em **Ativar diagnósticos**.

    ![Ativar logs de diagnóstico](./media/service-bus-diagnostic-logs/image2.png)

4.  Para **Status**, clique em **no**.

    ![alterar logs de diagnóstico de status](./media/service-bus-diagnostic-logs/image3.png)

5.  Defina o destino de arquivo desejado; por exemplo, uma conta de armazenamento, um hub de eventos ou logs de Azure Monitor.

6.  Guarde as novas definições de diagnóstico.

Novas definições entrem em vigor em cerca de 10 minutos. Depois disso, os logs aparecem no destino de arquivamento configurado, na folha **logs de diagnóstico** .

Para obter mais informações sobre como configurar diagnósticos, consulte a [descrição geral dos registos de diagnóstico do Azure](../azure-monitor/platform/resource-logs-overview.md).

## <a name="diagnostic-logs-schema"></a>Esquema de registos de diagnóstico

Todos os registos são armazenados no formato de JavaScript Object Notation (JSON). Cada entrada tem campos de cadeia de caracteres que usam o formato descrito na seção a seguir.

## <a name="operational-logs-schema"></a>Esquema de registos operacionais

Os logs na categoria **OperationalLogs** capturam o que acontece durante as operações do barramento de serviço. Especificamente, esses logs capturam o tipo de operação, incluindo a criação da fila, os recursos usados e o status da operação.

Cadeias de caracteres JSON de registo operacional incluem elementos listados na tabela a seguir:

Nome | Descrição
------- | -------
ActivityId | ID interna, usada para acompanhamento
EventName | Nome da operação           
resourceId | ID do recurso de Azure Resource Manager
SubscriptionId | ID da subscrição
EventTimeString | Tempo de operação
EventProperties | Propriedades da operação
State | Estado da operação
Caller | Chamador de operação (portal do Azure ou cliente de gerenciamento)
category | OperationalLogs

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

## <a name="next-steps"></a>Passos seguintes

Consulte os links a seguir para saber mais sobre o barramento de serviço:

* [Introdução ao barramento de serviço](service-bus-messaging-overview.md)
* [Introdução ao barramento de serviço](service-bus-dotnet-get-started-with-queues.md)
