---
title: Domínios do evento na grelha de eventos Azure
description: Este artigo descreve como usar os domínios de eventos para gerir o fluxo de eventos personalizados para as suas várias organizações empresariais, clientes ou aplicações.
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 32c06ac55f667ec9807c7952127c2cf0f0384024
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374714"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Compreender os domínios do evento para gerir tópicos da Grelha de Eventos

Este artigo descreve como usar os domínios de eventos para gerir o fluxo de eventos personalizados para as suas várias organizações empresariais, clientes ou aplicações. Utilize domínios de eventos para:

* Gerir arquiteturas de eventos multitenantes em escala.
* Gerencie a sua autorização e autenticação.
* Dividir os seus tópicos sem gerir cada um individualmente.
* Evite publicar individualmente cada um dos seus pontos finais tópicos.

## <a name="event-domain-overview"></a>Visão geral do domínio do evento

Um domínio de evento é uma ferramenta de gestão para um grande número de tópicos de Grade de Eventos relacionados com a mesma aplicação. Pode pensar nisso como um meta-tema que pode ter milhares de tópicos individuais.

Os domínios de eventos fornecem-lhe a mesma arquitetura usada pelos serviços Azure, como o Storage e o IoT Hub, para publicar os seus eventos. Permitem-lhe publicar eventos para milhares de tópicos. Os domínios também lhe dão autorização e controlo de autenticação sobre cada tópico para que possa dividir os seus inquilinos.

## <a name="example-use-case"></a>Caso de uso de exemplo
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="access-management"></a>Gestão de acesso

Com um domínio, obtém-se uma autorização de grão fino e controlo de autenticação sobre cada tópico através do controlo de acesso baseado em funções Azure (Azure RBAC). Pode utilizar estas funções para restringir cada inquilino na sua candidatura apenas aos tópicos a que deseja conceder-lhes acesso.

O Azure RBAC em domínios de eventos funciona da mesma forma que funciona o controlo de [acesso gerido](security-authorization.md) no resto da Grade de Eventos e Azure. Use o Azure RBAC para criar e impor definições de funções personalizadas em domínios de eventos.

### <a name="built-in-roles"></a>Construído em papéis

Event Grid tem duas definições de papel incorporadas para tornar o Azure RBAC mais fácil para trabalhar com domínios de eventos. Estes papéis são **EventGrid EventSubscription Contributor (Preview)** e **EventGrid EventSubscription Reader (Preview)**. Atribui estas funções a utilizadores que necessitem de subscrever tópicos no domínio do evento. Você traça a atribuição de papel apenas para o tópico que os utilizadores precisam subscrever.

Para obter informações sobre estas funções, consulte [as funções incorporadas para a Grade de Eventos.](security-authorization.md#built-in-roles)

## <a name="subscribing-to-topics"></a>Subscrever tópicos

Subscrever eventos sobre um tópico dentro de um domínio de evento é o mesmo que [criar uma Subscrição de Eventos num tópico personalizado](./custom-event-quickstart.md) ou subscrever um evento a partir de um serviço Azure.

> [!IMPORTANT]
> O tópico de domínio é considerado um recurso **gerido automaticamente** na Grade de Eventos. Pode criar uma subscrição de eventos no âmbito do tópico de domínio sem criar o tópico de domínio. Neste caso, o Event Grid cria automaticamente o tópico de domínio em seu nome. Claro que ainda pode optar por criar o tópico de domínio manualmente. Este comportamento permite-lhe preocupar-se com um recurso a menos quando lida com um grande número de tópicos de domínio. Quando a última subscrição de um tópico de domínio é eliminada, o tópico de domínio também é eliminado independentemente de o tópico de domínio ter sido criado manualmente ou criado automaticamente. 

### <a name="domain-scope-subscriptions"></a>Subscrições de âmbito de domínio

Os domínios do evento também permitem subscrições de âmbito de domínio. Uma subscrição de eventos num domínio de evento receberá todos os eventos enviados para o domínio, independentemente do tópico para o quais os eventos sejam enviados. As subscrições de âmbito de domínio podem ser úteis para fins de gestão e auditoria.

## <a name="publishing-to-an-event-domain"></a>Publicação para um domínio de eventos

Quando crias um domínio de evento, recebes um ponto final de publicação semelhante ao que criaste um tópico na Grade de Eventos. 

Para publicar eventos para qualquer tópico num Domínio de Eventos, empurre os eventos para o ponto final do domínio da [mesma forma que faria para um tópico personalizado.](./post-to-custom-topic.md) A única diferença é que deve especificar o tópico a que gostaria que o evento fosse entregue.

Por exemplo, a publicação da seguinte variedade de eventos enviaria o evento com `"id": "1111"` o tema `foo` enquanto o evento com o tema `"id": "2222"` seria enviado para o `bar` tópico:

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

Os domínios do evento lidam com a publicação de tópicos para si. Em vez de publicar eventos para cada tópico que gere individualmente, pode publicar todos os seus eventos no ponto final do domínio. A Grade de Eventos garante que cada evento é enviado para o tópico correto.

## <a name="limits-and-quotas"></a>Limites e quotas
Aqui estão os limites e quotas relacionadas com os domínios de eventos:

- 100.000 tópicos por domínio de evento 
- 100 domínios de eventos por subscrição do Azure 
- 500 subscrições de eventos por tópico num domínio de eventos
- 50 subscrições de âmbito de domínio 
- 5.000 eventos por segunda taxa de ingestão (em um domínio)

Se estes limites não lhe convêm, abra um bilhete de apoio ou envie um e-mail para [askgrid@microsoft.com](mailto:askgrid@microsoft.com) . 

## <a name="pricing"></a>Preços
Os domínios do evento utilizam os [mesmos preços de operações](https://azure.microsoft.com/pricing/details/event-grid/) que todas as outras funcionalidades na utilização da Grade de Eventos.

As operações funcionam da mesma forma em domínios de eventos como em tópicos personalizados. Cada entrada de um evento para um domínio de evento é uma operação, e cada tentativa de entrega de um evento é uma operação.



## <a name="next-steps"></a>Passos seguintes

* Para aprender a configurar domínios de eventos, criar tópicos, criar subscrições de eventos e publicar eventos, consulte [gerir os domínios de eventos.](./how-to-event-domains.md)
