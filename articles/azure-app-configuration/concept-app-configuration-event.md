---
title: Reagir a eventos de valor-chave de configuração de aplicações Azure
description: Utilize a Grelha de Eventos Azure para subscrever eventos de Configuração de Aplicações, que permitem que as aplicações reajam a alterações nos valores-chave sem a necessidade de um código complicado.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 12a62bbd06cf9adf3b5978bb061e1d014599b44c
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550747"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reagir a eventos de configuração de aplicativos Azure

Os eventos de Configuração de Aplicações Azure permitem que as aplicações reajam a alterações nos valores-chave. Isto é feito sem a necessidade de um código complicado ou de serviços de sondagens dispendiosos e ineficientes. Em vez disso, os eventos são empurrados através [da Azure Event Grid](https://azure.microsoft.com/services/event-grid/) para assinantes como [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), ou até mesmo para o seu próprio ouvinte personalizado. Criticamente, só se paga pelo que se usa.

Os eventos de Configuração de Aplicações Azure são enviados para a Grelha de Eventos Azure, que fornece serviços de entrega confiáveis às suas aplicações através de políticas de repreensão ricas e entrega de cartas mortas. Para saber mais, consulte a [entrega de mensagens de Event Grid e retentou.](../event-grid/delivery-and-retry.md)

Os cenários comuns de configuração da configuração da aplicação incluem configuração de aplicação refrescante, implementações de desencadeamento ou qualquer fluxo de trabalho orientado para a configuração. Quando as mudanças são pouco frequentes, mas o seu cenário requer capacidade de resposta imediata, a arquitetura baseada em eventos pode ser especialmente eficiente.

Veja a [Grelha de Eventos de Utilização para notificações de alteração de dados](./howto-app-configuration-event.md) para um exemplo rápido. 

![Modelo de grelha de evento](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Eventos disponíveis de configuração de aplicativos Azure
A grelha de eventos utiliza [subscrições de eventos](../event-grid/concepts.md#event-subscriptions) para encaminhar mensagens de evento para assinantes. As subscrições de eventos de configuração de aplicativos Azure podem incluir dois tipos de eventos:  

> |Nome do Evento|Description|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Disparado quando um valor-chave é criado ou substituído|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Disparado quando um valor-chave é eliminado|

## <a name="event-schema"></a>Esquema de eventos
Os eventos de Configuração de Aplicações Azure contêm todas as informações necessárias para responder às alterações nos seus dados. Pode identificar um evento de Configuração de Aplicações porque a propriedade eventType começa com "Microsoft.AppConfiguration". Informações adicionais sobre a utilização das propriedades do evento Event Grid estão documentadas no [esquema de eventos da Grade de Eventos.](../event-grid/event-schema.md)  

> |Propriedade|Tipo|Description|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |tópico|string|Full Azure Resource Manager id da Configuração de Aplicação que emite o evento.|
> |subject|string|O URI do valor-chave que é o tema do evento.|
> |eventTime|string|A data/hora em que o evento foi gerado, no formato ISO 8601.|
> |eventType|string|"Microsoft.AppConfiguration.KeyValueModified" ou "Microsoft.AppConfiguration.KeyValueDeleted".|
> |Id|string|Um identificador único deste evento.|
> |dataVersion|string|A versão do esquema do objeto de dados.|
> |metadadosVersão|string|A versão de esquema de propriedades de alto nível.|
> |dados|objeto|Recolha de dados específicos de configuração de aplicativos Azure|
> |dados.key|string|A chave do valor-chave que foi modificada ou eliminada.|
> |data.label|string|A etiqueta, se houver, do valor-chave que foi modificada ou eliminada.|
> |data.etag|string|Para `KeyValueModified` o etag do novo valor-chave. Para `KeyValueDeleted` o etag do valor-chave que foi eliminado.|

Aqui está um exemplo de um evento KeyValueModified:
```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]

```

Para obter mais informações, consulte [o esquema de eventos de configuração da aplicação Azure.](../event-grid/event-schema-app-configuration.md)

## <a name="practices-for-consuming-events"></a>Práticas para consumir eventos
As aplicações que lidam com eventos de Configuração de Aplicações devem seguir estas práticas recomendadas:
> [!div class="checklist"]
> * Várias subscrições podem ser configuradas para encaminhar eventos para o mesmo manipulador de eventos, por isso não assuma que os eventos são de uma determinada fonte. Em vez disso, verifique o tópico da mensagem para garantir que a instância de Configuração da Aplicação envia o evento.
> * Verifique o eventType e não assuma que todos os eventos que receber serão os tipos que espera.
> * Utilize os campos etag para entender se as suas informações sobre objetos ainda estão atualizadas.  
> * Use os campos de sequenciadores para entender a ordem dos eventos em qualquer objeto em particular.
> * Utilize o campo sujeito para aceder ao valor-chave que foi modificado.


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a Grelha de Eventos e dê à Azure App Configuration eventos uma tentativa:

- [Sobre o Event Grid](../event-grid/overview.md)
- [Como utilizar a Grelha de Eventos para notificações de alteração de dados](./howto-app-configuration-event.md)