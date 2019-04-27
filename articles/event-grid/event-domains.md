---
title: Domínios de evento na grelha de eventos do Azure
description: Descreve como os domínios de eventos são utilizados para gerir os tópicos do Azure Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 131a55d130e7ebf619ee283e943c0b0a7b45edfd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60562037"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Compreender os domínios de eventos para o gerenciamento de tópicos do Event Grid

Este artigo descreve como utilizar domínios de eventos para gerenciar o fluxo de eventos personalizados para várias organizações comerciais, os clientes ou aplicações. Domínios de eventos de utilização para:

* Faça a gestão de arquiteturas de multi-inquilino de eventos à escala.
* Gerir a sua autorização e autenticação.
* Particione os tópicos sem ter de gerir cada um individualmente.
* Evite publicar individualmente para cada um dos seus pontos de extremidade do tópico.

Esta funcionalidade está em pré-visualização. Para usá-lo, tem de instalar uma extensão de pré-visualização ou um módulo. Para obter instruções, consulte [tópicos de gerir e publicar eventos através de domínios de evento](how-to-event-domains.md).

## <a name="event-domain-overview"></a>Descrição geral do domínio de eventos

Um domínio de eventos é uma ferramenta de gestão para um grande número de tópicos do Event Grid relacionadas com a mesma aplicação. Pode pensá-lo como um meta-tópico que pode ter milhares de tópicos individuais.

Domínios de evento tornam disponível para a mesma arquitetura utilizada pelos serviços do Azure (como o armazenamento e o IoT Hub) para publicar seus eventos. Eles permitem que publicar eventos para milhares de tópicos. Domínios também dão-lhe controlo de autorização e autenticação sobre cada tópico para que pode dividir os seus inquilinos.

### <a name="example-use-case"></a>Caso de utilização de exemplo

Domínios de eventos mais facilmente são explicados com um exemplo. Digamos que executar o mecanismo de construção da Contoso, em que fabrica tractors, compreendendo o equipamento e outra maquinaria pesada. Como parte da execução da empresa, enviar informações em tempo real para os clientes sobre manutenção de equipamento, estado de funcionamento de sistemas e atualizações de contrato. Todas essas informações direciona-o para vários pontos de extremidade incluindo a sua aplicação, pontos de extremidade do cliente e outras infraestruturas de que os clientes configurados.

Domínios de eventos permitem o modelo de mecanismo de construção da Contoso como uma entidade de eventos único. Cada um dos seus clientes é representada como um tópico no domínio. Autenticação e autorização são processadas com o Azure Active Directory. Cada um dos seus clientes pode subscrever o tópico e obter os seus eventos fornecidos às mesmas. Acesso gerido através do domínio de eventos garante que só possam aceder ao respetivo tópico.

Ele também fornece um único ponto de extremidade, o que poderá publicar todos os eventos de cliente para. Grelha de eventos se encarregará de certificar-se de que cada tópico apenas tem conhecimento de eventos no âmbito do seu inquilino.

![Exemplo de construção de contoso](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Gestão de acesso

Com um domínio, tem de controlo de autorização e autenticação de refinado sobre cada tópico através do controlo de acesso baseado em funções do Azure (RBAC). Pode utilizar estas funções para restringir a cada inquilino na sua aplicação para apenas os tópicos que pretende conceder-lhes acesso a.

RBAC em domínios de evento funciona da mesma maneira [geridos pelo controlo de acesso](security-authentication.md#management-access-control) funciona no resto do Event Grid e o Azure. Utilize o RBAC para criar e impor definições de funções personalizadas em domínios de evento.

### <a name="built-in-roles"></a>Funções incorporadas

Grelha de eventos tem duas definições de função incorporada para facilitar o RBAC para trabalhar com domínios de evento. Estas funções são **EventGrid EventSubscription contribuinte (pré-visualização)** e **EventGrid EventSubscription leitor (pré-visualização)**. Atribuir essas funções a utilizadores que precisam de subscrever tópicos no seu domínio de evento. Definir o âmbito de atribuição de função para apenas o que os utilizadores tem de subscrever o tópico.

Para obter informações sobre estas funções, consulte [funções incorporadas do Event Grid](security-authentication.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Subscrição de tópicos

Subscrever a eventos num tópico dentro de um domínio de evento é igual a [criar uma subscrição de evento num tópico personalizado](./custom-event-quickstart.md) ou assinar um evento de um serviço do Azure.

### <a name="domain-scope-subscriptions"></a>Subscrições de âmbito de domínio

Domínios de evento também permitem subscrições de âmbito de domínio. Uma subscrição de evento num domínio de eventos irá receber todos os eventos enviados para o domínio, independentemente do tópico que os eventos são enviados para. Subscrições de âmbito de domínio podem ser úteis para gestão e fins de auditoria.

## <a name="publishing-to-an-event-domain"></a>Publicação de um domínio de eventos

Quando cria um domínio de evento, é-lhe fornecido um ponto final de publicação semelhante se tivesse criado um tópico no Event Grid. 

Para publicar eventos para nenhum tópico num domínio de evento, enviar os eventos para o ponto de extremidade do domínio a [mesma forma que faria para um tópico personalizado](./post-to-custom-topic.md). A única diferença é que tem de especificar o tópico que gostaria de ter o evento seja entregue ao.

Por exemplo, a matriz a seguir de eventos de publicação seria enviar eventos com `"id": "1111"` para o tópico `foo` enquanto o evento com `"id": "2222"` seriam enviados para o tópico `bar`:

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

Domínios de evento lidar com a publicação para tópicos para. Em vez de eventos de publicação para cada tópico que gerir individualmente, pode publicar todos os eventos para o ponto de extremidade do domínio. Grelha de eventos torna-se de que cada evento é enviado para o tópico correto.

## <a name="limits-and-quotas"></a>Limites e quotas

### <a name="control-plane"></a>Plano de controlo

Durante a pré-visualização, os domínios de evento estão limitados a 1000 tópicos dentro de um domínio e 50 subscrições de eventos por tópico dentro de um domínio. Subscrições de âmbito de domínio de eventos também estão limitadas a 50.

### <a name="data-plane"></a>Plano de dados

Durante a pré-visualização, o débito de eventos para um domínio de eventos será limitado para os mesmos 5.000 eventos por segundo velocidade de ingestão que tópicos personalizados estão limitados a.

## <a name="pricing"></a>Preços

Durante a pré-visualização, os domínios de eventos usam os mesmos [preços de operações](https://azure.microsoft.com/pricing/details/event-grid/) que utilizam todos os outros recursos no Event Grid.

Operações funcionam da mesma em domínios de evento tal como nos tópicos personalizados. Cada entrada de um evento a um domínio de eventos é uma operação e cada tentativa de entrega de um evento é uma operação.

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre como configurar domínios de eventos, criar tópicos, subscrições de eventos de criação e publicação de eventos, consulte [gerir domínios de evento](./how-to-event-domains.md).