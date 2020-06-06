---
title: A importância das notificações push nas suas aplicações móveis com o Visual Studio App Center e os Azure Notification Hubs
description: Saiba mais sobre serviços como o Visual Studio App Center que pode usar para se envolver com os utilizadores da sua aplicação móvel.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: c9ae5e268066e498b3a341a9c54391ee5d15d592
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450655"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Envolva-se com os utilizadores da sua aplicação enviando notificações push

Quer esteja a construir aplicações de consumidores ou empresas, pretende que os utilizadores utilizem a sua aplicação ativamente. Muitas vezes, há momentos em que pretende partilhar notícias de última hora, atualizações de jogos, ofertas emocionantes, atualizações de produtos ou qualquer outra informação relevante com os seus utilizadores. Para aumentar o envolvimento com os seus utilizadores e fazê-los voltar à sua aplicação, precisa de uma forma de comunicar com os seus utilizadores em tempo real.

As notificações push fornecem uma forma rápida e eficiente de comunicar com os utilizadores da sua aplicação. Pode contactar os seus utilizadores no momento certo e notificá-los das informações desejadas, normalmente num item pop-up ou caixa de diálogo num dispositivo móvel, independentemente do que estejam a fazer.

## <a name="value-of-push-notifications"></a>Valor das notificações push
As notificações push fornecem valor aos utilizadores e empresas.

As empresas podem:
- Comunicar diretamente com os utilizadores enviando mensagens em plataformas suportadas no momento certo.
- Aumente o envolvimento dos utilizadores enviando atualizações e lembretes em tempo real aos utilizadores, incentivando-os a envolverem-se regularmente com a aplicação.
- Reter os utilizadores e reiniciar com utilizadores inativos que descarregaram a aplicação mas não a utilizem para se tornarem novamente ativos.
- Aumente as taxas de conversão analisando o comportamento do utilizador, segmentando os utilizadores e aproveitando campanhas com base em notificações de push móvel.
- Promover produtos e serviços enviando mensagens push e atualizações atempadamente para os utilizadores.
- Utilizadores-alvo, enviando mensagens de impulso personalizadas.

Para os utilizadores de aplicações, notificações push:
- Fornecer valor e informação em tempo real.
- Lembre os utilizadores de usarem a aplicação.

Utilize os seguintes serviços para permitir notificações push nas suas aplicações móveis.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
Com [o App Center Push,](/appcenter/push/)pode enviar mensagens direcionadas para utilizadores de iOS, Android e Windows sem ter de gerir o processo de envio de notificações para dispositivos utilizando serviços de notificação push (PNS). Construído em cima dos Hubs de Notificação Azure, este serviço elimina as complexidades associadas ao empurrar as notificações manualmente, fornecendo um poderoso dashboard.

**Principais funcionalidades**
- Envie notificações push para dispositivos móveis em várias plataformas.
- Utilize notificações para enviar dados para uma aplicação, apresentar uma mensagem ao utilizador ou desencadear uma ação da aplicação.
- Utilize alvos de notificação para: 
    - Mensagens de transmissão para todos os dispositivos registados.
    - Envie notificações para o público com base em informações do dispositivo e propriedades personalizadas.
    - Envie notificações a utilizadores específicos.
    - Envie notificações para dispositivos específicos.
- Faça uso da rica telemetria em pushes, dispositivos e erros que estão disponíveis no portal do Centro de Aplicações.
- Ganhe suporte de plataforma para iOS, Android, macOS, Xamarin, React Native, Unity e Cordova.

**Referências**
- [Inscreva-se no Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Começar com o App Center Push](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure Notification Hubs
Os Centros de [Notificação](/azure/notification-hubs/notification-hubs-push-notification-overview) fornecem um motor de pressão fácil de utilizar e dimensionado. Pode usá-lo para enviar notificações para qualquer plataforma e a partir de qualquer extremidade traseira na nuvem ou no local.

**Principais funcionalidades**
- Envie notificações push para qualquer plataforma a partir de qualquer extremidade traseira, na nuvem ou no local.
- Transmissão rápida empurra para milhões de dispositivos móveis com uma única chamada API.
- Alfaiate de notificações push por cliente, idioma e localização.
- Definir e notificar dinamicamente os segmentos de clientes.
- Escalar instantaneamente para milhões de dispositivos móveis.
- Ganhe suporte à plataforma para iOS, Android, Windows, Kindle e Baidu.
        
**Referências**
- [Portal do Azure](https://portal.azure.com) 
- [Começar com os Hubs de Notificação Azure](/azure/notification-hubs/)
- [Guias de Início Rápido](/azure/notification-hubs/create-notification-hub-portal)
- [Amostras](/azure/notification-hubs/samples)
