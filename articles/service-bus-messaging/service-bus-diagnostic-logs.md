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
ms.openlocfilehash: 5bdda54ef46085cb1f3e33fe1d9f60937da9706f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355216"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>Habilitar logs de diagnóstico para o barramento de serviço

Ao começar a usar o namespace do barramento de serviço do Azure, convém monitorar como e quando o namespace é criado, excluído ou acessado. Este artigo fornece uma visão geral de todos os logs operacionais e de diagnóstico disponíveis.

Atualmente, o barramento de serviço do Azure dá suporte a atividades e logs operacionais, que capturam *operações de gerenciamento* executadas no namespace do barramento de serviço do Azure. Especificamente, esses logs capturam o tipo de operação, incluindo a criação da fila, os recursos usados e o status da operação.

## <a name="operational-logs-schema"></a>Esquema de registos operacionais

Todos os logs são armazenados no formato JavaScript Object Notation (JSON) nos dois locais a seguir:

- **AzureActivity**: exibe logs de operações e ações que são realizadas em relação ao seu namespace no portal do Azure ou por meio de implantações de modelo Azure Resource Manager.
- **AzureDiagnostics**: exibe logs de operações e ações que são realizadas em seu namespace usando a API ou por meio de clientes de gerenciamento no SDK do idioma.

As cadeias de caracteres JSON do log operacional incluem os elementos listados na tabela a seguir:

| Nome | Descrição |
| ------- | ------- |
| ActivityId | ID interna, usada para identificar a atividade especificada |
| EventName | Nome da operação |
| ResourceId | ID do recurso de Azure Resource Manager |
| SubscriptionId | ID de Subscrição |
| EventTimeString | Tempo de operação |
| EventProperties | Propriedades da operação |
| Estado | Estado da operação |
| Autor da chamada | Chamador de operação (o portal do Azure ou o cliente de gerenciamento) |
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

## <a name="events-and-operations-captured-in-operational-logs"></a>Eventos e operações capturados em logs operacionais

Os logs operacionais capturam todas as operações de gerenciamento executadas no namespace do barramento de serviço do Azure. As operações de dados não são capturadas devido ao alto volume de operações de dados que são realizadas no barramento de serviço do Azure.

> [!NOTE]
> Para ajudá-lo a acompanhar melhor as operações de dados, é recomendável usar o rastreamento do lado do cliente.

As seguintes operações de gerenciamento são capturadas em logs operacionais: 

| Âmbito | Operação|
|-------| -------- |
| Espaço de Nomes | <ul> <li> Criar Espaço de Nomes</li> <li> Atualizar namespace </li> <li> Excluir namespace </li>  </ul> | 
| Fila | <ul> <li> Criar Fila</li> <li> Atualizar fila</li> <li> Excluir fila </li> </ul> | 
| Tópico | <ul> <li> Criar tópico </li> <li> Atualizar tópico </li> <li> Excluir tópico </li> </ul> |
| Subscrição | <ul> <li> Criar Subscrição </li> <li> Atualizar assinatura </li> <li> Excluir assinatura </li> </ul> |

> [!NOTE]
> Atualmente, as operações de *leitura* não são acompanhadas nos logs operacionais.

## <a name="enable-operational-logs"></a>Habilitar logs operacionais

Os logs operacionais são desabilitados por padrão. Para habilitar os logs de diagnóstico, faça o seguinte:

1. Na [portal do Azure](https://portal.azure.com), acesse o namespace do barramento de serviço do Azure e, em **monitoramento**, selecione **configurações de diagnóstico**.

   ![O link "configurações de diagnóstico"](./media/service-bus-diagnostic-logs/image1.png)

1. No painel **configurações de diagnóstico** , selecione **Adicionar configuração de diagnóstico**.  

   ![O link "adicionar configuração de diagnóstico"](./media/service-bus-diagnostic-logs/image2.png)

1. Defina as configurações de diagnóstico fazendo o seguinte:

   a. Na caixa **nome** , insira um nome para as configurações de diagnóstico.  

   b. Selecione um dos três destinos a seguir para seus logs de diagnóstico:  
   - Se você selecionar **arquivar em uma conta de armazenamento**, precisará configurar a conta de armazenamento onde os logs de diagnóstico serão armazenados.  
   - Se você selecionar **fluxo para um hub de eventos**, precisará configurar o Hub de eventos para o qual deseja transmitir os logs de diagnóstico.
   - Se você selecionar **Enviar para log Analytics**, será necessário especificar a qual instância do log Analytics o diagnóstico será enviado.  

   c. Marque a caixa de seleção **OperationalLogs** .

    ![O painel "configurações de diagnóstico"](./media/service-bus-diagnostic-logs/image3.png)

1. Selecione **Guardar**.

As novas configurações entram em vigor em cerca de 10 minutos. Os logs são exibidos no destino de arquivamento configurado, no painel **logs de diagnóstico** .

Para obter mais informações sobre como definir as configurações de diagnóstico, consulte a [visão geral dos logs de diagnóstico do Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o barramento de serviço, consulte:

* [Introdução ao barramento de serviço](service-bus-messaging-overview.md)
* [Introdução ao barramento de serviço](service-bus-dotnet-get-started-with-queues.md)
