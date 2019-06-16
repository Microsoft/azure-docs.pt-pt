---
title: Conceitos do Azure Event Grid
description: Descreve o Azure Event Grid e respetivos conceitos. Define vários componentes essenciais do Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: spelluru
ms.openlocfilehash: 0821c749a6cb718e1b8abb74a2925bc041850eaf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66305263"
---
# <a name="concepts-in-azure-event-grid"></a>Conceitos no Azure Event Grid

Este artigo descreve os principais conceitos no Azure Event Grid.

## <a name="events"></a>Events

Um evento é a menor quantidade de informações que descreve totalmente algo aconteceram no sistema. Cada evento tem informações comuns, como: origem do evento, o tempo que demorou o evento local e o identificador exclusivo. Cada evento também tem informações específicas que só são relevantes para o tipo específico de evento. Por exemplo, um evento sobre um novo ficheiro a ser criado no armazenamento do Azure inclui detalhes sobre o ficheiro, tal como o `lastTimeModified` valor. Em alternativa, um evento de Hubs de eventos tem o URL do arquivo de captura. 

Um evento de tamanho até 64 KB é abordado pelo contrato de nível de serviço do disponibilidade geral (GA) (SLA). O suporte para um evento de tamanho até 1 MB está atualmente em pré-visualização. Eventos mais de 64 KB são cobrados em incrementos de 64 KB. 


Para obter as propriedades que são enviadas num evento, consulte [esquema de eventos do Azure Event Grid](event-schema.md).

## <a name="publishers"></a>Editores

Um publicador é o utilizador ou a organização que decide para enviar eventos para o Event Grid. A Microsoft publica os eventos para vários serviços do Azure. Pode publicar eventos de seu próprio aplicativo. As organizações que alojam serviços fora do Azure podem publicar eventos através do Event Grid.

## <a name="event-sources"></a>Origens de eventos

Uma origem de evento é onde acontece o evento. Cada origem de evento está associada a um ou mais tipos de evento. Por exemplo, o armazenamento do Azure é a origem do evento para o blob criado eventos. O IoT Hub é a origem do evento para o dispositivo que criou a eventos. Seu aplicativo é a fonte de eventos para eventos personalizados que definir. Origens de eventos são responsáveis pelo envio de eventos para o Event Grid.

Para obter informações sobre a implementação de qualquer uma das origens de Event Grid suportadas, consulte [origens de eventos no Azure Event Grid](event-sources.md).

## <a name="topics"></a>Tópicos

O tópico do event grid fornece um ponto de extremidade em que a origem envia eventos. O publicador cria o tópico do event grid e decide se uma origem de evento precisa de um tópico ou mais de um tópico. Um tópico é utilizado para uma coleção de eventos relacionados. Para responder a determinados tipos de eventos, os assinantes decidir quais tópicos para subscrever.

Tópicos de sistema são tópicos de incorporada fornecidos pelos serviços do Azure. Não vê a tópicos de sistema na sua subscrição do Azure, porque o publicador detém os tópicos, mas pode subscrevê-los. Para subscrever, fornecem informações sobre o recurso que pretende receber eventos a partir de. Desde que tenham acesso ao recurso, pode subscrever para seus eventos.

Tópicos personalizados são tópicos de terceiros e de aplicação. Quando criar ou recebem acesso a um tópico personalizado, verá esse tópico personalizado na sua subscrição.

Ao conceber a sua aplicação, tem flexibilidade ao decidir como vários tópicos de criar. Para soluções de grandes, crie um tópico personalizado para cada categoria de eventos relacionados. Por exemplo, considere um aplicativo que envia eventos relacionados com a modificação de contas de utilizador e processamento de pedidos. Não é provável que qualquer manipulador de eventos quer que as duas categorias de eventos. Criar dois tópicos personalizados e deixar de subscrever o que interessa-los de manipuladores de eventos. Para soluções de pequenas, poderá preferir enviar todos os eventos para um único tópico. Os assinantes do evento podem filtrar para os tipos de evento que desejarem.

## <a name="event-subscriptions"></a>Subscrições de eventos

Uma subscrição informa ao Event Grid os eventos num tópico deseja receber. Ao criar a subscrição, é fornecer um ponto de extremidade para manipular o evento. Pode filtrar os eventos que são enviados para o ponto final. Pode filtrar por tipo de evento, ou um padrão de assunto. Para obter mais informações, consulte [esquema de subscrições do Event Grid](subscription-creation-schema.md).

Para obter exemplos de criação de subscrições, veja:

* [Exemplos da CLI do Azure para o Event Grid](cli-samples.md)
* [Exemplos do Azure PowerShell para o Event Grid](powershell-samples.md)
* [Modelos do Azure Resource Manager para Event Grid](template-samples.md)

Para obter informações sobre como obter subscrições do grid de seu evento atual, consulte [subscrições do Event Grid da consulta](query-event-subscriptions.md).

## <a name="event-subscription-expiration"></a>Expiração da subscrição de evento
A subscrição do evento expirará automaticamente após essa data. Definir uma expiração para subscrições de eventos que são necessários apenas por um período limitado, e não quiser se preocupar sobre a limpeza nessas subscrições. Por exemplo, ao criar uma subscrição de evento para um cenário de teste, pode desejar definir uma vencimento. 

Para obter um exemplo de configuração de uma de vencimento, consulte [subscrever com filtros avançados](how-to-filter-events.md#subscribe-with-advanced-filters).

## <a name="event-handlers"></a>Processadores de eventos

Da perspectiva do Event Grid, um manipulador de eventos é o local em que o evento é enviado. O manipulador demora alguns mais nenhuma ação ao processar o evento. Event Grid suporta vários tipos de manipulador. Pode usar um serviço do Azure suportado ou a sua própria webhook como o manipulador. Dependendo do tipo de processador, o Event Grid segue mecanismos diferentes para garantir a entrega do evento. Para manipuladores de eventos de webhook HTTP, o evento é repetido até que o manipulador retorna um código de estado `200 – OK`. Para a fila de armazenamento do Azure, os eventos sejam repetidos até que o serviço de fila com êxito processa o envio de mensagens na fila.

Para obter informações sobre a implementação de qualquer um dos manipuladores Event Grid suportados, consulte [manipuladores de eventos no Azure Event Grid](event-handlers.md).

## <a name="security"></a>Segurança

Event Grid fornece segurança para a subscrição de tópicos e tópicos de publicação. Ao subscrever, tem de ter as permissões adequadas no tópico do Event grid recurso ou eventos. Ao publicar, tem de ter um token SAS ou a autenticação de chave para o tópico. Para obter mais informações, consulte [Event Grid segurança e autenticação](security-authentication.md).

## <a name="event-delivery"></a>Entrega de eventos

Se o Event Grid não é possível confirmar que um evento foi recebido pelo ponto de final do subscritor, entrega novamente o evento. Para obter mais informações, consulte [entrega de mensagens do Event Grid e volte a tentar](delivery-and-retry.md).

## <a name="batching"></a>Lotes

Quando utilizar um tópico personalizado, eventos tem sempre de ser publicados numa matriz. Isso pode ser um lote de um para cenários de débito e baixa, no entanto, para elevado volume casos de utilização, recomenda-se que batch vários eventos em conjunto por publicar para alcançar maior eficiência. Lotes podem ser até 1 MB. Cada evento ainda não deve ser superior a 64 KB (disponibilidade geral) ou 1 MB (pré-visualização).

> [!NOTE]
> Um evento de tamanho até 64 KB é abordado pelo contrato de nível de serviço do disponibilidade geral (GA) (SLA). O suporte para um evento de tamanho até 1 MB está atualmente em pré-visualização. Eventos mais de 64 KB são cobrados em incrementos de 64 KB. 

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente com o Event Grid, veja [criar e encaminhar eventos personalizados com o Azure Event Grid](custom-event-quickstart.md).
