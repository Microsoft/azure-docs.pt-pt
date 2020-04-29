---
title: Registos de diagnóstico de ônibus de serviço Azure Microsoft Docs
description: Este artigo fornece uma visão geral de todos os registos operacionais e de diagnóstico que estão disponíveis para o Azure Service Bus.
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: a80fb97810fee04a4eb50c43178c168e66f29173
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80618735"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>Ativar registos de diagnóstico para ônibus de serviço

Quando começar a utilizar o seu espaço de nome do Autocarro do Serviço Azure, é melhor monitorizar como e quando o seu espaço de nome é criado, apagado ou acedido. Este artigo fornece uma visão geral de todos os registos operacionais e de diagnóstico que estão disponíveis.

A Tualmente, a Azure Service Bus suporta a atividade e os registos operacionais, que captam operações de *gestão* que são realizadas no espaço de nome do Azure Service Bus. Especificamente, estes registos capturam o tipo de operação, incluindo a criação de filas, os recursos utilizados e o estado da operação.

## <a name="operational-logs-schema"></a>Esquema de registos operacionais

Todos os registos são armazenados no formato JavaScript Object Notation (JSON) nos dois locais seguintes:

- **AzureActivity**: Exibe registos de operações e ações que são conduzidas contra o seu espaço de nome no portal Azure ou através de implementações de modelos do Gestor de Recursos Azure.
- **AzureDiagnostics**: Exibe registos de operações e ações que são conduzidas contra o seu espaço de nome utilizando a API, ou através de clientes de gestão no idioma SDK.

As cordas JSON de registo operacional incluem os elementos listados na tabela seguinte:

| Nome | Descrição |
| ------- | ------- |
| Atividadeid | ID interno, usado para identificar a atividade especificada |
| EventName | Nome da operação |
| ResourceId | ID de recurso do Gestor de Recursos Azure |
| SubscriptionId | ID da subscrição |
| String de Tempo de Evento | Tempo de operação |
| Propriedades de Eventos | Propriedades de operação |
| Estado | Estado da operação |
| Autor da chamada | Chamada de operação (portal Azure ou cliente de gestão) |
| Categoria | Registos Operacionais |

Aqui está um exemplo de uma cadeia de log operacional JSON:

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

## <a name="events-and-operations-captured-in-operational-logs"></a>Eventos e operações capturados em registos operacionais

Os registos operacionais captam todas as operações de gestão que são realizadas no espaço de nome do Azure Service Bus. As operações de dados não são capturadas, devido ao elevado volume de operações de dados que são realizadas no Azure Service Bus.

> [!NOTE]
> Para ajudá-lo a rastrear melhor as operações de dados, recomendamos a utilização de rastreios do lado do cliente.

As seguintes operações de gestão são capturadas em registos operacionais: 

| Âmbito | Operação|
|-------| -------- |
| Espaço de nomes | <ul> <li> Criar espaço de nome</li> <li> Atualizar espaço de nome </li> <li> Excluir espaço de nome </li> <li> Atualizar a política de acesso partilhado do espaço de nome </li> </ul> | 
| Filas | <ul> <li> Criar fila</li> <li> Atualizar fila</li> <li> Eliminar a fila </li> <li> Eliminar automaticamente a fila de eliminação </li> </ul> | 
| Tópico | <ul> <li> Criar tópico </li> <li> Tópico de Atualização </li> <li> Excluir Tópico </li> <li> Eliminar automaticamente o tópico de eliminar </li> </ul> |
| Subscrição | <ul> <li> Criar Subscrição </li> <li> Subscrição de Atualização </li> <li> Excluir Subscrição </li> <li> Eliminar automaticamente a subscrição </li> </ul> |

> [!NOTE]
> Atualmente, as operações *de Leitura* não são rastreadas nos registos operacionais.

## <a name="enable-operational-logs"></a>Ativar registos operacionais

Os registos operacionais são desativados por defeito. Para ativar os registos de diagnóstico, faça o seguinte:

1. No [portal Azure,](https://portal.azure.com)vá ao seu espaço de nome do Ônibus de Serviço Azure e, em seguida, sob **monitorização,** selecione **definições**de diagnóstico .

   ![O link "Definições de diagnóstico"](./media/service-bus-diagnostic-logs/image1.png)

1. No painel de definições de **Diagnóstico,** selecione **Adicionar definição de diagnóstico**.  

   ![O link "Adicionar definição de diagnóstico"](./media/service-bus-diagnostic-logs/image2.png)

1. Configure as definições de diagnóstico fazendo o seguinte:

   a. Na caixa **Nome,** introduza um nome para as definições de diagnóstico.  

   b. Selecione um dos três destinos seguintes para os seus registos de diagnóstico:  
   - Se selecionar o Arquivo para uma conta de **armazenamento,** precisa de configurar a conta de armazenamento onde os registos de diagnóstico serão armazenados.  
   - Se selecionar **o Stream para um centro**de eventos, precisa de configurar o centro de eventos para o quais pretende transmitir os registos de diagnóstico.
   - Se selecionar **Enviar para Registar Analytics,** precisa especificar para que instância de Log Analytics os diagnósticos serão enviados.  

   c. Selecione a caixa de verificação **De Registos Operacionais.**

    ![O painel "Definições de Diagnóstico"](./media/service-bus-diagnostic-logs/image3.png)

1. Selecione **Guardar**.

As novas definições têm efeito em cerca de 10 minutos. Os registos são apresentados no alvo de arquivo configurado, no painel de registos de **diagnóstico.**

Para obter mais informações sobre a configuração das definições de diagnóstico, consulte a [visão geral dos registos de diagnóstico sinuoso](../azure-monitor/platform/diagnostic-logs-overview.md)do Azure .

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Ônibus de Serviço, consulte:

* [Introdução ao ônibus de serviço](service-bus-messaging-overview.md)
* [Introdução ao Service Bus](service-bus-dotnet-get-started-with-queues.md)
