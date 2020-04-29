---
title: Diagnóstico deixou cair notificações em Centros de Notificação Azure
description: Saiba diagnosticar questões comuns com notificações abandonadas em Centros de Notificação Azure.
services: notification-hubs
documentationcenter: Mobile
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 02/25/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/04/2019
ms.openlocfilehash: 1f3c16e6fe1855cf7882d83e620c70d15ce3cb92
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77657588"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Diagnóstico deixou cair notificações em Centros de Notificação Azure

Uma pergunta comum sobre o Azure Notification Hubs é como resolver problemas quando as notificações de uma aplicação não aparecem nos dispositivos do cliente. Os clientes querem saber onde e por que as notificações foram retiradas, e como corrigir o problema. Este artigo identifica por que razão as notificações podem ser retiradas ou não recebidas por dispositivos. Também explica como determinar a causa da raiz.

É fundamental perceber primeiro como os Centros de Notificação empurram notificações para um dispositivo.

![Arquitetura De Centros de Notificação][0]

Num fluxo típico de notificação de envio, a mensagem é enviada do fundo da *aplicação* para centros de notificação. O Notification Hubs processa todas as inscrições. Tem em conta as etiquetas configuradas e as expressões de etiquetas para determinar os alvos. Os alvos são os registos que precisam de receber a notificação push. Estes registos podem abranger qualquer uma das nossas plataformas suportadas: Android, Baidu (dispositivos Android na China), Fire OS (Amazon) iOS, Windows e Windows Phone.

Com os alvos estabelecidos, o Notification Hubs empurra notificações para o serviço de *notificação push* para a plataforma do dispositivo. Exemplos incluem o serviço de Notificação push apple (APNs) para iOS e macOS, e Mensagens cloud firebase (FCM) para dispositivos Android. O Notification Hubs empurra notificações divididas em vários lotes de registos. Autentica-se com o respetivo serviço de notificação push, com base nas credenciais que definiu no portal Azure, no âmbito do **Configure Notification Hub**. O serviço de notificação push reencaminha então as notificações para os *respetivos dispositivos clientes*.

A última etapa da entrega de notificações é entre o serviço de notificação push da plataforma e o dispositivo. A entrega de notificação pode falhar em qualquer uma das quatro fases do processo de notificação push (cliente, back end da aplicação, Centros de Notificação e serviço de notificação push da plataforma). Para mais informações sobre a arquitetura Notification Hubs, consulte a visão geral dos Centros de [Notificação.]

Pode ocorrer uma não entrega de notificações durante a fase inicial de teste/encenação. As notificações retiradas nesta fase podem indicar um problema de configuração. Se ocorrer uma falha na entrega de notificações na produção, algumas ou todas as notificações poderão ser retiradas. Neste caso, indica-se uma aplicação mais profunda ou um problema de padrões de mensagens.

A secção seguinte analisa cenários em que as notificações podem ser retiradas, que vão de comum a raros.

## <a name="notification-hubs-misconfiguration"></a>Configuração errada dos Centros de Notificação

Para enviar notificações para o respetivo serviço de notificação push, os Centros de Notificação devem autenticar-se no contexto da sua aplicação. Tem de criar uma conta de desenvolvimento com o serviço de notificação da plataforma alvo (Microsoft, Apple, Google, etc.). Em seguida, deve registar a sua aplicação no SISTEMA onde obtém um símbolo ou chave que utiliza para trabalhar com o PNS-alvo.

Deve adicionar credenciais de plataforma ao portal Azure. Se não houver notificações a chegar ao dispositivo, o primeiro passo é garantir que as credenciais corretas estão configuradas nos Centros de Notificação. As credenciais devem corresponder à aplicação criada sob uma conta de desenvolvedor específico da plataforma.

Para obter instruções passo a passo para concluir este processo, consulte Iniciar com Hubs de [Notificação Azure].

Aqui estão algumas configurações comuns para verificar:

### <a name="notification-hub-name-location"></a>Localização do nome do hub de notificação

Certifique-se de que o seu nome de centro de notificação (sem tipografia) é o mesmo em cada um destes locais:

* Onde se regista no cliente
* Onde envia notificações a partir da parte de trás
* Onde configuraas as credenciais do serviço de notificação push

Certifique-se de que utiliza as cordas de configuração de assinatura de acesso partilhado corretas no cliente e na parte de trás da aplicação. Geralmente, deve utilizar o **DefaultListenSharedAccessSignature** no cliente e **o DefaultFullSharedAccessSignature** na extremidade traseira da aplicação. Isto concede permissões para enviar notificações para Centros de Notificação.

### <a name="apn-configuration"></a>Configuração APN

Deve manter dois centros diferentes: um para produção e outro para testes. Tem de carregar o certificado que utiliza num ambiente de caixa de areia para um hub separado do que o certificado/hub que utilizará na produção. Não tente enviar diferentes tipos de certificados para o mesmo centro. Causará falhas de notificação.

Se enviar inadvertidamente diferentes tipos de certificados para o mesmo centro, deve apagar o hub e começar de novo com um novo hub. Se por alguma razão não conseguir apagar o hub, deve pelo menos apagar todos os registos existentes do centro.

### <a name="fcm-configuration"></a>Configuração fcm

1. Certifique-se de que a chave do *servidor* obtida a partir da Firebase corresponde à chave do servidor registada no portal Azure.

   ![Chave do servidor da base de fogo][3]

2. Certifique-se de que configurao **o ID** do Projeto no cliente. Pode obter o valor para id do **Projeto** no painel firebase.

   ![Id do Projeto Base de Fogo][1]

## <a name="application-issues"></a>Questões de aplicação

### <a name="tags-and-tag-expressions"></a>Tags e expressões de etiquetas

Se utilizar tags ou tags para segmentar o seu público, é possível que quando envia a notificação, não seja encontrado nenhum alvo. Este erro baseia-se nas etiquetas especificadas ou nas expressões de etiquetas na sua chamada de envio.

Reveja as suas inscrições para garantir que as etiquetas correspondem quando envia uma notificação. Em seguida, verifique o recibo de notificação apenas dos clientes que possuam esses registos.

Por exemplo, suponha que todos os seus registos com Centros de Notificação utilizem a etiqueta "Política". Se enviar uma notificação com a etiqueta "Sports", a notificação não será enviada para qualquer dispositivo. Um caso complexo pode envolver expressões de etiquetas onde se registou usando "Tag A" *ou* "Tag B", mas apontou "Tag A && Tag B". A secção de dicas de autodiagnóstico mais tarde no artigo mostra-lhe como rever as suas inscrições e as suas etiquetas.

### <a name="template-issues"></a>Problemas de modelo

Se utilizar modelos, certifique-se de que segue as diretrizes descritas nos [modelos].

### <a name="invalid-registrations"></a>Registos inválidos

Se o centro de notificação foi configurado corretamente e as etiquetas ou expressões de etiquetas foram utilizadas corretamente, são encontrados alvos válidos. As notificações devem ser enviadas para estes alvos. Os Centros de Notificação disparam vários lotes de processamento em paralelo. Cada lote envia mensagens para um conjunto de registos.

> [!NOTE]
> Dado que os Centros de Notificação processam lotes em paralelo, a ordem pela qual as notificações são entregues não está garantida.

O Notification Hubs está otimizado para um modelo de entrega de mensagens "no máximo". Tentamos a duplicação, para que não sejam entregues notificações mais de uma vez a um dispositivo. As inscrições são verificadas para garantir que apenas uma mensagem é enviada por identificador de dispositivo antes de ser enviada para o serviço de notificação push.

Cada lote é enviado para o serviço de notificação push, que por sua vez aceita e valida as inscrições. Durante este processo, é possível que o serviço de notificação push detete um erro com um ou mais registos num lote. O serviço de notificação push devolve então um erro aos Centros de Notificação e o processo para. O serviço de notificação push deixa cair completamente o lote. Isto é especialmente verdade com APNs, que usa um protocolo de fluxo TCP.

Neste caso, o registo de avarias é removido da base de dados. Em seguida, retry entrega de notificação para o resto dos dispositivos nesse lote.

Para obter mais informações de erro sobre a tentativa de entrega falhada contra um registo, pode utilizar os Centros de Notificação REST APIs [Por Telemetria de Mensagem: Obtenha telemetria de mensagens de notificação](https://docs.microsoft.com/rest/api/notificationhubs/get-notification-message-telemetry) e [feedback pNS](https://msdn.microsoft.com/library/azure/mt705560.aspx). Para obter o código da amostra, consulte o [exemplo Enviar REST](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/).

## <a name="push-notification-service-issues"></a>Problemas de serviço de notificação push

Após o serviço de notificação push receber a notificação, entrega a notificação ao dispositivo. Neste momento, o Notification Hubs não tem qualquer controlo sobre a entrega da notificação ao dispositivo.

Como os serviços de notificação da plataforma são robustos, as notificações tendem a chegar aos dispositivos em poucos segundos. Se o serviço de notificação push estiver a acelerar, o Notification Hubs aplica uma estratégia exponencial de back-off. Se o serviço de notificação push permanecer inacessível por 30 minutos, há uma política em vigor para expirar e deixar as mensagens permanentemente.

Se um serviço de notificação push tentar entregar uma notificação, mas o dispositivo estiver offline, a notificação é armazenada pelo serviço de notificação push. Está guardado por um período limitado de tempo. A notificação é entregue ao dispositivo quando o dispositivo estiver disponível.

Cada aplicação armazena apenas uma notificação recente. Se forem enviadas várias notificações enquanto um dispositivo estiver offline, cada nova notificação faz com que a última seja descartada. Manter apenas a mais recente notificação chama-se *acoligar-se* em APNs e *colapsar* na FCM. (A FCM usa uma chave em colapso.) Quando o dispositivo permanece offline por muito tempo, as notificações que foram armazenadas para o dispositivo são descartadas. Para mais informações, consulte a visão geral da [APNs] e [sobre as mensagens da FCM].

Com centros de notificação, pode passar uma chave de aparação através de um cabeçalho HTTP utilizando a API de Notificação de Envio genérica. Por exemplo, para o .NET SDK, utilizaria `SendNotificationAsync`. A API de Notificação de Envio também leva cabeçalhos HTTP que são passados como é o respetivo serviço de notificação push.

## <a name="self-diagnosis-tips"></a>Dicas de autodiagnóstico

Aqui estão os caminhos para diagnosticar a causa principal das notificações retiradas nos Centros de Notificação.

### <a name="verify-credentials"></a>Verificar credenciais

#### <a name="push-notification-service-developer-portal"></a>Portal de desenvolvedor de serviços de notificação push

Verifique as credenciais no respetivo portal de desenvolvedores de serviços de notificação push (APNs, FCM, Windows Notification Service, e assim por diante). Para mais informações, consulte [Tutorial: Envie notificações para aplicações da Plataforma Universal windows utilizando hubs de notificação do Azure](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification).

#### <a name="azure-portal"></a>Portal do Azure

Para rever e combinar as credenciais com as obtidas no portal de desenvolvedores de serviços de notificação push, aceda ao separador Políticas de **Acesso** no portal Azure.

![Políticas de acesso ao portal Azure][4]

### <a name="verify-registrations"></a>Verificar registos

#### <a name="visual-studio"></a>Visual Studio

No Estúdio Visual, pode ligar-se ao Azure através do Server Explorer para visualizar e gerir vários serviços Azure, incluindo Centros de Notificação. Este atalho é principalmente útil para o seu ambiente de desenvolvimento/teste.

![Explorador de servidor de estúdio visual][9]

![Explorador de Servidores](media/notification-hubs-push-notification-fixer/vsserverexplorer2.png)

Pode ver e gerir todas as inscrições no seu centro. As inscrições podem ser categorizadas por plataforma, registo nativo ou modelo, etiqueta, identificador de serviço de notificação push, ID de registo e data de validade. Também pode editar uma inscrição nesta página. É especialmente útil para editar etiquetas.

Clique no seu centro de notificação no **Server Explorer**e selecione **Diagnosticar**. 

![Visual Studio Server Explorer: Menu de diagnóstico](./media/notification-hubs-push-notification-fixer/diagnose-menu.png)

Veja a seguinte página:

![Estúdio Visual: Página de diagnóstico](./media/notification-hubs-push-notification-fixer/diagnose-page.png)

Mude para a página registos do **dispositivo:**

![Estúdio Visual: Registos de Dispositivos](./media/notification-hubs-push-notification-fixer/VSRegistrations.png)

Pode utilizar a página **'Enviar de Teste'** para enviar uma mensagem de notificação de teste:

![Estúdio Visual: Envio de teste](./media/notification-hubs-push-notification-fixer/test-send-vs.png)

> [!NOTE]
> Utilize o Visual Studio para editar registos apenas durante o desenvolvimento/teste, e com um número limitado de inscrições. Se precisar de editar os seus registos a granel, considere utilizar a funcionalidade de registo de exportação e importação descrita em [Como: Exportar e Modificar Registos a granel](https://msdn.microsoft.com/library/dn790624.aspx).

#### <a name="service-bus-explorer"></a>Explorador do Service Bus

Muitos clientes usam [o Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) para visualizar e gerir os seus centros de notificação. Service Bus Explorer é um projeto de código aberto. 

### <a name="verify-message-notifications"></a>Verificar notificações de mensagens

#### <a name="azure-portal"></a>Portal do Azure

Para enviar uma notificação de teste aos seus clientes sem ter um serviço de volta a acabar e a funcionar, em **suporte + RESOLUÇÃO DE PROBLEMAS,** selecione **Test Send**.

![Teste Enviar funcionalidade em Azure][7]

#### <a name="visual-studio"></a>Visual Studio

Também pode enviar notificações de teste do Visual Studio.

![Teste Enviar funcionalidade no Estúdio Visual][10]

Para obter mais informações sobre a utilização de Centros de Notificação com o Visual Studio Server Explorer, consulte estes artigos:

* [Como visualizar os registos de dispositivos para centros de notificação](https://docs.microsoft.com/previous-versions/windows/apps/dn792122(v=win.10))
* [Deep dive: Visual Studio 2013 Update 2 RC e Azure SDK 2.3]
* [Anunciando o lançamento do Visual Studio 2013 Update 3 e Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Debug falhou notificações e revisão resultado da notificação

#### <a name="enabletestsend-property"></a>Propriedade EnableTestSend

Quando envia uma notificação via Notification Hubs, a notificação está inicialmente na fila. O Notification Hubs determina os alvos corretos e, em seguida, envia a notificação para o serviço de notificação push. Se estiver a utilizar a API REST ou qualquer um dos SDKs do cliente, a devolução da sua chamada de envio significa apenas que a mensagem está em fila com Centros de Notificação. Não fornece informações sobre o que aconteceu quando o Notification Hubs acabou por enviar a notificação para o serviço de notificação push.

Se a sua notificação não chegar ao dispositivo cliente, pode ter ocorrido um erro quando o Notification Hubs tentou entregá-lo ao serviço de notificação push. Por exemplo, o tamanho da carga útil pode exceder o máximo permitido pelo serviço de notificação push, ou as credenciais configuradas nos Centros de Notificação podem ser inválidas.

Para obter informações sobre os erros do serviço de notificação push, pode utilizar a propriedade [EnableTestSend.] Esta propriedade é ativada automaticamente quando envia mensagens de teste do portal ou cliente do Estúdio Visual. Você pode usar esta propriedade para ver informações detalhadas de depuração e também através de APIs. Atualmente, pode usá-lo no .NET SDK. Será adicionado a todos os SDKs clientes eventualmente.

Para utilizar `EnableTestSend` a propriedade com a chamada REST, anexar um parâmetro de corda de consulta chamado *teste* até ao final da sua chamada de envio. Por exemplo:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>exemplo de SDK .NET

Aqui está um exemplo de usar o .NET SDK para enviar uma notificação pop-up nativa (torrada):

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

No final da `result.State` execução, `Enqueued`simplesmente afirma. Os resultados não fornecem qualquer informação sobre o que aconteceu com a sua notificação push.

Em seguida, você `EnableTestSend` pode usar a propriedade Boolean. Utilize `EnableTestSend` a propriedade enquanto inicia `NotificationHubClient` para obter um estado detalhado sobre os erros de serviço de notificação push que ocorrem quando a notificação é enviada. A chamada de envio leva tempo adicional para regressar porque primeiro precisa de Centros de Notificação para entregar a notificação ao serviço de notificação push.

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

#### <a name="sample-output"></a>Resultado da amostra

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Esta mensagem indica que as credenciais configuradas nos Centros de Notificação são inválidas ou que há um problema com os registos no centro. Elimine este registo e deixe o cliente recriar o registo antes de enviar a mensagem.

> [!NOTE]
> O uso `EnableTestSend` da propriedade é fortemente estrangulado. Utilize esta opção apenas num ambiente de desenvolvimento/teste e com um conjunto limitado de inscrições. As notificações de depuração são enviadas para apenas 10 dispositivos. Há também um limite no processamento de envios de depurados, a 10 por minuto.

### <a name="review-telemetry"></a>Rever telemetria

#### <a name="azure-portal"></a>Portal do Azure

No portal, pode obter uma visão geral rápida de toda a atividade no seu centro de notificação.

1. No separador **Overview,** pode ver uma visão agregada de registos, notificações e erros por plataforma.

   ![Painel de visão geral do Hubs de notificação][5]

2. No separador **Monitor,** pode adicionar muitas outras métricas específicas da plataforma para um aspeto mais profundo. Pode analisar especificamente os erros que são devolvidos quando o Notification Hubs tenta enviar a notificação para o serviço de notificação push.

   ![Registo de atividade do portal Azure][6]

3. Comece por rever **mensagens recebidas,** **operações de registo**e **notificações bem sucedidas.** Em seguida, vá ao separador por plataforma para rever erros específicos do serviço de notificação push.

4. Se as definições de autenticação do seu centro de notificação estiverem incorretas, aparece o Erro de **Autenticação PNS** da mensagem. É uma boa indicação para verificar as credenciais do serviço de notificação push.

#### <a name="programmatic-access"></a>Acesso programático

Para obter mais informações sobre acesso programático, consulte [acesso programático.](https://docs.microsoft.com/previous-versions/azure/azure-services/dn458823(v=azure.100))

> [!NOTE]
> Várias características relacionadas com a telemetria, como a exportação e importação de registos e acesso à telemetria via APIs, estão disponíveis apenas no nível de serviço Standard. Se tentar utilizar estas funcionalidades a partir do nível de serviço Gratuito ou Básico, receberá uma mensagem de exceção se utilizar o SDK. Obterá um erro HTTP 403 (Proibido) se utilizar as funcionalidades diretamente a partir das APIs REST.
>
> Para utilizar funcionalidades relacionadas com a telemetria, certifique-se primeiro no portal Azure de que está a utilizar o nível de serviço Standard.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-push-notification-fixer/Architecture.png
[1]: ./media/notification-hubs-push-notification-fixer/FCMConfigure.png
[3]: ./media/notification-hubs-push-notification-fixer/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-push-notification-fixer/PortalDashboard.png
[6]: ./media/notification-hubs-push-notification-fixer/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-push-notification-fixer/VSRegistrations.png
[9]: ./media/notification-hubs-push-notification-fixer/vsserverexplorer.png
[10]: ./media/notification-hubs-push-notification-fixer/VSTestNotification.png

<!-- LINKS -->
[Visão geral dos Centros de Notificação]: notification-hubs-push-notification-overview.md
[Começar com hubs de notificação Azure]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Modelos]: https://msdn.microsoft.com/library/dn530748.aspx
[Visão geral das APNs]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Sobre as mensagens da FCM]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Deep dive: Visual Studio 2013 Update 2 RC e Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Anunciando o lançamento do Visual Studio 2013 Update 3 e Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx
