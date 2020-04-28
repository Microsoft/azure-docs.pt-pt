---
title: Conceitos - Azure Event Grid IoT Edge [ Microsoft Docs
description: Conceitos em Grelha de Eventos na Borda IoT.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 73309e10e88c11e639e6ac6fd3bb061e1b5c685b
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "72992551"
---
# <a name="event-grid-concepts"></a>Conceitos do Event Grid

Este artigo descreve os principais conceitos na Grelha de Eventos Azure.

## <a name="events"></a>Eventos

Um evento é a menor quantidade de informação que descreve totalmente algo que aconteceu no sistema. Cada evento tem informações comuns como: fonte do evento, hora do evento, e identificador único. Cada evento também tem informações específicas que só são relevantes para o tipo específico de evento. O suporte para um evento de tamanho até 1 MB está atualmente em pré-visualização.

Para as propriedades que estão incluídas num evento, consulte [o evento Azure Event Grid schema](event-schemas.md).

## <a name="publishers"></a>Editores

Uma editora é o utilizador ou organização que decide enviar eventos para a Event Grid. Pode publicar eventos a partir da sua própria aplicação.

## <a name="event-sources"></a>Origens de eventos

Uma fonte de evento é onde o evento acontece. Cada origem de evento está relacionada com um ou mais tipos de evento. Por exemplo, o Armazenamento do Azure é a origem de evento para eventos criados por blobs. A sua aplicação é a origem de evento para os eventos personalizados que definir. As origens de eventos são responsáveis pelo envio de eventos para o Event Grid.

## <a name="topics"></a>Tópicos

O tópico da grelha de eventos fornece um ponto final onde a fonte envia eventos. A editora cria o tópico da grelha do evento e decide se uma fonte de evento precisa de um tópico ou mais do que um tópico. Um tópico é usado para uma coleção de eventos relacionados. Para responder a certos tipos de eventos, os subscritores decidem quais os tópicos a subscrever.

Ao desenhar a sua aplicação, tem a flexibilidade para decidir quantos tópicos criar. Para grandes soluções, crie um tópico personalizado para cada categoria de eventos relacionados. Por exemplo, considere uma aplicação que envia eventos relacionados com a modificação de contas de utilizador e com o processamento de encomendas. É pouco provável que qualquer processador de eventos queira as duas categorias de eventos. Crie dois tópicos personalizados e deixe que os processadores de eventos subscrevam aquele que for do seu interesse. Para soluções pequenas, talvez prefira enviar todos os eventos para um único tópico. Os subscritores do evento podem filtrar os tipos de eventos que querem.

Consulte a documentação da [Rest API](api.md) sobre como gerir tópicos na Grelha de Eventos.

## <a name="event-subscriptions"></a>Subscrições de eventos

Uma subscrição diz ao Event Grid quais os eventos sobre um tópico que está interessado em receber. Ao criar a subscrição, fornece um ponto final para lidar com o evento. Pode filtrar os eventos enviados para o ponto final. 

Consulte a documentação rest [API](api.md) sobre como gerir as subscrições em Event Grid.

## <a name="event-handlers"></a>Processadores de eventos

Do ponto de vista da Grelha de Eventos, um manipulador de eventos é o local onde o evento é enviado. O manipulador toma mais medidas para processar o evento. A Grelha de Eventos suporta vários tipos de manipuladores. Pode utilizar um serviço Azure suportado ou o seu próprio gancho web como manipulador. Dependendo do tipo de manipulador, a Rede de Eventos segue diferentes mecanismos para garantir a entrega do evento. Se o manipulador de eventos de destino for um gancho web HTTP, o evento é novamente experimentado quando o manipulador devolve um código de estado de `200 – OK`. Para o Edge Hub, se o evento for entregue sem qualquer exceção, é considerado bem sucedido.

## <a name="security"></a>Segurança

A Event Grid oferece segurança para subscrever tópicos e temas editoriais. Para mais informações, consulte [a segurança e a autenticação da Rede de Eventos.](security-authentication.md)

## <a name="event-delivery"></a>Entrega de eventos

Se a Event Grid não puder confirmar que um evento foi recebido pelo ponto final do assinante, reentrega o evento. Para mais informações, consulte a [entrega e a retentação da mensagem da Rede de Eventos.](delivery-retry.md)

## <a name="batching"></a>Lotes

Ao utilizar um tópico personalizado, os eventos devem ser sempre publicados numa matriz. Para cenários de baixa pontuação, a matriz terá apenas um valor. Para casos de uso de grande volume, recomendamos que emlota vários eventos por publicação para obter uma maior eficiência. Os lotes podem chegar a 1 MB. Cada evento ainda não deve ser superior a 1 MB (pré-visualização).