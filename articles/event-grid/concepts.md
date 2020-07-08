---
title: Conceitos de grelha de eventos Azure
description: Descreve o Azure Event Grid e respetivos conceitos. Define vários componentes-chave da Grade de Eventos.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: spelluru
ms.openlocfilehash: a1464acf2b4a620bf0e2dc91f362cc1739737176
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84659185"
---
# <a name="concepts-in-azure-event-grid"></a>Conceitos em Azure Event Grid

Este artigo descreve os principais conceitos em Azure Event Grid.

## <a name="events"></a>Eventos

Um evento é a menor quantidade de informação que descreve totalmente algo que aconteceu no sistema. Cada evento tem informações comuns como: fonte do evento, hora do evento, e identificador único. Cada evento também tem informações específicas que só são relevantes para o tipo específico de evento. Por exemplo, um evento sobre um novo ficheiro que está a ser criado no Armazenamento do Azure inclui detalhes sobre o ficheiro, tal como o valor `lastTimeModified`. Ou, um evento dos Hubs de Eventos inclui o URL do ficheiro de Recolha. 

Um evento de tamanho até 64 KB é coberto pelo Acordo de Nível de Serviço (SLA) de Disponibilidade Geral (GA). O suporte para um evento de tamanho até 1 MB está atualmente em pré-visualização. Os eventos com mais de 64 KB são carregados em incrementos de 64 KB. 


Para as propriedades que são enviadas num evento, consulte [o esquema de eventos Azure Event Grid](event-schema.md).

## <a name="publishers"></a>Editores

Um editor é o utilizador ou organização que decide enviar eventos para a Grade de Eventos. A Microsoft publica eventos para vários serviços Azure. Pode publicar eventos a partir da sua própria aplicação. As organizações que alojam serviços fora do Azure podem publicar eventos através do Event Grid.

## <a name="event-sources"></a>Origens de eventos

Uma fonte de evento é onde o evento acontece. Cada origem de evento está relacionada com um ou mais tipos de evento. Por exemplo, o Armazenamento do Azure é a origem de evento para eventos criados por blobs. O Hub IoT é a origem de evento para eventos criados por dispositivos. A sua aplicação é a origem de evento para os eventos personalizados que definir. As origens de eventos são responsáveis pelo envio de eventos para o Event Grid.

Para obter informações sobre a implementação de qualquer uma das fontes suportadas da Grelha de Eventos, consulte [fontes do Evento em Azure Event Grid](overview.md#event-sources).

## <a name="topics"></a>Tópicos

O tópico da grelha de eventos fornece um ponto final onde a fonte envia eventos. A editora cria o tópico da grelha de eventos e decide se uma fonte de evento precisa de um tópico ou mais de um tópico. Um tópico é usado para uma coleção de eventos relacionados. Para responder a determinados tipos de eventos, os subscritores decidem quais os tópicos a subscrever.

Os tópicos do sistema são tópicos integrados fornecidos por serviços Azure, tais como Azure Storage, Azure Event Hubs e Azure Service Bus. Pode criar tópicos de sistema na sua subscrição Azure e subscrevê-los. Para mais informações, consulte [a visão geral dos tópicos do sistema.](system-topics.md) 

Tópicos personalizados são tópicos de terceiros e da aplicação. Quando cria ou lhe é atribuído acesso a um tópico personalizado, verá esse tópico personalizado na sua subscrição. Para mais informações, consulte [tópicos personalizados.](custom-topics.md)

Ao desenhar a sua aplicação, tem flexibilidade ao decidir quantos tópicos criar. Para grandes soluções, crie um tópico personalizado para cada categoria de eventos relacionados. Por exemplo, considere uma aplicação que envia eventos relacionados com a modificação de contas de utilizador e com o processamento de encomendas. É pouco provável que qualquer processador de eventos queira as duas categorias de eventos. Crie dois tópicos personalizados e deixe que os processadores de eventos subscrevam aquele que for do seu interesse. Para pequenas soluções, você pode preferir enviar todos os eventos para um único tópico. Os subscritores do evento podem filtrar os tipos de eventos que quiserem.

## <a name="event-subscriptions"></a>Subscrições de eventos

Uma subscrição diz ao Event Grid quais eventos sobre um tópico que está interessado em receber. Ao criar a subscrição, fornece um ponto final para lidar com o evento. Pode filtrar os eventos que são enviados para o ponto final. Pode filtrar por tipo de evento ou padrão de sujeito. Para mais informações, consulte [o esquema de subscrição da Grade de Eventos.](subscription-creation-schema.md)

Por exemplo, criar subscrições, consulte:

* [Exemplos da CLI do Azure para o Event Grid](cli-samples.md)
* [Exemplos do Azure PowerShell para o Event Grid](powershell-samples.md)
* [Modelos do Azure Resource Manager para Event Grid](template-samples.md)

Para obter informações sobre a obtenção das subscrições atuais da grelha de eventos, consulte [as subscrições da Grelha de Eventos de Consulta](query-event-subscriptions.md).

## <a name="event-subscription-expiration"></a>Expiração da subscrição do evento
A subscrição do evento expirará automaticamente após essa data. Desa estada uma expiração para subscrições de eventos que são necessárias apenas por um tempo limitado e você não quer se preocupar em limpar essas subscrições. Por exemplo, ao criar uma subscrição de eventos para testar um cenário, é possível que queira definir uma expiração. 

Para um exemplo de definição de expiração, consulte [Subscreva com filtros avançados](how-to-filter-events.md#subscribe-with-advanced-filters).

## <a name="event-handlers"></a>Processadores de eventos

Do ponto de vista da Grade de Eventos, um manipulador de eventos é o local onde o evento é enviado. O manipulador toma mais algumas medidas para processar o evento. A Grade de Eventos suporta vários tipos de manipuladores. Você pode usar um serviço Azure suportado ou seu próprio webhook como o manipulador. Dependendo do tipo de manipulador, a Grade de Eventos segue diferentes mecanismos para garantir a entrega do evento. Para os manipuladores de eventos http webhook, o evento é novamente experimentado até que o manipulador devolva um código de estado de `200 – OK` . Para a fila de armazenamento Azure, os eventos são novamente experimentados até que o serviço de Fila processe com sucesso a mensagem empurrando para a fila.

Para obter informações sobre a implementação de qualquer um dos manipuladores de grelha de eventos suportados, consulte [os manipuladores de eventos em Azure Event Grid](event-handlers.md).

## <a name="security"></a>Segurança

A Grade de Eventos oferece segurança para subscrever tópicos e publicar tópicos. Ao subscrever, deve ter permissões adequadas sobre o tópico de rede de recursos ou eventos. Ao publicar, deve ter um token SAS ou autenticação chave para o tópico. Para mais informações, consulte [a segurança e a autenticação da Grade de Eventos.](security-authentication.md)

## <a name="event-delivery"></a>Entrega de eventos

Se o Event Grid não puder confirmar que um evento foi recebido pelo ponto final do assinante, ele reentrece o evento. Para obter mais informações, consulte [a entrega da mensagem de Event Grid e redando.](delivery-and-retry.md)

## <a name="batching"></a>Lotes

Ao utilizar um tópico personalizado, os eventos devem ser sempre publicados num conjunto. Este pode ser um lote de um para cenários de baixa produção, no entanto, para casos de uso de volume elevado, é recomendado que você lote de vários eventos juntos por publicação para obter uma maior eficiência. Os lotes podem chegar a 1 MB. Cada evento não deve ainda ser superior a 64 KB (Disponibilidade Geral) ou 1 MB (pré-visualização).

> [!NOTE]
> Um evento de tamanho até 64 KB é coberto pelo Acordo de Nível de Serviço (SLA) de Disponibilidade Geral (GA). O suporte para um evento de tamanho até 1 MB está atualmente em pré-visualização. Os eventos com mais de 64 KB são carregados em incrementos de 64 KB. 

## <a name="next-steps"></a>Próximos passos

* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente a utilizar a Grade de Eventos, consulte [Criar e encaminhar eventos personalizados com a Azure Event Grid](custom-event-quickstart.md).
