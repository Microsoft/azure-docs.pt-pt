---
title: Domínios de evento na grade de eventos do Azure
description: Este artigo descreve como usar domínios de evento para gerenciar o fluxo de eventos personalizados para suas várias organizações de negócios, clientes ou aplicativos.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: df560df21740d5396bc177e20de5d0eb4bf47713
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76511387"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Entender os domínios de evento para gerenciar tópicos da grade de eventos

Este artigo descreve como usar domínios de evento para gerenciar o fluxo de eventos personalizados para suas várias organizações de negócios, clientes ou aplicativos. Use domínios de evento para:

* Gerencie arquiteturas de eventos de multilocatário em escala.
* Gerencie sua autorização e autenticação.
* Particione seus tópicos sem gerenciar cada um individualmente.
* Evite publicar individualmente em cada um dos seus pontos de extremidade de tópico.

## <a name="event-domain-overview"></a>Visão geral do domínio de evento

Um domínio de evento é uma ferramenta de gerenciamento para grandes números de tópicos de grade de eventos relacionados ao mesmo aplicativo. Você pode considerar isso como um meta-tópico que pode ter milhares de tópicos individuais.

Os domínios de evento tornam-se disponíveis para você a mesma arquitetura usada pelos serviços do Azure (como o armazenamento e o Hub IoT) para publicar seus eventos. Eles permitem que você publique eventos em milhares de tópicos. Os domínios também oferecem autorização e controle de autenticação sobre cada tópico para que você possa particionar seus locatários.

### <a name="example-use-case"></a>Caso de uso de exemplo

Os domínios de evento são explicados com mais facilidade usando um exemplo. Digamos que você execute máquinas de construção da Contoso, em que você fabrica os tratores, a utilização de equipamentos e outras máquinas pesadas. Como parte da execução dos negócios, você envia informações em tempo real para clientes sobre manutenção de equipamentos, integridade de sistemas e atualizações de contrato. Todas essas informações vão para vários pontos de extremidade, incluindo seu aplicativo, os pontos de extremidade do cliente e outras infra-estruturas que os clientes configuram.

Os domínios de evento permitem modelar máquinas de construção da Contoso como uma única entidade de eventos. Cada um de seus clientes é representado como um tópico dentro do domínio. A autenticação e a autorização são manipuladas usando Azure Active Directory. Cada um de seus clientes pode assinar seu tópico e obter seus eventos entregues a eles. O acesso gerenciado por meio do domínio de evento garante que eles só possam acessar seus tópicos.

Ele também fornece um único ponto de extremidade, no qual você pode publicar todos os seus eventos de cliente. A grade de eventos cuidará da garantia de que cada tópico está ciente apenas dos eventos com escopo definido para seu locatário.

![Exemplo de construção da contoso](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Gestão de acesso

Com um domínio, você obtém autorização e controle de autenticação refinados sobre cada tópico por meio do RBAC (controle de acesso baseado em função) do Azure. Você pode usar essas funções para restringir cada locatário em seu aplicativo somente aos tópicos aos quais você deseja conceder acesso.

O RBAC em domínios de evento funciona da mesma forma que o [controle de acesso gerenciado](security-authentication.md#management-access-control) funciona no restante da grade de eventos e no Azure. Use o RBAC para criar e impor definições de função personalizadas em domínios de evento.

### <a name="built-in-roles"></a>Funções internas

A grade de eventos tem duas definições de função internas para tornar o RBAC mais fácil para trabalhar com domínios de evento. Essas funções são **EventGrid EventSubscription Contributor (visualização)** e **EventGrid EventSubscription Reader (versão prévia)** . Atribua essas funções a usuários que precisam assinar tópicos em seu domínio de evento. Você faz o escopo da atribuição de função apenas para o tópico que os usuários precisam assinar.

Para obter informações sobre essas funções, consulte [funções internas para a grade de eventos](security-authentication.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Assinando tópicos

Assinar eventos em um tópico dentro de um domínio de evento é o mesmo que [criar uma assinatura de evento em um tópico personalizado](./custom-event-quickstart.md) ou inscrever-se em um evento de um serviço do Azure.

### <a name="domain-scope-subscriptions"></a>Assinaturas de escopo de domínio

Os domínios de evento também permitem assinaturas de escopo de domínio. Uma assinatura de evento em um domínio de evento receberá todos os eventos enviados ao domínio, independentemente do tópico para o qual os eventos são enviados. As assinaturas de escopo de domínio podem ser úteis para fins de gerenciamento e auditoria.

## <a name="publishing-to-an-event-domain"></a>Publicando em um domínio de evento

Ao criar um domínio de evento, você recebe um ponto de extremidade de publicação semelhante a se você tivesse criado um tópico na grade de eventos. 

Para publicar eventos em qualquer tópico em um domínio de evento, envie por push os eventos para o ponto de extremidade do domínio da [mesma maneira que você faria para um tópico personalizado](./post-to-custom-topic.md). A única diferença é que você deve especificar o tópico para o qual deseja que o evento seja entregue.

Por exemplo, a publicação da seguinte matriz de eventos enviará um evento com `"id": "1111"` ao tópico `foo` enquanto o evento com `"id": "2222"` seria enviado para o tópico `bar`:

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

Os domínios de evento lidam com a publicação para tópicos para você. Em vez de publicar eventos para cada tópico que você gerencia individualmente, você pode publicar todos os seus eventos no ponto de extremidade do domínio. A grade de eventos garante que cada evento seja enviado para o tópico correto.

## <a name="limits-and-quotas"></a>Limites e quotas
Aqui estão os limites e as cotas relacionados aos domínios de evento:

- 100.000 tópicos por domínio de evento 
- 100 domínios de evento por assinatura do Azure 
- 500 subscrições de eventos por tópico num domínio de eventos
- 50 assinaturas de escopo de domínio 
- taxa de ingestão de eventos por segundo do 5.000 (em um domínio)

Se esses limites não forem adequados a você, acesse a equipe do produto abrindo um tíquete de suporte ou enviando um email para [askgrid@microsoft.com](mailto:askgrid@microsoft.com). 

## <a name="pricing"></a>Preços
Os domínios de evento usam o mesmo [preço de operações](https://azure.microsoft.com/pricing/details/event-grid/) que todos os outros recursos na grade de eventos usam.

As operações funcionam da mesma forma em domínios de evento como em tópicos personalizados. Cada entrada de um evento em um domínio de evento é uma operação e cada tentativa de entrega para um evento é uma operação.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre como configurar domínios de evento, criar tópicos, criar assinaturas de evento e publicar eventos, consulte [gerenciar domínios de evento](./how-to-event-domains.md).
