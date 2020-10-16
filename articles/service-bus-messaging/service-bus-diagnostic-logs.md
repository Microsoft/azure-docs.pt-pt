---
title: Registos de diagnóstico de autocarros da Azure Service / Microsoft Docs
description: Este artigo fornece uma visão geral de todos os registos operacionais e de diagnóstico que estão disponíveis para a Azure Service Bus.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: f227f5a988ccd51425b4f43e87b4ed4d9af74e1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88064439"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>Ativar registos de diagnóstico para Service Bus

Quando começar a utilizar o seu espaço de nomes Azure Service Bus, poderá querer monitorizar como e quando o seu espaço de nome é criado, eliminado ou acedido. Este artigo fornece uma visão geral de todos os registos operacionais e de diagnóstico que estão disponíveis.

A Azure Service Bus atualmente suporta atividade e registos operacionais, que capturam *operações de gestão* que são realizadas no espaço de nomes do Azure Service Bus. Especificamente, estes registos captam o tipo de operação, incluindo a criação de filas, os recursos utilizados e o estado da operação.

## <a name="operational-logs-schema"></a>Esquema de registos operacionais

Todos os registos são armazenados no formato JavaScript Object Notation (JSON) nos dois locais seguintes:

- **AzureActivity**: Exibe registos de operações e ações que são conduzidas contra o seu espaço de nome no portal Azure ou através de implementações de modelos do Azure Resource Manager.
- **AzureDiagnostics**: Exibe registos de operações e ações que são conduzidas contra o seu espaço de nome utilizando a API, ou através de clientes de gestão na língua SDK.

As cordas JSON de registo operacional incluem os elementos listados na tabela seguinte:

| Nome | Descrição |
| ------- | ------- |
| ActivityId | ID interno, usado para identificar a atividade especificada |
| EventName | Nome da operação |
| ResourceId | Azure Resource Manager resource ID |
| SubscriptionId | ID da subscrição |
| EventTimeString | Tempo de funcionamento |
| EventProperties | Propriedades de operação |
| Estado | Estado da operação |
| Autor da chamada | Chamador de operação (o portal Azure ou cliente de gestão) |
| Categoria | Logística |

Aqui está um exemplo de uma cadeia de log JSON operacional:

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

Os registos operacionais captam todas as operações de gestão que são realizadas no espaço de nomes do Azure Service Bus. As operações de dados não são capturadas, devido ao elevado volume de operações de dados que são realizadas na Azure Service Bus.

> [!NOTE]
> Para ajudá-lo a rastrear melhor as operações de dados, recomendamos a utilização de rastreios do lado do cliente.

As seguintes operações de gestão são capturadas em registos operacionais: 

| Âmbito | Operação|
|-------| -------- |
| Espaço de Nomes | <ul> <li> Criar espaço de nome</li> <li> Atualizar espaço de nome </li> <li> Excluir espaço de nomes </li> <li> Atualizar a política de Partilha de Espaços de Nome </li> </ul> | 
| Fila | <ul> <li> Criar fila</li> <li> Atualização da fila</li> <li> Apagar fila </li> <li> AutoDelete Delete Queue </li> </ul> | 
| Tópico | <ul> <li> Criar Tópico </li> <li> Tópico de Atualização </li> <li> Eliminar Tópico </li> <li> AutoDelete Delete Topic </li> </ul> |
| Subscrição | <ul> <li> Criar Subscrição </li> <li> Atualização subscrição </li> <li> Excluir subscrição </li> <li> Assinatura de Exclusão automática </li> </ul> |

> [!NOTE]
> Atualmente, as operações *de leitura* não são rastreadas nos registos operacionais.

## <a name="enable-operational-logs"></a>Ativar registos operacionais

Os registos operacionais são desativado por predefinição. Para ativar registos de diagnóstico, faça o seguinte:

1. No [portal Azure,](https://portal.azure.com)aceda ao seu espaço de nomes para o Azure Service Bus e, em seguida, em **Monitorização,** selecione  **Definições de Diagnóstico**.

   ![A ligação "Definições de diagnóstico"](./media/service-bus-diagnostic-logs/image1.png)

1. No painel de **definições de Diagnóstico,** selecione **Adicionar a definição de diagnóstico**.  

   ![A ligação "Adicionar definição de diagnóstico"](./media/service-bus-diagnostic-logs/image2.png)

1. Configure as definições de diagnóstico fazendo o seguinte:

   a. Na caixa **Nome,** insira um nome para as definições de diagnóstico.  

   b. Selecione um dos seguintes três destinos para os seus registos de diagnóstico:  
   - Se selecionar **o Archive para uma conta de armazenamento,** tem de configurar a conta de armazenamento onde os registos de diagnóstico serão armazenados.  
   - Se selecionar **o Stream para um centro**de eventos, tem de configurar o centro de eventos para o quais pretende transmitir os registos de diagnóstico.
   - Se selecionar **Enviar para Registar Analytics,** tem de especificar para que instância de Log Analytics os diagnósticos serão enviados.  

   c. Selecione a caixa de verificação **'Registos Operacionais'.**

    ![O painel "Definições de Diagnóstico"](./media/service-bus-diagnostic-logs/image3.png)

1. Selecione **Guardar**.

As novas definições fazem efeito em cerca de 10 minutos. Os registos são apresentados no alvo de arquivo configurado, no painel de **registos de diagnóstico.**

Para obter mais informações sobre a configuração das definições de diagnóstico, consulte a [visão geral dos registos de diagnósticos do Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Service Bus, consulte:

* [Introdução ao Service Bus](service-bus-messaging-overview.md)
* [Introdução ao Service Bus](service-bus-dotnet-get-started-with-queues.md)