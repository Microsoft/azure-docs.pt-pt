---
title: Reagindo a Azure App eventos de valor de chave de configuração | Microsoft Docs
description: Use a grade de eventos do Azure para assinar eventos de configuração de aplicativo.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 05/30/2019
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 5da64155f2823712eee7a60427b1c1e80abec068
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185288"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reagindo a Azure App eventos de configuração

Azure App eventos de configuração permitem que os aplicativos reajam às alterações nos valores de chave. Isso é feito sem a necessidade de código complicado ou serviços de sondagem caros e ineficientes. Em vez disso, os eventos são enviados por Push por meio da [grade de eventos do Azure](https://azure.microsoft.com/services/event-grid/) para assinantes como [Azure Functions](https://azure.microsoft.com/services/functions/), [aplicativos lógicos do Azure](https://azure.microsoft.com/services/logic-apps/)ou até mesmo para seu próprio ouvinte HTTP personalizado e você paga apenas pelo que usar.

Azure App eventos de configuração são enviados para a grade de eventos do Azure, que fornece serviços de entrega confiáveis para seus aplicativos por meio de políticas de repetição avançadas e entrega de mensagens mortas. Para saber mais, confira [entrega e repetição de mensagem da grade de eventos](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

Os cenários de evento de configuração de aplicativo comuns incluem a atualização da configuração do aplicativo, o disparo de implantações ou qualquer fluxo de trabalho orientado à configuração. Quando as alterações não são frequentes, mas seu cenário requer capacidade de resposta imediata, a arquitetura baseada em eventos pode ser especialmente eficiente.

Dê uma olhada na [rota Azure app eventos de configuração para um ponto de extremidade da Web personalizado-CLI](./howto-app-configuration-event.md) para obter um exemplo rápido. 

![Modelo de grade de eventos](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Azure App eventos de configuração disponíveis
A grade de eventos usa [assinaturas de evento](../event-grid/concepts.md#event-subscriptions) para rotear mensagens de evento para assinantes. Azure App as assinaturas de evento de configuração podem incluir dois tipos de eventos:  

> |Nome do evento|Descrição|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Acionado quando um valor de chave é criado ou substituído|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Acionado quando um valor de chave é excluído|

## <a name="event-schema"></a>Esquema de eventos
Azure App eventos de configuração contêm todas as informações de que você precisa para responder às alterações em seus dados. Você pode identificar um evento de configuração de aplicativo porque a propriedade eventType começa com "Microsoft. AppConfiguration". Informações adicionais sobre o uso de propriedades de evento da grade de eventos estão documentadas no [esquema de eventos da grade de eventos](../event-grid/event-schema.md).  

> |Propriedade|Tipo|Descrição|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |tópico|string|ID de Azure Resource Manager completo da configuração do aplicativo que emite o evento.|
> |subject|string|O URI do valor de chave que é o assunto do evento.|
> |eventTime|string|A data/hora em que o evento foi gerado, no formato ISO 8601.|
> |eventType|string|"Microsoft. AppConfiguration. KeyValueModified" ou "Microsoft. AppConfiguration. KeyValueDeleted".|
> |Id|string|Um identificador exclusivo deste evento.|
> |dataVersion|string|A versão do esquema do objeto de dados.|
> |metadataVersion|string|A versão do esquema das propriedades de nível superior.|
> |data|objeto|Coleção de dados de eventos específicos de configuração de Azure App|
> |Data. Key|string|A chave do valor de chave que foi modificado ou excluído.|
> |data.label|string|O rótulo, se houver, do valor de chave que foi modificado ou excluído.|
> |data.etag|string|Por `KeyValueModified` a eTag do novo valor de chave. Por `KeyValueDeleted` a eTag do valor-chave que foi excluído.|

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

Para obter mais informações, consulte [Azure app esquema de eventos de configuração](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Práticas para consumo de eventos
Os aplicativos que lidam com eventos de configuração de aplicativo devem seguir algumas práticas recomendadas:
> [!div class="checklist"]
> * Como várias assinaturas podem ser configuradas para rotear eventos para o mesmo manipulador de eventos, é importante não supor que os eventos sejam de uma fonte específica, mas para verificar o tópico da mensagem para garantir que ele venha da configuração do aplicativo que você espera.
> * Da mesma forma, verifique se o eventType é um que você está preparado para processar e não presuma que todos os eventos recebidos serão os tipos esperados.
> * Como as mensagens podem chegar fora de ordem e depois de algum atraso, use os campos ETag para entender se suas informações sobre objetos ainda estão atualizadas.  Além disso, use os campos do Sequencer para entender a ordem dos eventos em qualquer objeto específico.
> * Use o campo assunto para acessar a chave-valor que foi modificado.


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a grade de eventos e dê uma tentativa de Azure App eventos de configuração:

- [Sobre o Event Grid](../event-grid/overview.md)
- [Rotear eventos de configuração de Azure App para um ponto de extremidade da Web personalizado](./howto-app-configuration-event.md)