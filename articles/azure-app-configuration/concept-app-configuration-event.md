---
title: Reagir a eventos de chave-valor de configuração de aplicações do Azure | Documentos da Microsoft
description: Utilize o Azure Event Grid para subscrever a eventos de configuração de aplicações.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 05/30/2019
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 601124aef37d2b285db71130f5c63b3620c7768f
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735650"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reagir a eventos de configuração de aplicações do Azure

Eventos de configuração de aplicações do Azure permitem aplicações reagir a alterações nos valores de chave. Isso é feito sem a necessidade de código complicado ou serviços de consulta dispendiosa e ineficiente. Em vez disso, os eventos são enviados por meio [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) para os assinantes, tal como [as funções do Azure](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), ou até mesmo para o seu próprio serviço de escuta de http personalizado e apenas pague apenas aquilo que utiliza.

Eventos de configuração de aplicações do Azure são enviados para o Azure Event Grid, que fornece serviços de entrega fiável às suas aplicações através de rich Repita políticas e entrega de mensagens não entregues. Para obter mais informações, consulte [entrega de mensagens do Event Grid e volte a tentar](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

Cenários de eventos de configuração de aplicações comuns incluem a atualizar a configuração da aplicação, acionamento de implementações, ou qualquer fluxo de trabalho e orientados a configuração. Quando as alterações são pouco frequentes, mas o seu cenário requer a capacidade de resposta imediata, com base em eventos arquitetura pode ser especialmente eficiente.

Dê uma olhada [ponto final - CLI de web de eventos de configuração de aplicações do Azure de rota para um personalizado](./howto-app-configuration-event.md) um exemplo rápido. 

![Modelo de grade do evento](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Eventos de configuração de aplicações do Azure disponíveis
Grelha de eventos usa [subscrições de eventos](../event-grid/concepts.md#event-subscriptions) encaminhar mensagens de eventos para os assinantes. Subscrições de eventos de configuração de aplicações do Azure podem incluir dois tipos de eventos:  

> |Nome do evento|Descrição|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Acionado quando um valor de chave é criado ou substituído|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Acionado quando é eliminado um valor de chave|

## <a name="event-schema"></a>Esquema de eventos
Eventos de configuração de aplicações do Azure contêm todas as informações que necessárias para responder a alterações nos seus dados. É possível identificar um evento de configuração de aplicação porque a propriedade eventType começa com "Microsoft.AppConfiguration". Informações adicionais sobre a utilização das propriedades de eventos do Event Grid estão documentadas no [esquema de eventos do Event Grid](../event-grid/event-schema.md).  

> |Propriedade|Tipo|Descrição|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |topic|string|Id do Azure Resource Manager completo da configuração da aplicação que emite o evento.|
> |subject|string|O URI de chave-valor que é o assunto do evento.|
> |eventTime|string|A data/hora que o evento foi gerado, no formato ISO 8601.|
> |eventType|string|"Microsoft.AppConfiguration.KeyValueModified" ou "Microsoft.AppConfiguration.KeyValueDeleted".|
> |Id|string|Um identificador exclusivo deste evento.|
> |dataVersion|string|A versão do esquema do objeto de dados.|
> |metadataVersion|string|A versão do esquema das propriedades de nível superior.|
> |data|objeto|Recolha de dados de evento específico de configuração de aplicações do Azure|
> |data.key|string|A chave de chave-valor que foi alterada ou eliminada.|
> |data.label|string|A etiqueta, caso haja algum, de chave-valor que foi alterado ou eliminado.|
> |data.etag|string|Para `KeyValueModified` a etag do novo valor de chave. Para `KeyValueDeleted` a etag de chave-valor que foi eliminada.|

Eis um exemplo de um evento de KeyValueModified:
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

Para obter mais informações, consulte [esquema de eventos de configuração de aplicações do Azure](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Práticas recomendadas para o consumo de eventos
Aplicações que processam os eventos de configuração de aplicação devem seguir algumas práticas recomendadas:
> [!div class="checklist"]
> * Como várias subscrições podem ser configuradas para encaminhar eventos para o mesmo manipulador de eventos, é importante não partem do princípio de eventos são de uma origem específica, mas o tópico da mensagem para se certificar de que trata da configuração de aplicação que estiver à espera de verificação.
> * Da mesma forma, verifique se o eventType é um estão preparados para o processo e não partem do princípio de que todos os eventos recebidos serão os tipos esperados.
> * Como as mensagens podem chegar fora de ordem e, depois de algum atraso, utilize os campos de etag para saber se as informações sobre objetos são ainda atualizadas.  Além disso, utilize os campos do sequenciador para compreender a ordem dos eventos em qualquer objeto específico.
> * Utilize o campo do requerente para aceder o chave-valor que foi modificado.


## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o Event Grid e dê uma chance de eventos de configuração de aplicações do Azure:

- [Sobre o Event Grid](../event-grid/overview.md)
- [Encaminhar eventos de configuração de aplicações do Azure para um ponto final web personalizado](./howto-app-configuration-event.md)