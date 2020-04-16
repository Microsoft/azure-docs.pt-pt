---
title: Conceitos de Grelha de Eventos Azure
description: Descreve o Azure Event Grid e respetivos conceitos. Define vários componentes chave da Grelha de Eventos.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: spelluru
ms.openlocfilehash: 348d82f704b89b97e11a09b8f88e92831901b3bf
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393468"
---
# <a name="concepts-in-azure-event-grid"></a>Conceitos na Grelha de Eventos Azure

Este artigo descreve os principais conceitos na Grelha de Eventos Azure.

## <a name="events"></a>Eventos

Um evento é a menor quantidade de informação que descreve totalmente algo que aconteceu no sistema. Cada evento tem informações comuns como: fonte do evento, hora do evento, e identificador único. Cada evento também tem informações específicas que só são relevantes para o tipo específico de evento. Por exemplo, um evento sobre um novo ficheiro que está a ser criado no Armazenamento do Azure inclui detalhes sobre o ficheiro, tal como o valor `lastTimeModified`. Ou, um evento dos Hubs de Eventos inclui o URL do ficheiro de Recolha. 

Um evento de tamanho até 64 KB é coberto pelo Acordo de Nível de Serviço de Disponibilidade Geral (GA) (SLA). O suporte para um evento de tamanho até 1 MB está atualmente em pré-visualização. Eventos superiores a 64 KB são carregados em incrementos de 64 KB. 


Para as propriedades que são enviadas em um evento, consulte [O evento Azure Event Grid schema](event-schema.md).

## <a name="publishers"></a>Editores

Uma editora é o utilizador ou organização que decide enviar eventos para a Event Grid. A Microsoft publica eventos para vários serviços Azure. Pode publicar eventos a partir da sua própria aplicação. As organizações que alojam serviços fora do Azure podem publicar eventos através do Event Grid.

## <a name="event-sources"></a>Origens de eventos

Uma fonte de evento é onde o evento acontece. Cada origem de evento está relacionada com um ou mais tipos de evento. Por exemplo, o Armazenamento do Azure é a origem de evento para eventos criados por blobs. O Hub IoT é a origem de evento para eventos criados por dispositivos. A sua aplicação é a origem de evento para os eventos personalizados que definir. As origens de eventos são responsáveis pelo envio de eventos para o Event Grid.

Para obter informações sobre a implementação de qualquer uma das fontes suportadas da Rede de Eventos, consulte fontes do [evento em Azure Event Grid](overview.md#event-sources).

## <a name="topics"></a>Tópicos

O tópico da grelha de eventos fornece um ponto final onde a fonte envia eventos. A editora cria o tópico da grelha do evento e decide se uma fonte de evento precisa de um tópico ou mais do que um tópico. Um tópico é usado para uma coleção de eventos relacionados. Para responder a certos tipos de eventos, os subscritores decidem quais os tópicos a subscrever.

Tópicos de sistema são tópicos incorporados fornecidos pelos serviços do Azure. Não vê tópicos de sistema na sua subscrição do Azure porque o publicador é o proprietário dos tópicos, mas pode subscrevê-los. Para subscrever, terá de fornecer informações sobre o recurso do qual pretende receber eventos. Desde que tenha acesso ao recurso, pode subscrever os respetivos eventos.

Tópicos personalizados são tópicos de terceiros e da aplicação. Quando cria ou lhe é atribuído acesso a um tópico personalizado, verá esse tópico personalizado na sua subscrição.

Ao desenhar a sua aplicação, tem flexibilidade ao decidir quantos tópicos criar. Para grandes soluções, crie um tópico personalizado para cada categoria de eventos relacionados. Por exemplo, considere uma aplicação que envia eventos relacionados com a modificação de contas de utilizador e com o processamento de encomendas. É pouco provável que qualquer processador de eventos queira as duas categorias de eventos. Crie dois tópicos personalizados e deixe que os processadores de eventos subscrevam aquele que for do seu interesse. Para soluções pequenas, talvez prefira enviar todos os eventos para um único tópico. Os subscritores do evento podem filtrar os tipos de eventos que querem.

## <a name="event-subscriptions"></a>Subscrições de eventos

Uma subscrição diz ao Event Grid quais os eventos sobre um tópico que está interessado em receber. Ao criar a subscrição, fornece um ponto final para lidar com o evento. Pode filtrar os eventos enviados para o ponto final. Pode filtrar por tipo de evento ou padrão de assunto. Para mais informações, consulte o esquema de [subscrição da Event Grid](subscription-creation-schema.md).

Por exemplo, na criação de subscrições, consulte:

* [Exemplos da CLI do Azure para o Event Grid](cli-samples.md)
* [Exemplos do Azure PowerShell para o Event Grid](powershell-samples.md)
* [Modelos do Azure Resource Manager para Event Grid](template-samples.md)

Para obter as suas subscrições de grelha de eventos atuais, consulte [as subscrições da Grelha](query-event-subscriptions.md)de Eventos de Consulta .

## <a name="event-subscription-expiration"></a>Expiração da subscrição do evento
A subscrição do evento expirará automaticamente após essa data. Detete uma expiração para subscrições de eventos que só são necessárias por um tempo limitado e não se quer preocupar em limpar essas subscrições. Por exemplo, ao criar uma subscrição de evento para testar um cenário, é melhor definir uma expiração. 

Para um exemplo de definição de uma expiração, consulte [Subscrever com filtros avançados](how-to-filter-events.md#subscribe-with-advanced-filters).

## <a name="event-handlers"></a>Processadores de eventos

Do ponto de vista da Grelha de Eventos, um manipulador de eventos é o local onde o evento é enviado. O manipulador toma mais medidas para processar o evento. A Grelha de Eventos suporta vários tipos de manipuladores. Pode utilizar um serviço Azure suportado ou o seu próprio webhook como manipulador. Dependendo do tipo de manipulador, a Rede de Eventos segue diferentes mecanismos para garantir a entrega do evento. Para os manipuladores de eventos http webhook, o evento `200 – OK`é novamente experimentado até que o manipulador derdete um código de estado de . Para a fila de armazenamento Azure, os eventos são novamente experimentados até que o serviço de fila processe com sucesso a mensagem empurrar para a fila.

Para obter informações sobre a implementação de qualquer um dos manipuladores de Rede de Eventos suportados, consulte os manipuladores de [eventos em Azure Event Grid](event-handlers.md).

## <a name="security"></a>Segurança

A Event Grid oferece segurança para subscrever tópicos e temas editoriais. Ao subscrever, deve ter permissões adequadas sobre o tópico da rede de recursos ou eventos. Ao publicar, deve ter um símbolo SAS ou autenticação chave para o tópico. Para mais informações, consulte [a segurança e a autenticação da Rede de Eventos.](security-authentication.md)

## <a name="event-delivery"></a>Entrega de eventos

Se a Event Grid não puder confirmar que um evento foi recebido pelo ponto final do assinante, reentrega o evento. Para mais informações, consulte a [entrega e a retentação da mensagem da Rede de Eventos.](delivery-and-retry.md)

## <a name="batching"></a>Lotes

Ao utilizar um tópico personalizado, os eventos devem ser sempre publicados numa matriz. Este pode ser um lote de um para cenários de baixa produção, no entanto, para casos de uso de alto volume, recomenda-se que você emlote vários eventos juntos por publicação para obter uma maior eficiência. Os lotes podem chegar a 1 MB. Cada evento não deve ainda ser superior a 64 KB (Disponibilidade Geral) ou 1 MB (pré-visualização).

> [!NOTE]
> Um evento de tamanho até 64 KB é coberto pelo Acordo de Nível de Serviço de Disponibilidade Geral (GA) (SLA). O suporte para um evento de tamanho até 1 MB está atualmente em pré-visualização. Eventos superiores a 64 KB são carregados em incrementos de 64 KB. 

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente a usar a Grelha de Eventos, consulte [create e encaminhe eventos personalizados com a Grelha de Eventos Azure](custom-event-quickstart.md).
