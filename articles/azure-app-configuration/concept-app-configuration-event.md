---
title: Reagir a eventos de valor-chave de configuração de aplicações do Azure
description: Utilize a Grelha de Eventos Azure para subscrever eventos de configuração de aplicações.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: a4f61d147ba1abf73ada6360b8d0d965d8e063a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77523803"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reagir a eventos de configuração de aplicações do Azure

Os eventos de configuração de aplicações do Azure permitem que as aplicações reajam a alterações nos valores-chave. Isto é feito sem a necessidade de código complicado ou serviços de votação dispendiosos e ineficientes. Em vez disso, os eventos são empurrados através da Rede de [Eventos Azure](https://azure.microsoft.com/services/event-grid/) para assinantes como [Funções Azure,](https://azure.microsoft.com/services/functions/) [Aplicações Lógicas Azure,](https://azure.microsoft.com/services/logic-apps/)ou até mesmo para o seu próprio ouvinte personalizado http. Criticamente, só se paga pelo que se usa.

Os eventos de configuração de aplicações azure são enviados para a Rede de Eventos Azure que fornece serviços de entrega fiáveis às suas aplicações através de políticas ricas de retry e entrega de cartas mortas. Para saber mais, consulte a [entrega e a retentação da mensagem da Rede de Eventos.](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)

Os cenários comuns de configuração de aplicações incluem configuração de aplicação refrescante, implantação de implementações ou qualquer fluxo de trabalho orientado para a configuração. Quando as mudanças são pouco frequentes, mas o seu cenário requer capacidade de resposta imediata, a arquitetura baseada em eventos pode ser especialmente eficiente.

Veja os eventos de configuração da [Aplicação Route Azure para um ponto final personalizado - CLI](./howto-app-configuration-event.md) para um exemplo rápido. 

![Modelo de grelha de eventos](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Eventos de configuração de aplicações azure disponíveis
A grelha do evento utiliza subscrições de [eventos](../event-grid/concepts.md#event-subscriptions) para direcionar mensagens de eventos para assinantes. As subscrições de eventos de configuração de aplicações do Azure podem incluir dois tipos de eventos:  

> |Nome do Evento|Descrição|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Disparado quando um valor-chave é criado ou substituído|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Disparado quando um valor-chave é eliminado|

## <a name="event-schema"></a>Esquema de eventos
Os eventos de configuração de aplicações do Azure contêm todas as informações necessárias para responder a alterações nos seus dados. Pode identificar um evento de Configuração de Aplicações porque a propriedade do eventoType começa com "Microsoft.AppConfiguration". Informações adicionais sobre a utilização das propriedades do evento Grid estão documentadas no [event grid schema](../event-grid/event-schema.md).  

> |Propriedade|Tipo|Descrição|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |tópico|string|Full Azure Resource Manager id da Configuração da App que emite o evento.|
> |Assunto|string|O URI do valor-chave que é o tema do evento.|
> |eventTime|string|A data/hora que o evento foi gerado, no formato ISO 8601.|
> |eventType|string|"Microsoft.AppConfiguration.KeyValueModificado" ou "Microsoft.AppConfiguration.KeyValueDeleted".|
> |Id|string|Um identificador único deste evento.|
> |dataVersion|string|A versão do esquema do objeto de dados.|
> |metadataVersion|string|A versão de esquema sinuoso de propriedades de alto nível.|
> |data|objeto|Recolha de dados específicos de eventos de configuração de aplicações do Azure|
> |data.key|string|A chave do valor-chave que foi modificado ou eliminado.|
> |data.label|string|A etiqueta, se houver, do valor-chave que foi modificado ou eliminado.|
> |data.etag|string|Para `KeyValueModified` o etag do novo valor-chave. Para `KeyValueDeleted` o etag do valor-chave que foi eliminado.|

Aqui está um exemplo de um evento KeyValueModificado:
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

Para mais informações, consulte o esquema de eventos de configuração de [aplicações do Azure](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Práticas para consumir eventos
As aplicações que lidam com eventos de configuração de aplicações devem seguir estas práticas recomendadas:
> [!div class="checklist"]
> * Várias subscrições podem ser configuradas para direcionar eventos para o mesmo manipulador de eventos, por isso não assuma que os eventos são de uma determinada fonte. Em vez disso, verifique o tópico da mensagem para garantir que a instância de Configuração da Aplicação envia o evento.
> * Verifique o eventoType e não assuma que todos os eventos que receber serão os tipos que espera.
> * Utilize os campos de etag para entender se a sua informação sobre objetos ainda está atualizada.  
> * Utilize os campos de sequenciadores para entender a ordem dos acontecimentos em qualquer objeto em particular.
> * Utilize o campo de assunto para aceder ao valor-chave que foi modificado.


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a Grelha de Eventos e dê aos eventos de configuração de aplicações do Azure uma tentativa:

- [Sobre o Event Grid](../event-grid/overview.md)
- [Eventos de configuração de aplicações Route Azure para um ponto final da web personalizado](./howto-app-configuration-event.md)