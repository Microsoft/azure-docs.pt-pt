---
title: O que são os Hubs de Notificação do Azure?
description: Saiba como adicionar capacidades de envio de notificações push com os Hubs de Notificação do Azure.
author: sethmanheim
manager: femila
editor: tjsomasundaram
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 02/20/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 04/30/2019
ms.openlocfilehash: dad71d473ecb6107a5cf0d8f5953667f07bfce5c
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526778"
---
# <a name="what-is-azure-notification-hubs"></a>O que são os Hubs de Notificação do Azure?

Os Hubs de Notificação Azure fornecem um motor push fácil de usar e dimensionado que lhe permite enviar notificações para qualquer plataforma (iOS, Android, Windows, etc.) a partir de qualquer back-end (cloud ou on-site). Os Hubs de Notificação funcionam perfeitamente para cenários de utilização empresarial e de consumo. Eis alguns exemplos de cenários:

- Enviar notificações de notícias de última hora para milhões com baixa latência.
- Enviar cupões com base na localização a segmentos de utilizadores interessados.
- Enviar notificações relacionadas com eventos a utilizadores ou grupos para aplicações de multimédia/desporto/finanças/jogos.
- Enviar conteúdos promocionais às aplicações, para interação e comercialização junto dos clientes.
- Notifique os utilizadores de eventos empresariais, tais como novas mensagens e itens de trabalho.
- Enviar códigos para a autenticação multifator.

## <a name="what-are-push-notifications"></a>O que são as notificações push?

As notificações push são uma forma de comunicação app-a-utilizador onde os utilizadores de aplicações móveis são notificados de determinadas informações desejadas, geralmente numa caixa pop-up ou de diálogo num dispositivo móvel. Os utilizadores geralmente optam por ver ou descartar a mensagem; escolhendo o primeiro abre a aplicação móvel que comunicou a notificação. Algumas notificações são silenciosas - entregues nos bastidores para que a app processe e decida o que fazer.

As notificações push são essenciais para as aplicações de consumidor, para aumentar a interação e a utilização da aplicação, e para as aplicações empresariais, para comunicar informações da empresa atualizadas. É a melhor comunicação app-a-utilizador porque é energeticamente eficiente para dispositivos móveis, flexível para os remetentes de notificações, e disponível quando as aplicações correspondentes não estão ativas.

> [!NOTE]
> Os Hubs de Notificação Azure não suportam notificações push Voice Over Internet Protocol (VOIP).

Para obter mais informações sobre as notificações push para algumas plataformas populares, veja os tópicos seguintes:

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](https://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-do-push-notifications-work"></a>Como funcionam as notificações push?

As notificações push são entregues através de infraestruturas específicas da plataforma denominadas *Sistemas de Notificação de Plataforma* (PNS). Oferecem funcionalidades básicas de push para entregar uma mensagem a um dispositivo com um cabo fornecido, e não têm interface comum. Para enviar uma notificação a todos os clientes através das versões Android, iOS e Windows de uma aplicação, o desenvolvedor deve trabalhar separadamente com o Apple Push Notification Service (APNS), Firebase Cloud Messaging (FCM) e Windows Notification Service (WNS).

A um nível elevado, eis como o push funciona:

1. Uma aplicação quer receber uma notificação, pelo que contacta o PNS para a plataforma-alvo em que a aplicação está a funcionar e solicita um manípulo único e temporário. O tipo de pega depende do sistema (por exemplo, o WNS utiliza URIs enquanto os APNS usam fichas).
2. A aplicação do cliente armazena este cabo no backend ou fornecedor da aplicação.
3. Para enviar uma notificação push, o backend da aplicação contacta o PNS usando o punho para direcionar uma aplicação específica do cliente.
4. O PNS reencaminha a notificação para o dispositivo especificado pelo identificador.

![Fluxo de trabalho das notificações push](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>Os desafios das notificações push

Os PNS são poderosos. Contudo, deixam muito trabalho ao programador da aplicação para implementar até mesmo cenários de notificações push comuns, como difundi-las para utilizadores segmentados.

O envio de notificações push requer infraestruturas complexas que não estão relacionadas com a principal lógica de negócio da aplicação. Alguns dos desafios da infraestrutura são:

- **Dependência da plataforma**
  - O backend requer uma lógica complexa e difícil de manter dependente da plataforma para enviar notificações a dispositivos em várias plataformas, uma vez que os PNSes não estão unificados.
- **Dimensionar**
  - De acordo com as diretrizes do PNS, as fichas do dispositivo devem ser renovadas em todos os lançamentos da aplicação. O backend lida com uma grande quantidade de tráfego e acesso à base de dados apenas para manter os tokens atualizados. Quando o número de dispositivos aumenta para centenas, milhares ou milhões, o custo de criação e manutenção desta infraestrutura é enorme.
  - A maioria dos PNS não suporta a difusão para vários dispositivos. Uma simples difusão para um milhão de dispositivos resulta num milhão de chamadas para os PNS. Dimensionar esta quantidade de tráfico com latência mínima não é algo trivial.
- **Encaminhamento**
  - Embora os PNSes forneçam uma forma de enviar mensagens para dispositivos, a maioria das notificações de aplicações são direcionadas para utilizadores ou grupos de interesse. O backend deve manter um registo para associar dispositivos a grupos de interesses, utilizadores, propriedades, etc. Este custo aumenta o tempo para os custos de mercado e manutenção de uma app.

## <a name="why-use-azure-notification-hubs"></a>Porquê utilizar os Hubs de Notificação?

O Notification Hubs elimina todas as complexidades associadas ao envio de notificações push por conta própria a partir do seu backend de aplicações. A infraestrutura de notificações push de escalamento horizontal e multiplataforma reduz a programação relacionada com pushes e simplifica o seu back-end. Com os Hubs de Notificação, os seus dispositivos são meramente responsáveis pelo registo dos respetivos identificadores do PNS hum hub, ao passo que o back-end envia mensagens aos utilizadores ou grupos de interesse, conforme mostrado na seguinte figura:

![Diagrama dos Hubs de Notificação](./media/notification-hubs-overview/notification-hub-diagram.png)

O Notification Hubs é o seu motor push pronto a utilizar com as seguintes vantagens:

- **Várias plataformas**
  - Suporte para todas as grandes plataformas push.
  - Uma interface comum para enviar para todas plataformas em formatos específicos para uma plataforma ou formatos independentes de plataforma sem qualquer trabalho para uma plataforma específica.
  - Gestão de identificadores dos dispositivos num único local.
- **Vários back-ends**
  - Nuvem ou no local.
  - .NET, Node.js, Java, Python, etc.
- **Conjunto de padrões de entrega rico**
  - Transmissão para uma ou mais plataformas: Pode transmitir instantaneamente para milhões de dispositivos através de plataformas com uma única chamada API.
  - Enviar para dispositivo: pode segmentar notificações para dispositivos individuais.
  - Empurre para o utilizador: Tags e modelos ajudam-no a alcançar todos os dispositivos cross-platform para um utilizador.
  - Empurre para segmentar com tags dinâmicas: A funcionalidade de tags ajuda-o a segmentar dispositivos e a empurrá-los de acordo com as suas necessidades, quer esteja a enviar para um segmento ou uma expressão de segmentos (por exemplo, ativo e vive em Seattle NÃO novo utilizador). Em vez de se restringir a inscrição na publicação, pode atualizar as etiquetas do dispositivo em qualquer lugar e a qualquer hora.
  - Impulso localizado: A função de modeloajuda a alcançar a localização sem afetar o código de backend.
  - Push silencioso: pode ativar o padrão push-to-pull ao enviar notificações silenciosas para os dispositivos e acioná-las para concluir determinadas extrações ou ações.
  - Push agendado: Pode agendar notificações a qualquer momento.
  - Push direto: pode ignorar o registo dos dispositivos no serviço Hubs de Notificação e enviar por lotes diretamente para uma lista de identificadores de dispositivos.
  - Push personalizado: As variáveis push do dispositivo ajudam-no a enviar notificações personalizadas de push personalizadas com pares de valor-chave personalizados.
- **Telemetria avançada**
  - O impulso geral, o dispositivo, o erro e a telemetria de operação estão disponíveis tanto no portal Azure como programáticamente.
  - A telemetria por mensagem rastreia cada impulso da sua chamada inicial de pedido para o serviço Centros de Notificação enviando com sucesso os pushs.
  - O feedback do Sistema de Notificação da Plataforma comunica todos os feedbacks dos PNSes para ajudar na depuração.
- **Escalabilidade**
  - Envie mensagens rápidas a milhões de dispositivos sem re-arquitectar ou sharding de dispositivos.
- **Segurança**
  - Segredo de Acesso Partilhado (SAS) ou autenticação federada.

## <a name="next-steps"></a>Passos seguintes

Seguir [Tutorial: Enviar notificações para aplicações móveis](notification-hubs-android-push-notification-google-fcm-get-started.md) para aprender a criar e a utilizar hubs de notificação.

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png

[How customers are using Notification Hubs]: https://azure.microsoft.com/services/notification-hubs
[Notification Hubs tutorials and guides]: https://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
[Android]: https://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
[Windows Universal]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
[Windows Phone]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
[Kindle]: https://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
[Xamarin.iOS]: https://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
[Xamarin.Android]: https://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
[Microsoft.WindowsAzure.Messaging.NotificationHub]: https://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
[Microsoft.ServiceBus.Notifications]: https://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
[App Service Mobile Apps]: https://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop/
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Azure portal]: https://portal.azure.com
[tags]: (https://msdn.microsoft.com/library/azure/dn530749.aspx)
