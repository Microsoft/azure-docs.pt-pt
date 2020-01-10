---
title: A importância das notificações por push em seus aplicativos móveis com Visual Studio App Center e hubs de notificação do Azure
description: Saiba mais sobre serviços como Visual Studio App Center que você pode usar para se envolver com seus usuários de aplicativos móveis.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 1717bf6da849218f60bae7dafca20cb304286976
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453135"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Entre em contato com os usuários do seu aplicativo enviando notificações por push

Independentemente de você estar criando aplicativos de consumidor ou corporativos, você deseja que os usuários usem seu aplicativo ativamente. Geralmente, há ocasiões em que você deseja compartilhar últimas notícias, atualizações de jogos, ofertas empolgantes, atualizações de produtos ou qualquer outra informação relevante com seus usuários. Para aumentar o envolvimento com seus usuários e fazê-los retornar ao seu aplicativo, você precisa de uma maneira de se comunicar com seus usuários em tempo real.

As notificações por push fornecem uma maneira rápida e eficiente de se comunicar com os usuários do aplicativo. Você pode entrar em contato com seus usuários no momento certo e notificá-los das informações desejadas, geralmente em um item pop-up ou caixa de diálogo em um dispositivo móvel, independentemente do que estão fazendo.

## <a name="value-of-push-notifications"></a>Valor de notificações por push
As notificações por push fornecem valor aos usuários e às empresas.

As empresas podem:
- Comunique-se diretamente com os usuários enviando mensagens em plataformas com suporte no momento certo.
- Aumente o envolvimento do usuário enviando atualizações e lembretes em tempo real para os usuários, incentivando-os a se envolver com o aplicativo regularmente.
- Manter os usuários e reparticipar com usuários inativos que baixaram o aplicativo, mas não usá-los para se tornarem ativos novamente.
- Aumente as taxas de conversão analisando o comportamento do usuário, segmentando os usuários e aproveitando as campanhas com base em notificações por push móvel.
- Promova produtos e serviços enviando mensagens por push e atualizações oportunas para os usuários.
- Direcionar usuários enviando mensagens de push personalizadas.

Para usuários de aplicativos, notificações por push:
- Forneça valor e informações em tempo real.
- Lembre os usuários para usar o aplicativo.

Use os seguintes serviços para habilitar notificações por push em seus aplicativos móveis.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
Com o [app Center Push](/appcenter/push/), você pode enviar mensagens direcionadas para os usuários do IOS, Android e Windows sem precisar gerenciar o processo de envio de notificações para dispositivos usando o PNS (serviços de notificação por push). Criado com base nos hubs de notificação do Azure, esse serviço elimina as complexidades associadas ao envio manual de notificações, fornecendo um painel poderoso.

**Principais funcionalidades**
- Envie notificações por push para dispositivos móveis em uma variedade de plataformas.
- Use notificações para enviar dados a um aplicativo, exibir uma mensagem para o usuário ou disparar uma ação pelo aplicativo.
- Use destinos de notificação para: 
    - Transmita mensagens para todos os dispositivos registrados.
    - Envie notificações para públicos com base em informações do dispositivo e propriedades personalizadas.
    - Enviar notificações para usuários específicos.
    - Enviar notificações para dispositivos específicos.
- Faça uso da telemetria avançada em Pushes, dispositivos e erros disponíveis no portal de App Center.
- Obter suporte de plataforma para iOS, Android, macOS, Xamarin, reagir nativo, Unity e Cordova.

**Referências**
- [Inscreva-se com Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introdução ao App Center push](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Hubs de Notificação do Azure
Os [hubs de notificação](/azure/notification-hubs/notification-hubs-push-notification-overview) fornecem um mecanismo de push fácil de usar e escalado horizontalmente. Você pode usá-lo para enviar notificações para qualquer plataforma e de qualquer back-end na nuvem ou local.

**Principais funcionalidades**
- Envie notificações por push para qualquer plataforma de qualquer back-end, na nuvem ou local.
- Envio por push de difusão rápida para milhões de dispositivos móveis com uma única chamada à API.
- Personalize as notificações por Push por cliente, idioma e local.
- Defina e notifique segmentos de clientes dinamicamente.
- Dimensione instantaneamente para milhões de dispositivos móveis.
- Obter suporte de plataforma para iOS, Android, Windows, Kindle e Baidu.
        
**Referências**
- [Portal do Azure](https://portal.azure.com) 
- [Introdução aos hubs de notificação do Azure](/azure/notification-hubs/)
- [Inícios rápidos](/azure/notification-hubs/create-notification-hub-portal)
- [Amostras](/azure/notification-hubs/samples)
