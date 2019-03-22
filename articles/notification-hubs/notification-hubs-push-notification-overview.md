---
title: O que são os Hubs de Notificação do Azure?
description: Saiba como adicionar capacidades de envio de notificações push com os Hubs de Notificação do Azure.
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: da2f9f8c8f9579d315f7df9e050ee07a5fb9cab4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57842506"
---
# <a name="what-is-azure-notification-hubs"></a>O que são os Hubs de Notificação do Azure?

Os Hubs de Notificação do Azure oferecem um motor de envio de escalamento horizontal e utilização fácil que lhe permite enviar notificações para qualquer plataforma (iOS, Android, Windows, Kindle, Baidu, etc.) a partir de qualquer back-end (cloud ou no local). Os Hubs de Notificação funcionam perfeitamente para cenários de utilização empresarial e de consumo. Eis alguns exemplos de cenários:

- Enviar notificações de notícias de última hora para milhões com baixa latência.
- Enviar cupões com base na localização a segmentos de utilizadores interessados.
- Enviar notificações relacionadas com eventos a utilizadores ou grupos para aplicações de multimédia/desporto/finanças/jogos.
- Enviar conteúdos promocionais às aplicações, para interação e comercialização junto dos clientes.
- Notificar os utilizadores de eventos da empresa, como mensagens e itens de trabalho novos.
- Enviar códigos para a autenticação multifator.

## <a name="what-are-push-notifications"></a>O que são as notificações push?

As notificações push são uma forma de comunicação da aplicação para o utilizador, através das quais os utilizadores de aplicações móveis recebem notificações de determinadas informações em que estão interessados, geralmente num pop-up ou numa caixa de diálogo. Regra geral, os utilizadores podem optar por ver ou dispensar as mensagens. Se escolherem ver, a aplicação móvel que comunicou a notificação é aberta.

As notificações push são essenciais para as aplicações de consumidor, para aumentar a interação e a utilização da aplicação, e para as aplicações empresariais, para comunicar informações da empresa atualizadas. É a melhor comunicação da aplicação para o utilizador, pois é eficiente em termos de energia para os dispositivos móveis, flexível para os remetentes das notificações e está disponível quando as aplicações correspondentes não estão ativas.

Para obter mais informações sobre as notificações push para algumas plataformas populares, veja os tópicos seguintes:

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](https://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>Como funcionam as notificações push?

As notificações push são entregues através de infraestruturas específicas da plataforma denominadas *Sistemas de Notificação de Plataforma* (PNS). Oferecem funcionalidades de push barebone para entregar mensagens a dispositivos com um identificador fornecido e não têm uma interface comum. Para enviar uma notificação para todos os clientes através do Android, iOS e versões do Windows de uma aplicação, o desenvolvedor tem de trabalhar com o Apple Push Notification Service(APNS), Firebase Cloud Messaging(FCM) e Service(WNS) de notificação do Windows.

A um nível elevado, eis como o push funciona:

1. A aplicação cliente decide que pretende receber notificações. Por conseguinte,-contacta o PNS correspondente para obter o respetivo identificador de push exclusivo e temporário. O tipo de identificador depende do sistema (por exemplo, o WNS tem URIs, ao passo que o APNS tem tokens).
2. A aplicação cliente armazena este identificador no back-end ou no fornecedor da aplicação.
3. Para enviar uma notificação push, o back-end da aplicação contacta o PNS com o identificador para segmentar uma aplicação cliente específica.
4. O PNS reencaminha a notificação para o dispositivo especificado pelo identificador.

![Fluxo de trabalho das notificações push](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>Os desafios das notificações push

Os PNS são poderosos. Contudo, deixam muito trabalho ao programador da aplicação para implementar até mesmo cenários de notificações push comuns, como difundi-las para utilizadores segmentados.

O envio de notificações exige uma infraestrutura complexa que não está relacionada com a lógica de negócio principal da aplicação. Alguns dos desafios em termos de infraestrutura são:

- **Dependência da plataforma**
  - O back-end tem de ter uma lógica dependente da plataforma, complexa e de manutenção difícil, para enviar notificações a dispositivos em várias plataformas, uma vez que os PNS não estão unificados.
- **Dimensionamento**
  - De acordo com as diretrizes do PNS, os tokens dos dispositivos têm de ser atualizados sempre que a aplicação é iniciada. O back-end está a lidar com uma grande quantidade de tráfego e acesso a bases de dados apenas para manter os tokens atualizados. Se o número de dispositivos aumentar para centenas e milhares de milhões, o custo de criar e manter essa infraestrutura é enorme.
  - A maioria dos PNS não suporta a difusão para vários dispositivos. Uma simples difusão para um milhão de dispositivos resulta num milhão de chamadas para os PNS. Dimensionar esta quantidade de tráfico com latência mínima não é algo trivial.
- **Encaminhamento**
  - Embora os PNS proporcionem uma forma de enviar mensagens para dispositivos, a maioria das notificações das aplicações são segmentadas para os utilizadores ou para grupos de interesses. O back-end tem de manter um registo para associar os dispositivos aos grupos de interesses, utilizadores, propriedades, etc. Esta sobrecarga soma-se ao tempo de comercialização e aos custos de manutenção das aplicações.

## <a name="why-use-azure-notification-hubs"></a>Porquê utilizar os Hubs de Notificação?

Os Hubs de Notificação eliminam todas as complexidades associadas ao envio de notificações manual a partir do back-end da sua aplicação. A infraestrutura de notificações push de escalamento horizontal e multiplataforma reduz a programação relacionada com pushes e simplifica o seu back-end. Com os Hubs de Notificação, os seus dispositivos são meramente responsáveis pelo registo dos respetivos identificadores do PNS hum hub, ao passo que o back-end envia mensagens aos utilizadores ou grupos de interesse, conforme mostrado na seguinte figura:

![Diagrama dos Hubs de Notificação](./media/notification-hubs-overview/notification-hub-diagram.png)

Os Hubs de Notificação são o seu motor pronto a usar e oferecem as seguintes vantagens:

- **Várias plataformas**
  - Suporte para todas as principais plataformas de push, incluindo iOS, Android, Windows, Kindle e Baidu.
  - Uma interface comum para enviar para todas plataformas em formatos específicos para uma plataforma ou formatos independentes de plataforma sem qualquer trabalho para uma plataforma específica.
  - Gestão de identificadores dos dispositivos num único local.
- **Vários back-ends**
  - Cloud ou no local
  - .NET, Node.js, Java, etc.
- **Conjunto de padrões de entrega rico**
  - Difundir para uma ou várias plataformas: Pode instantaneamente de difusão para milhões de dispositivos entre plataformas com uma única chamada de API.
  - Envie para o dispositivo: Pode direcionar as notificações push para dispositivos individuais.
  - Envie para o utilizador: Recursos de etiquetas e modelos ajudam a alcançar a todos os dispositivos de várias plataformas de um utilizador.
  - Envie para o segmento com as etiquetas dinâmicas: Funcionalidade de etiquetas ajuda-o a dispositivos de segmento e push aos mesmos, de acordo com suas necessidades, se estão a enviar para um segmento ou uma expressão de segmentos (por exemplo, de Active Directory e reside em Seattle não novo utilizador). Em vez de estar limitado a publicação-subscrição, pode atualizar as etiquetas dos dispositivos em qualquer lugar e em qualquer altura.
  - Push localizadas: Funcionalidade de modelos ajuda a alcançar uma localização sem afetar o código de back-end.
  - Push silenciosa: Pode ativar o padrão de push a solicitação ao enviar notificações silenciosas para dispositivos e acionando-los para concluir determinadas extrai ou ações.
  - Push agendado: Pode agendar para enviar notificações em qualquer altura.
  - O Direct push: Pode ignorar o registo de dispositivos com o serviço de Hubs de notificação e diretamente do batch push para uma lista de identificadores de dispositivo.
  - Push personalizadas: Variáveis de push de dispositivo personalizada ajuda a que envia específicos do dispositivo de notificações push com pares de chave-valor personalizados.
- **Telemetria avançada**
  - Está disponível telemetria de pushes, dispositivos, erros e operações no portal do Azure e programaticamente.
  - A Telemetria por Mensagem regista cada push a partir da chamada de pedido inicial ao serviço Hubs de Notificação, processando os pushes por lotes corretamente.
  - O Feedback do Sistema de Notificação de Plataforma comunica todos os feedbacks do Sistema de Notificação de Plataforma, para ajudar na depuração.
- **Escalabilidade**
  - Enviar mensagens rápidas para milhões de dispositivos sem alterar a arquitetura ou fragmentar os dispositivos.
- **Segurança**
  - Segredo de Acesso Partilhado (SAS) ou autenticação federada.

## <a name="integration-with-app-service-mobile-apps"></a>Integração com as Aplicações Móveis do Serviço de Aplicações

Para facilitar uma experiência totalmente integrada e unificadora em todos os serviços do Azure, as [Aplicações Móveis do Serviço de Aplicações](../app-service-mobile/app-service-mobile-value-prop.md) têm suporte incorporado para notificações push com os Hubs de Notificação. As [Aplicações Móveis do Serviço de Aplicações](../app-service-mobile/app-service-mobile-value-prop.md) oferecem uma plataforma de desenvolvimento de aplicações móveis altamente dimensionável e globalmente disponível para Programadores Empresariais e Integradores de Sistemas, que fornece um conjunto completo de capacidades para programadores móveis.

Os programadores de Mobile Apps podem utilizar Notification Hubs com o fluxo de trabalho seguinte:

1. Obter o identificador PNS do dispositivo
2. Registar o dispositivo nos Hubs de Notificação através da conveniente API de registo do SDK de Cliente das Aplicações Móveis

    > [!NOTE]
    > Tenha em atenção que, por motivos de segurança, as Mobile Apps eliminam todas as etiquetas ao registarem-se. Trabalhar com Notification Hubs diretamente no seu back-end para associar etiquetas a dispositivos.
3. Enviar notificações a partir do seu back-end de aplicação com Notification Hubs

Aqui estão algumas das conveniências que esta integração proporciona a programadores:

- **SDKs cliente das Mobile Apps**: Estes SDKs multiplataforma fornecem APIs simples para registar e falar com o Notification Hub ligado automaticamente à aplicação móvel. Os programadores não precisam de analisar a fundo as credenciais dos Notification Hubs e trabalham com um serviço adicional.
  - *Enviar para o utilizador*: Os SDKs etiquetam automaticamente o dispositivo especificado com ID de utilizador autenticado de Mobile Apps para ativar o push para o cenário do utilizador.
  - *Enviar para o dispositivo*: Os SDKs utilizam automaticamente o ID de Instalação das Mobile Apps como GUID para se registarem nos Notification Hubs, poupando aos programadores o trabalho de manterem GUIDs para vários serviços.
- **Modelo de instalação**: As Mobile Apps funcionam com o modelo push mais recente dos Notification Hubs para representar todas as propriedades push associadas a um dispositivo numa instalação JSON que se alinha com os Serviço de Notificações Push e é fácil de utilizar.
- **Flexibilidade**: Os programadores podem sempre optar por trabalhar diretamente com Notification Hubs, mesmo com a integração instalada.
- **Experiência integrada no [portal do Azure](https://portal.azure.com)**: O Push como uma capacidade está representado visualmente em Mobile Apps e os programadores podem trabalhar facilmente com o Notification Hub associado através das Mobile Apps.

## <a name="next-steps"></a>Passos Seguintes

Introdução à criação e utilização de um hub de notificação, seguindo o [Tutorial: Notificações push para aplicações móveis](notification-hubs-android-push-notification-google-fcm-get-started.md).

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
