---
title: Definir filtros de subscrições em Azure Service Bus | Microsoft Docs
description: Este artigo fornece exemplos para definir filtros e ações em subscrições de tópicos Azure Service Bus.
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: bcbb72901ed8e2dfe0932163ee18683e0011ce70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654390"
---
# <a name="set-subscription-filters-azure-service-bus"></a>Definir filtros de subscrição (Azure Service Bus)
Este artigo fornece alguns exemplos sobre a definição de filtros nas subscrições de tópicos do Service Bus. Para obter informações conceptuais sobre filtros, consulte [Filtros.](topic-filters.md)

## <a name="filter-on-system-properties"></a>Filtrar as propriedades do sistema
Para se referir a uma propriedade do sistema num filtro, utilize o seguinte formato: `sys.<system-property-name>` . 

```csharp
sys.label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

## <a name="filter-on-message-properties"></a>Filtrar nas propriedades da mensagem
Aqui estão os exemplos de utilização de propriedades de mensagens num filtro. Pode aceder às propriedades da mensagem utilizando `user.property-name` ou apenas `property-name` .

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

## <a name="filter-on-message-properties-with-special-characters"></a>Filtrar nas propriedades da mensagem com caracteres especiais
Se o nome da propriedade da mensagem tiver caracteres especiais, use aspas duplas `"` () para incluir o nome da propriedade. Por exemplo, se o nome da propriedade `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"` for, utilize a seguinte sintaxe no filtro. 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

## <a name="filter-on-message-properties-with-numeric-values"></a>Filtrar as propriedades da mensagem com valores numéricos
Os exemplos a seguir mostram como pode utilizar propriedades com valores numéricos em filtros. 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

## <a name="parameter-based-filters"></a>Filtros baseados em parâmetros
Aqui estão alguns exemplos de utilização de filtros baseados em parâmetros. Nestes exemplos, `DataTimeMp` é uma mensagem propriedade do tipo `DateTime` e é um parâmetro passado para o filtro como um `@dtParam` `DateTime` objeto.

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

## <a name="using-in-and-not-in"></a>Utilização de IN E NÃO IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Para obter uma amostra C#, consulte [a amostra de Filtros Tópicos no GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).


## <a name="correlation-filter-using-correlationid"></a>Filtro de correlação usando CorrelationID

```csharp
new CorrelationFilter("Contoso");
```

Filtra mensagens com `CorrelationID` definido para `Contoso` . 

## <a name="correlation-filter-using-system-and-user-properties"></a>Filtro de correlação usando sistema e propriedades do utilizador

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

É equivalente a: `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`

## <a name="next-steps"></a>Passos seguintes
Consulte as seguintes amostras: 

- [.NET - Envio básico e receber tutorial com filtros](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET - Filtros tópicos](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Modelo Azure Resource Manager](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)