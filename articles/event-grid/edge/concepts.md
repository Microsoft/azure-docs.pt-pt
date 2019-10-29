---
title: Conceitos-IoT Edge de grade de eventos do Azure | Microsoft Docs
description: Conceitos na grade de eventos em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 73309e10e88c11e639e6ac6fd3bb061e1b5c685b
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992551"
---
# <a name="event-grid-concepts"></a>Conceitos do Event Grid

Este artigo descreve os principais conceitos na grade de eventos do Azure.

## <a name="events"></a>Eventos

Um evento é a menor quantidade de informações que descreve totalmente algo que aconteceu no sistema. Cada evento tem informações comuns como: origem do evento, hora em que o evento ocorreu e identificador exclusivo. Cada evento também tem informações específicas que só são relevantes para o tipo específico de evento. O suporte para um evento de tamanho de até 1 MB está atualmente em visualização.

Para as propriedades incluídas em um evento, consulte esquema de [eventos da grade de eventos do Azure](event-schemas.md).

## <a name="publishers"></a>Editores

Um Publicador é o usuário ou a organização que decide enviar eventos para a grade de eventos. Você pode publicar eventos de seu próprio aplicativo.

## <a name="event-sources"></a>Origens de eventos

Uma origem do evento é onde o evento acontece. Cada origem do evento está relacionada a um ou mais tipos de evento. Por exemplo, o armazenamento do Azure é a origem do evento para eventos de blob criados. Seu aplicativo é a origem do evento para eventos personalizados que você definir. Origens de evento são responsáveis por enviar eventos para a grade de eventos.

## <a name="topics"></a>Tópicos

O tópico da grade de eventos fornece um ponto de extremidade onde a origem envia eventos. O Publicador cria o tópico da grade de eventos e decide se uma origem do evento precisa de um tópico ou mais de um tópico. Um tópico é usado para uma coleção de eventos relacionados. Para responder a determinados tipos de eventos, os assinantes decidem quais tópicos assinar.

Ao projetar seu aplicativo, você tem a flexibilidade de decidir quantos tópicos devem ser criados. Para soluções grandes, crie um tópico personalizado para cada categoria de eventos relacionados. Por exemplo, considere um aplicativo que envia eventos relacionados à modificação de contas de usuário e pedidos de processamento. É improvável que qualquer manipulador de eventos queira as duas categorias de eventos. Crie dois tópicos personalizados e permita que os manipuladores de eventos assinem aquele que os interessa. Para soluções pequenas, você pode preferir enviar todos os eventos para um único tópico. Os assinantes de eventos podem filtrar os tipos de eventos que desejam.

Consulte a [documentação da API REST](api.md) sobre como gerenciar tópicos na grade de eventos.

## <a name="event-subscriptions"></a>Subscrições de eventos

Uma assinatura informa à grade de eventos quais eventos em um tópico você está interessado em receber. Ao criar a assinatura, você fornece um ponto de extremidade para manipular o evento. Você pode filtrar os eventos que são enviados para o ponto de extremidade. 

Consulte a [documentação da API REST](api.md) sobre como gerenciar assinaturas na grade de eventos.

## <a name="event-handlers"></a>Processadores de eventos

De uma perspectiva da grade de eventos, um manipulador de eventos é o local onde o evento é enviado. O manipulador executa uma ação adicional para processar o evento. A grade de eventos dá suporte a vários tipos de manipulador. Você pode usar um serviço do Azure com suporte ou seu próprio gancho da Web como o manipulador. Dependendo do tipo de manipulador, a grade de eventos segue diferentes mecanismos para garantir a entrega do evento. Se o manipulador de eventos de destino for um gancho da Web HTTP, o evento será repetido quando o manipulador retornar um código de status `200 – OK`. Para o Hub do Edge, se o evento for entregue sem nenhuma exceção, ele será considerado com êxito.

## <a name="security"></a>Segurança

A grade de eventos fornece segurança para assinar tópicos e publicar tópicos. Para obter mais informações, consulte [segurança e autenticação da grade de eventos](security-authentication.md).

## <a name="event-delivery"></a>Entrega de eventos

Se a grade de eventos não puder confirmar que um evento foi recebido pelo ponto de extremidade do Assinante, ele reentregará o evento. Para obter mais informações, consulte [entrega de mensagem de grade de eventos e tentar novamente](delivery-retry.md).

## <a name="batching"></a>Lotes

Ao usar um tópico personalizado, os eventos sempre devem ser publicados em uma matriz. Para cenários de baixa taxa de transferência, a matriz terá apenas um valor. Para casos de uso de alto volume, é recomendável que você execute vários eventos em lote por publicação para obter maior eficiência. Os lotes podem ter até 1 MB. Cada evento ainda deve ser maior que 1 MB (versão prévia).