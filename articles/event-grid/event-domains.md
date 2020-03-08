---
title: Domínios de eventos na grelha de eventos azure
description: Este artigo descreve como usar domínios de eventos para gerir o fluxo de eventos personalizados para as suas várias organizações empresariais, clientes ou aplicações.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: f6698f91d7659f9fc2c314a9291380301146f8ed
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898871"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Compreender os domínios do evento para gerir tópicos da Grelha de Eventos

Este artigo descreve como usar domínios de eventos para gerir o fluxo de eventos personalizados para as suas várias organizações empresariais, clientes ou aplicações. Utilize domínios de eventos para:

* Gerencie arquiteturas de eventos multiarrendatários em escala.
* Gerencie a sua autorização e autenticação.
* Partilhe os seus tópicos sem gerir individualmente cada um.
* Evite publicar individualmente em cada um dos seus pontos finais tópicos.

## <a name="event-domain-overview"></a>Visão geral do domínio do evento

Um domínio de evento é uma ferramenta de gestão para um grande número de tópicos da Grelha de Eventos relacionados com a mesma aplicação. Pode pensar nisso como um meta-tópico que pode ter milhares de tópicos individuais.

Os domínios do evento disponibilizam-lhe a mesma arquitetura utilizada pelos serviços Azure (como Storage e IoT Hub) para publicar os seus eventos. Permitem-lhe publicar eventos em milhares de tópicos. Os domínios também lhe dão autorização e controlo de autenticação sobre cada tópico para que possa dividir os seus inquilinos.

### <a name="example-use-case"></a>Caso de utilização de exemplo

Os domínios do evento são mais facilmente explicados usando um exemplo. Digamos que gere saquinaria de construção Contoso, onde fabrica tratores, equipamento de escavação e outras máquinas pesadas. Como parte da gestão do negócio, você empurra informações em tempo real para os clientes sobre manutenção de equipamentos, saúde de sistemas e atualizações de contratos. Todas estas informações vão para vários pontos finais, incluindo a sua app, pontos finais do cliente e outras infraestruturas que os clientes criaram.

Os domínios do evento permitem-lhe modelar a Máquina de Construção Contoso como uma única entidade de eventos. Cada um dos seus clientes é representado como um tópico dentro do domínio. A autenticação e a autorização são manuseadas utilizando o Diretório Ativo Azure. Cada um dos seus clientes pode subscrever o seu tópico e obter os seus eventos entregues a eles. O acesso gerido através do domínio do evento garante que só podem aceder ao seu tópico.

Também lhe dá um único ponto final, ao qual pode publicar todos os eventos do seu cliente. A Event Grid cuidará de garantir que cada tópico só está ciente dos eventos abordados ao seu inquilino.

![Exemplo de Construção Contoso](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Gestão de acesso

Com um domínio, obtém-se uma autorização de grão fino e controlo de autenticação sobre cada tópico através do controlo de acesso baseado em papéis do Azure (RBAC). Pode utilizar estas funções para restringir cada inquilino na sua candidatura apenas aos tópicos a que pretende conceder-lhes acesso.

O RBAC em domínios de eventos funciona da mesma forma que os trabalhos de [controlo de acesso geridos](security-authorization.md) no resto da Event Grid e Azure. Use o RBAC para criar e impor definições de papéis personalizadas em domínios de eventos.

### <a name="built-in-roles"></a>Construído em papéis

A Event Grid tem duas definições de função incorporadas para facilitar o RBAC ao trabalho com domínios de eventos. Estas funções são **EventGrid EventSubscription Contributor (Preview)** e **EventGrid EventGrid EventSubscription Reader (Pré-visualização)** . Atribui estas funções a utilizadores que necessitem de subscrever tópicos no domínio do seu evento. Você scopea a atribuição de funções apenas para o tópico que os utilizadores precisam subscrever.

Para obter informações sobre estas funções, consulte [as funções incorporadas para a Grelha de Eventos.](security-authorization.md#built-in-roles)

## <a name="subscribing-to-topics"></a>Subscrever tópicos

Subscrever eventos sobre um tema dentro de um domínio de evento é o mesmo que criar uma Subscrição de [Eventos sobre um tópico personalizado](./custom-event-quickstart.md) ou subscrever um evento a partir de um serviço Azure.

### <a name="domain-scope-subscriptions"></a>Assinaturas de âmbito de domínio

Os domínios do evento também permitem subscrições de âmbito de domínio. Uma subscrição de evento num domínio de evento receberá todos os eventos enviados para o domínio, independentemente do tópico para o que os eventos são enviados. As subscrições de âmbito de domínio podem ser úteis para fins de gestão e auditoria.

## <a name="publishing-to-an-event-domain"></a>Publicação para um domínio de evento

Quando crias um domínio de evento, recebes um ponto final de publicação semelhante ao que se tivessecriado um tópico em Event Grid. 

Para publicar eventos para qualquer tópico num Domínio de Evento, empurre os eventos para o ponto final do domínio da [mesma forma que faria para um tópico personalizado](./post-to-custom-topic.md). A única diferença é que deve especificar o tópico a que gostaria que o evento fosse entregue.

Por exemplo, a publicação do seguinte conjunto de eventos enviaria evento com `"id": "1111"` para tópico `foo` enquanto o evento com `"id": "2222"` seria enviado para tópico `bar`:

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

Os domínios do evento tratam da publicação de tópicos para si. Em vez de publicar eventos em cada tópico que gere individualmente, pode publicar todos os seus eventos no ponto final do domínio. A Grelha de Eventos garante que cada evento é enviado para o tópico correto.

## <a name="limits-and-quotas"></a>Limites e quotas
Aqui estão os limites e quotas relacionados com os domínios do evento:

- 100.000 tópicos por domínio de evento 
- 100 domínios de eventos por subscrição do Azure 
- 500 subscrições de eventos por tópico num domínio de evento
- 50 assinaturas de âmbito de domínio 
- 5\.000 eventos por segunda taxa de ingestão (num domínio)

Se estes limites não lhe servirem, contacte a equipa de produtos abrindo um bilhete de apoio ou enviando um e-mail para [askgrid@microsoft.com](mailto:askgrid@microsoft.com). 

## <a name="pricing"></a>Preços
Os domínios do evento utilizam os [mesmos preços](https://azure.microsoft.com/pricing/details/event-grid/) de operações que todas as outras funcionalidades na utilização da Rede de Eventos.

As operações funcionam da mesma forma em domínios de eventos como em tópicos personalizados. Cada ingresso de um evento para um domínio de evento é uma operação, e cada tentativa de entrega para um evento é uma operação.

## <a name="next-steps"></a>Passos seguintes

* Para aprender sobre a criação de domínios de eventos, criar tópicos, criar subscrições de eventos e publicar eventos, ver [Gerir domínios de eventos](./how-to-event-domains.md).
