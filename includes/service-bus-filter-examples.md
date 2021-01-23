---
title: incluir ficheiro
description: incluir ficheiro
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 01/22/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ddc0234accd9f434aad850d2404e2f3001c4bae
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742693"
---
## <a name="examples"></a>Exemplos

### <a name="filter-on-system-properties"></a>Filtrar as propriedades do sistema
Para se referir a uma propriedade do sistema num filtro, utilize o seguinte formato: `sys.<system-property-name>` . 

```csharp
sys.Label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

### <a name="filter-on-message-properties"></a>Filtrar nas propriedades da mensagem
Aqui estão os exemplos de utilização de propriedades de mensagens num filtro. Pode aceder às propriedades da mensagem utilizando `user.property-name` ou apenas `property-name` .

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

### <a name="filter-on-message-properties-with-special-characters"></a>Filtrar nas propriedades da mensagem com caracteres especiais
Se o nome da propriedade da mensagem tiver caracteres especiais, use aspas duplas `"` () para incluir o nome da propriedade. Por exemplo, se o nome da propriedade `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"` for, utilize a seguinte sintaxe no filtro. 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

### <a name="filter-on-message-properties-with-numeric-values"></a>Filtrar as propriedades da mensagem com valores numéricos
Os exemplos a seguir mostram como pode utilizar propriedades com valores numéricos em filtros. 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

### <a name="parameter-based-filters"></a>Filtros baseados em parâmetros
Aqui estão alguns exemplos de utilização de filtros baseados em parâmetros. Nestes exemplos, `DataTimeMp` é uma mensagem propriedade do tipo `DateTime` e é um parâmetro passado para o filtro como um `@dtParam` `DateTime` objeto.

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

### <a name="using-in-and-not-in"></a>Utilização de IN E NÃO IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Para obter uma amostra C#, consulte [a amostra de Filtros Tópicos no GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).


### <a name="set-rule-action-for-a-sql-filter"></a>Definir ação de regra para um filtro SQL

```csharp
// instantiate the ManagementClient
this.mgmtClient = new ManagementClient(connectionString);

// create the SQL filter
var sqlFilter = new SqlFilter("source = @stringParam");

// assign value for the parameter
sqlFilter.Parameters.Add("@stringParam", "orders");

// instantiate the Rule = Filter + Action
var filterActionRule = new RuleDescription
{
    Name = "filterActionRule",
    Filter = sqlFilter,
    Action = new SqlRuleAction("SET source='routedOrders'")
};

// create the rule on Service Bus
await this.mgmtClient.CreateRuleAsync(topicName, subscriptionName, filterActionRule);
```

### <a name="correlation-filter-using-correlationid"></a>Filtro de correlação usando CorrelationID

```csharp
new CorrelationFilter("Contoso");
```

Filtra mensagens com `CorrelationID` definido para `Contoso` . 

### <a name="correlation-filter-using-system-and-user-properties"></a>Filtro de correlação usando sistema e propriedades do utilizador

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

É equivalente a: `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`

