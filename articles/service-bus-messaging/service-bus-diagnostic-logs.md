---
title: Registos de diagnóstico do Service Bus do Azure | Documentos da Microsoft
description: Saiba como configurar registos de diagnóstico para o Service Bus no Azure.
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
ms.openlocfilehash: 7d4cb8e55c5d1561c09cf85122550a66e3671f17
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57216344"
---
# <a name="service-bus-diagnostic-logs"></a>Registos de diagnóstico do Service Bus

Pode ver dois tipos de registos do Azure Service Bus:
* **[Registos de atividades](../azure-monitor/platform/activity-logs-overview.md)**. Estes registos contêm informações sobre as operações executadas numa tarefa. Os registos são sempre ativados.
* **[Os registos de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md)**. Pode configurar os registos de diagnóstico para informações mais detalhadas sobre tudo o que acontece dentro de uma tarefa. Atividades de capa registos de diagnóstico desde o momento, que a tarefa é criada até que o trabalho é eliminado, incluindo atualizações e as atividades que ocorrem enquanto a tarefa está em execução.

## <a name="turn-on-diagnostic-logs"></a>Ativar registos de diagnóstico

Os registos de diagnóstico estão desativados por predefinição. Para ativar os registos de diagnóstico, execute os seguintes passos:

1.  Na [portal do Azure](https://portal.azure.com), em **monitorização + gestão**, clique em **registos de diagnóstico**.

    ![navegação do painel para registos de diagnóstico](./media/service-bus-diagnostic-logs/image1.png)

2. Clique em recursos que pretende monitorizar.  

3.  Clique em **Ativar diagnósticos**.

    ![Ativar registos de diagnóstico](./media/service-bus-diagnostic-logs/image2.png)

4.  Para **Status**, clique em **no**.

    ![alterar os registos de diagnóstico de estado](./media/service-bus-diagnostic-logs/image3.png)

5.  Definir o destino de arquivo que pretende; Por exemplo, uma conta de armazenamento, um hub de eventos ou do Azure Monitor registos.

6.  Guarde as novas definições de diagnóstico.

Novas definições entrem em vigor em cerca de 10 minutos. Depois disso, os registos de aparecem no destino de arquivo configurado, no **registos de diagnóstico** painel.

Para obter mais informações sobre como configurar diagnósticos, consulte a [descrição geral dos registos de diagnóstico do Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="diagnostic-logs-schema"></a>Esquema de registos de diagnóstico

Todos os registos são armazenados no formato de JavaScript Object Notation (JSON). Cada entrada tem campos de cadeia de caracteres que utilizam o formato descrito na secção seguinte.

## <a name="operational-logs-schema"></a>Esquema de registos operacionais

Iniciar sessão a **OperationalLogs** categoria capturar o que acontece durante as operações do Service Bus. Especificamente, estes registos capturam o tipo de operação, incluindo a criação da fila, os recursos utilizados e o estado da operação.

Cadeias de caracteres JSON de registo operacional incluem elementos listados na tabela a seguir:

Nome | Descrição
------- | -------
ActivityId | ID interno, utilizado para o controlo
EventName | Nome da operação           
resourceId | ID de recurso do Azure Resource Manager
SubscriptionId | ID da subscrição
EventTimeString | Tempo de operação
EventProperties | Propriedades de operação
Estado | Estado da operação
Autor da chamada | Chamador de operação (cliente do Azure de portal ou de gestão)
categoria | OperationalLogs

Eis um exemplo de um cadeia de caracteres do JSON de registo operacional:

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

## <a name="next-steps"></a>Passos Seguintes

Consulte as seguintes ligações para saber mais sobre o Service Bus:

* [Introdução ao Service Bus](service-bus-messaging-overview.md)
* [Introdução ao Service Bus](service-bus-dotnet-get-started-with-queues.md)
