---
title: Conceitos - Azure Event Grid IoT Edge | Microsoft Docs
description: Conceitos em Grade de Eventos em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 8314447e7d5d282eb428ec9316c4eef6844a7423
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682384"
---
# <a name="event-grid-concepts"></a>Conceitos do Event Grid

Este artigo descreve os principais conceitos em Azure Event Grid.

## <a name="events"></a>Eventos

Um evento é a menor quantidade de informação que descreve totalmente algo que aconteceu no sistema. Cada evento tem informações comuns como: fonte do evento, hora do evento, e identificador único. Cada evento também tem informações específicas que só são relevantes para o tipo específico de evento. O suporte para um evento de tamanho até 1 MB está atualmente em pré-visualização.

Para as propriedades que estão incluídas num evento, consulte [o esquema de eventos Azure Event Grid.](event-schemas.md)

## <a name="publishers"></a>Editores

Um editor é o utilizador ou organização que decide enviar eventos para a Grade de Eventos. Pode publicar eventos a partir da sua própria aplicação.

## <a name="event-sources"></a>Origens de eventos

Uma fonte de evento é onde o evento acontece. Cada origem de evento está relacionada com um ou mais tipos de evento. Por exemplo, o Armazenamento do Azure é a origem de evento para eventos criados por blobs. A sua aplicação é a origem de evento para os eventos personalizados que definir. As origens de eventos são responsáveis pelo envio de eventos para o Event Grid.

## <a name="topics"></a>Tópicos

O tópico da grelha de eventos fornece um ponto final onde a fonte envia eventos. A editora cria o tópico da grelha de eventos e decide se uma fonte de evento precisa de um tópico ou mais de um tópico. Um tópico é usado para uma coleção de eventos relacionados. Para responder a determinados tipos de eventos, os subscritores decidem quais os tópicos a subscrever.

Ao desenhar a sua aplicação, tem a flexibilidade para decidir quantos tópicos criar. Para grandes soluções, crie um tópico personalizado para cada categoria de eventos relacionados. Por exemplo, considere uma aplicação que envia eventos relacionados com a modificação de contas de utilizador e com o processamento de encomendas. É pouco provável que qualquer processador de eventos queira as duas categorias de eventos. Crie dois tópicos personalizados e deixe que os processadores de eventos subscrevam aquele que for do seu interesse. Para pequenas soluções, você pode preferir enviar todos os eventos para um único tópico. Os subscritores do evento podem filtrar os tipos de eventos que quiserem.

Consulte [documentação da API](api.md) REST sobre como gerir tópicos na Grade de Eventos.

## <a name="event-subscriptions"></a>Subscrições de eventos

Uma subscrição diz ao Event Grid quais eventos sobre um tópico que está interessado em receber. Ao criar a subscrição, fornece um ponto final para lidar com o evento. Pode filtrar os eventos que são enviados para o ponto final. 

Consulte [documentação da API](api.md) REST sobre como gerir subscrições em Grade de Eventos.

## <a name="event-handlers"></a>Processadores de eventos

Do ponto de vista da Grade de Eventos, um manipulador de eventos é o local onde o evento é enviado. O manipulador toma medidas adicionais para processar o evento. A Grade de Eventos suporta vários tipos de manipuladores. Pode utilizar um serviço Azure suportado ou o seu próprio gancho web como manipulador. Dependendo do tipo de manipulador, a Grade de Eventos segue diferentes mecanismos para garantir a entrega do evento. Se o manipulador de eventos de destino for um gancho web HTTP, o evento é novamente experimentado até que o manipulador devolva um código de estado de `200 – OK` . Para o Edge Hub, se o evento for entregue sem qualquer exceção, é considerado um sucesso.

## <a name="security"></a>Segurança

A Grade de Eventos oferece segurança para subscrever tópicos e publicar tópicos. Para mais informações, consulte [a segurança e a autenticação da Grade de Eventos.](security-authentication.md)

## <a name="event-delivery"></a>Entrega de eventos

Se o Event Grid não puder confirmar que um evento foi recebido pelo ponto final do assinante, ele reentrece o evento. Para obter mais informações, consulte [a entrega da mensagem de Event Grid e redando.](delivery-retry.md)

## <a name="batching"></a>Lotes

Ao utilizar um tópico personalizado, os eventos devem ser sempre publicados num conjunto. Para cenários de baixa produção, a matriz terá apenas um valor. Para casos de uso de grande volume, recomendamos que desemote vários eventos juntos por publicação para obter uma maior eficiência. Os lotes podem chegar a 1 MB. Cada evento não deve ainda ser superior a 1 MB (pré-visualização).