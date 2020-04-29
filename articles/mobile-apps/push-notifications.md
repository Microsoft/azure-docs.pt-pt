---
title: A importância das notificações push nas suas aplicações móveis com visual studio App Center e Hubs de Notificação Azure
description: Saiba mais sobre serviços como visual studio App Center que pode usar para se envolver com os seus utilizadores de aplicações móveis.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: bc59857cdb843dfff54e69f2226f13cfe70df8f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80235335"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Envolva-se com os utilizadores da sua aplicação enviando notificações push

Quer esteja a construir aplicações de consumo ou de empresa, pretende que os utilizadores utilizem a sua aplicação de forma ativa. Há muitas vezes momentos em que quer partilhar notícias de última hora, atualizações de jogos, ofertas emocionantes, atualizações de produtos ou qualquer outra informação relevante com os seus utilizadores. Para aumentar o envolvimento com os seus utilizadores e fazê-los regressar à sua aplicação, precisa de uma forma de comunicar com os seus utilizadores em tempo real.

As notificações push fornecem uma forma rápida e eficiente de comunicar com os utilizadores da sua aplicação. Pode contactar os seus utilizadores na hora certa e notificá-los das informações desejadas, normalmente num item pop-up ou numa caixa de diálogo num dispositivo móvel, independentemente do que estejam a fazer.

## <a name="value-of-push-notifications"></a>Valor das notificações push
As notificações push fornecem valor aos utilizadores e empresas.

As empresas podem:
- Comunicar diretamente com os utilizadores enviando mensagens em plataformas suportadas no momento certo.
- Impulsionar o envolvimento dos utilizadores enviando atualizações e lembretes em tempo real aos utilizadores, incentivando-os a envolverem-se regularmente com a aplicação.
- Retenha os utilizadores e reenvolva-se com utilizadores inativos que descarregaram a aplicação, mas não a utilizem para voltar a estar ativa.
- Aumentar as taxas de conversão analisando o comportamento dos utilizadores, segmentando os utilizadores e alavancando campanhas baseadas em notificações de push móvel.
- Promover produtos e serviços enviando mensagens push e atualizações oportunas para os utilizadores.
- Utilizadores-alvo enviando mensagens de pressão personalizadas.

Para os utilizadores de aplicações, notificações push:
- Fornecer valor e informação em tempo real.
- Lembre os utilizadores de usarem a aplicação.

Utilize os seguintes serviços para permitir notificações push nas suas aplicações móveis.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
Com o [App Center Push,](/appcenter/push/)pode enviar mensagens direcionadas para utilizadores de iOS, Android e Windows sem ter de gerir o processo de envio de notificações para dispositivos utilizando serviços de notificação push (PNS). Construído em cima dos Hubs de Notificação Azure, este serviço elimina complexidades associadas a notificações de pressão manualmente, fornecendo um poderoso dashboard.

**Principais funcionalidades**
- Envie notificações push para dispositivos móveis através de várias plataformas.
- Utilize notificações para enviar dados para uma aplicação, apresentar uma mensagem ao utilizador ou desencadear uma ação pela aplicação.
- Utilize os alvos de notificação para: 
    - Transmita mensagens para todos os dispositivos registados.
    - Envie notificações para o público com base em informações do dispositivo e propriedades personalizadas.
    - Envie notificações a utilizadores específicos.
    - Envie notificações para dispositivos específicos.
- Faça uso da telemetria rica em pushs, dispositivos e erros que estão disponíveis no portal App Center.
- Ganhe suporte de plataforma para iOS, Android, macOS, Xamarin, React Native, Unity e Cordova.

**Referências**
- [Inscreva-se no Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Começar com o App Center Push](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure Notification Hubs
[O Notification Hubs](/azure/notification-hubs/notification-hubs-push-notification-overview) fornece um motor push de fácil utilização e escalado. Pode usá-lo para enviar notificações para qualquer plataforma e de qualquer extremidade traseira na nuvem ou no local.

**Principais funcionalidades**
- Envie notificações push para qualquer plataforma a partir de qualquer extremidade traseira, na nuvem ou no local.
- A transmissão rápida empurra para milhões de dispositivos móveis com uma única chamada API.
- Notificações push personalizadas por cliente, idioma e localização.
- Defina e notifique os segmentos de cliente de forma dinâmica.
- Escala instantaneamente para milhões de dispositivos móveis.
- Ganhe suporte de plataforma para iOS, Android, Windows, Kindle e Baidu.
        
**Referências**
- [Portal do Azure](https://portal.azure.com) 
- [Começar com hubs de notificação Azure](/azure/notification-hubs/)
- [Guias de Início Rápido](/azure/notification-hubs/create-notification-hub-portal)
- [Amostras](/azure/notification-hubs/samples)
