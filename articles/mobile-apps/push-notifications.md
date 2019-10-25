---
title: Importância das notificações por push em seus aplicativos móveis com Visual Studio App Center e hubs de notificação do Azure
description: Saiba mais sobre os serviços como App Center que permitem que você se envolva com seus usuários de aplicativos móveis.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 05a9bc6ccd26aaecd3e0bf615880e0543b8604f7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795916"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Entre em contato com os usuários do seu aplicativo enviando notificações por push 

Independentemente de você estar criando aplicativos de consumidor ou corporativos, você deseja que os usuários usem seu aplicativo ativamente. Geralmente, há ocasiões em que você deseja compartilhar últimas notícias, atualizações de jogos, ofertas empolgantes, atualização de produto ou qualquer informação relevante com seus usuários. Para aumentar o envolvimento com seus usuários e mantê-los de volta para seu aplicativo, você precisa de uma maneira de se comunicar com seus usuários em tempo real.

As notificações por push fornecem uma maneira rápida e eficiente de se comunicar com os usuários do aplicativo. Você pode entrar em contato com seus usuários no momento certo e pode notificá-los de informações desejadas, geralmente em uma caixa de diálogo ou pop-up em um dispositivo móvel, independentemente do que estão fazendo.

## <a name="value-of-push-notifications"></a>Valor de notificações por push
As notificações por push fornecem valor para os usuários finais e para os negócios.

As empresas podem:
- **Comunique-se diretamente com seus usuários** enviando mensagens a eles na plataforma com suporte no momento certo.
- **Aumente o envolvimento do usuário** enviando atualizações e lembretes em tempo real para seus usuários, incentivando-os a se envolver com seu aplicativo regularmente.
- **Mantenha os usuários** e reenvolva-os com usuários inativos que baixaram o aplicativo, mas não o usam para se tornarem ativos novamente.
- **Aumente as taxas de conversão** analisando o comportamento do usuário final, segmentando os usuários finais e aproveitando campanhas baseadas em notificações por push móvel.
- **Promova produtos e serviços** enviando mensagens por push e atualizações oportunas para os usuários.
- **Destinar os usuários** ao envio de mensagens de push personalizadas.

Para usuários de aplicativos, notificações por push:
- **Forneça valor e informações** em tempo real.
- **Lembre os usuários** para usar o aplicativo.

Use os seguintes serviços para habilitar notificações por push em seus aplicativos móveis.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center serviço de envio por push](/appcenter/push/) permite que você se envolva com seus usuários enviando mensagens direcionadas para os usuários do IOS, Android e Windows sem precisar gerenciar o processo de envio de notificações para dispositivos usando o push Notification Services (PNS). Criado com base nos hubs de notificação do Azure, esse serviço elimina as complexidades associadas ao envio manual de notificações, fornecendo um painel poderoso.

**Principais recursos**
- **Envie notificações por push para dispositivos móveis** em várias plataformas.
- Use notificações para enviar dados a um aplicativo, exibir uma mensagem para o usuário ou disparar uma ação pelo aplicativo.
- Destinos de notificação: 
    - Enviar mensagens de difusão para **todos os dispositivos registrados**.
    - Envie notificações para **audiências** criadas com base em informações do dispositivo e propriedades personalizadas.
    - Enviar notificações para **usuários específicos**.
    - Enviar notificações para **dispositivos específicos**.
- **Telemetria avançada** em push, dispositivo, erro estão disponíveis no portal app Center.
- **Suporte à plataforma** -Ios, Android, MacOS, Xamarin, reagir nativo, Unity, Cordova.

**Referências**
- [Inscreva-se com App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introdução ao App Center push](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Hubs de Notificação do Azure
Os [hubs de notificação](/azure/notification-hubs/notification-hubs-push-notification-overview) fornecem um mecanismo de push fácil de usar e escalável que permite enviar notificações para qualquer plataforma e de qualquer back-end (nuvem ou local).

**Principais recursos**
- **Envie notificações por push** para qualquer plataforma de qualquer back-end, na nuvem ou local.
- Envio por push de **transmissão rápida** para milhões de dispositivos móveis com chamada de API única.
- Personalize as notificações por Push por cliente, idioma e local.
- Defina e notifique **segmentos de clientes**dinamicamente.
- **Dimensione instantaneamente** para milhões de dispositivos móveis.
- **Suporte à plataforma** -Ios, Android, Windows, Kindle, Baidu.
        
**Referências**
- [Portal do Azure](https://portal.azure.com) 
- [Introdução aos hubs de notificação do Azure](/azure/notification-hubs/)   
- [Inícios rápidos](/azure/notification-hubs/create-notification-hub-portal)
- [Amostras](/azure/notification-hubs/samples)
