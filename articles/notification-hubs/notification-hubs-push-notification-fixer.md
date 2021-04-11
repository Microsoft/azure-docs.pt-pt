---
title: Diagnóstico deixou cair notificações em Azure Notification Hubs
description: Saiba como diagnosticar problemas comuns com notificações deixadas em Azure Notification Hubs.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 787cf922fcee0ee613fc0874a490830da9adf38a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455030"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Diagnóstico deixou cair notificações em Azure Notification Hubs

Uma questão comum sobre os Hubs de Notificação Azure é como resolver problemas quando as notificações de uma aplicação não aparecem nos dispositivos dos clientes. Os clientes querem saber onde e por que as notificações foram retiradas, e como corrigir o problema. Este artigo identifica por que razão as notificações podem ser retiradas ou não ser recebidas por dispositivos. Também explica como determinar a causa da raiz.

É fundamental primeiro entender como os Centros de Notificação empurram as notificações para um dispositivo.

![Arquitetura de Centros de Notificação][0]

Num fluxo típico de notificação de envio, a mensagem é enviada do final da *aplicação* para os Centros de Notificação. Os Centros de Notificação processam todas as inscrições. Tem em conta as etiquetas configuradas e as expressões de etiquetas para determinar os alvos. Os alvos são os registos que precisam de receber a notificação push. Estas inscrições podem abranger qualquer uma das nossas plataformas suportadas: Android, Baidu (dispositivos Android na China), Fire OS (Amazon) iOS, Windows e Windows Phone.

Com os alvos estabelecidos, o Notification Hubs empurra as notificações para o *serviço de notificação push* para a plataforma do dispositivo. Exemplos incluem o serviço de notificação Apple Push (APNs) para iOS e macOS, e Firebase Cloud Messaging (FCM) para dispositivos Android. O Notification Hubs empurra notificações divididas em vários lotes de registos. Autentica-se com o respetivo serviço de notificação push, com base nas credenciais que definiu no portal Azure, no âmbito do **Configure Notification Hub**. Em seguida, o serviço de notificação push reencaminha as notificações para os *respetivos dispositivos clientes*.

A última etapa da entrega de notificação encontra-se entre o serviço de notificação push da plataforma e o dispositivo. A entrega de notificações pode falhar em qualquer uma das quatro fases do processo de notificação push (cliente, final de aplicação, Centros de Notificação e serviço de notificação push da plataforma). Para obter mais informações sobre a arquitetura Dos Centros de Notificação, consulte [a visão geral dos Centros de Notificação].

Pode ocorrer uma falha na entrega de notificações durante a fase inicial de teste/fase de preparação. As notificações retiradas nesta fase podem indicar um problema de configuração. Se ocorrer uma falha na entrega de notificações na produção, algumas ou todas as notificações poderão ser retiradas. Neste caso, é indicado um problema de padrão de aplicação ou padrão de mensagens mais profundo.

A secção seguinte analisa cenários em que as notificações podem ser retiradas, variando de comum a raro.

## <a name="notification-hubs-misconfiguration"></a>Aviso Hubs configuração errada

Para enviar notificações para o respetivo serviço de notificação push, os Centros de Notificação devem autenticar-se no contexto da sua aplicação. Tem de criar uma conta de programador com o serviço de notificação da plataforma-alvo (Microsoft, Apple, Google, etc.). Em seguida, deve registar a sua aplicação com o SO onde obtém um token ou chave que usa para trabalhar com o PNS alvo.

Tem de adicionar credenciais de plataforma ao portal Azure. Se não houver notificações a chegar ao dispositivo, o primeiro passo é garantir que as credenciais corretas são configuradas nos Centros de Notificação. As credenciais devem corresponder à aplicação criada sob uma conta de programador específica da plataforma.

Para obter instruções passo a passo para concluir este processo, consulte [Começar com os Hubs de Notificação Azure].

Aqui estão algumas configurações erradas comuns para verificar:

### <a name="notification-hub-name-location"></a>Localização do nome do centro de notificação

Certifique-se de que o nome do seu hub de notificação (sem tipografias) é o mesmo em cada um destes locais:

* Onde se regista no cliente
* Onde envia notificações a partir da parte de trás
* Onde configuraste as credenciais de serviço de notificação push

Certifique-se de que utiliza as cadeias de configuração de assinatura de acesso partilhado corretas no cliente e na parte traseira da aplicação. Geralmente, deve utilizar **a assinatura defaultListenSharedAccessss** no cliente e **no DefaultFulFullSharedAccessssignature** no back end da aplicação. Isto concede permissões para enviar notificações para Os Centros de Notificação.

### <a name="apn-configuration"></a>Configuração APN

Deve manter dois centros diferentes: um para produção e outro para testes. Deve enviar o certificado que utiliza num ambiente de caixa de areia para um centro separado do que o certificado/hub que utilizará na produção. Não tente enviar diferentes tipos de certificados para o mesmo centro. Causará falhas de notificação.

Se enviar inadvertidamente diferentes tipos de certificados para o mesmo hub, deve apagar o hub e começar de novo com um novo hub. Se por alguma razão não puder apagar o hub, deve pelo menos apagar todos os registos existentes do centro.

### <a name="fcm-configuration"></a>Configuração FCM

1. Certifique-se de que a chave do *servidor* obtida a partir do Firebase corresponde à chave de servidor registada no portal Azure.

   ![Chave do servidor firebase][3]

2. Certifique-se de que tem o **ID do Projeto** configurado no cliente. Pode obter o valor do **Projeto ID** a partir do painel de instrumentos firebase.

   ![ID do projeto firebase][1]

## <a name="application-issues"></a>Questões de aplicação

### <a name="tags-and-tag-expressions"></a>Etiquetas e expressões de etiquetas

Se utilizar tags ou expressões de etiquetas para segmentar o seu público, é possível que quando envia a notificação, não seja encontrado nenhum alvo. Este erro baseia-se nas etiquetas especificadas ou nas expressões de identificação na sua chamada de envio.

Reveja as suas inscrições para garantir que as etiquetas correspondem quando envia uma notificação. Em seguida, verifique o recibo de notificação apenas dos clientes que têm esses registos.

Por exemplo, suponha que todos os seus registos com Centros de Notificação usem a etiqueta "Política". Se enviar então uma notificação com a etiqueta "Sports", a notificação não será enviada para nenhum dispositivo. Um caso complexo pode envolver expressões de etiquetas onde se registou usando "Tag A" *ou* "Tag B", mas apontou "Tag A && Tag B". A secção de dicas de autodiagnóstico mais tarde no artigo mostra-lhe como rever as suas inscrições e as suas etiquetas.

### <a name="template-issues"></a>Problemas de modelo

Se utilizar modelos, certifique-se de que segue as diretrizes descritas nos [Modelos].

### <a name="invalid-registrations"></a>Registos inválidos

Se o centro de notificação foi configurado corretamente e as etiquetas ou expressões de etiquetas foram utilizadas corretamente, encontram-se alvos válidos. As notificações devem ser enviadas para estes objetivos. Os Centros de Notificação disparam vários lotes de processamento em paralelo. Cada lote envia mensagens para um conjunto de registos.

> [!NOTE]
> Uma vez que os Hubs de Notificação processam os lotes em paralelo, a ordem pela qual as notificações são entregues não está garantida.

Os Centros de Notificação estão otimizados para um modelo de entrega de mensagens "no máximo de uma vez". Tentamos desduplica, para que nenhuma notificação seja entregue mais do que uma vez a um dispositivo. As inscrições são verificadas para garantir que apenas uma mensagem é enviada por identificador de dispositivo antes de ser enviada para o serviço de notificação push.

Cada lote é enviado para o serviço de notificação push, que por sua vez aceita e valida as inscrições. Durante este processo, é possível que o serviço de notificação push detete um erro com um ou mais registos num lote. O serviço de notificação push retorna então um erro aos Centros de Notificação e o processo para. O serviço de notificação push deixa completamente esse lote. Isto é especialmente verdade com as APNs, que usam um protocolo de fluxo TCP.

Neste caso, o registo de avarias é removido da base de dados. Em seguida, recandidámos a entrega de notificação para o resto dos dispositivos nesse lote.

Para obter mais informações de erro sobre a tentativa falhada de entrega contra um registo, pode utilizar os Hubs de Notificação REST APIs [Per Message Telemetria: Obter telemetria de mensagem de notificação](/rest/api/notificationhubs/get-notification-message-telemetry) e [feedback PNS](/previous-versions/azure/reference/mt705560(v=azure.100)). Para obter o código de amostra, consulte o [exemplo Enviar REST](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/).

## <a name="push-notification-service-issues"></a>Problemas de serviço de notificação push

Após o serviço de notificação push receber a notificação, entrega a notificação ao dispositivo. Neste momento, os Centros de Notificação não têm qualquer controlo sobre a entrega da notificação ao dispositivo.

Como os serviços de notificação da plataforma são robustos, as notificações tendem a chegar aos dispositivos em poucos segundos. Se o serviço de notificação push estiver a estrangular, os Centros de Notificação aplicam uma estratégia exponencial de back-off. Se o serviço de notificação push permanecer inacessível durante 30 minutos, existe uma política em vigor para expirar e deixar as mensagens permanentemente.

Se um serviço de notificação push tentar entregar uma notificação mas o dispositivo estiver offline, a notificação é armazenada pelo serviço de notificação push. Está guardado por um período limitado de tempo. A notificação é entregue ao dispositivo quando o dispositivo estiver disponível.

Cada aplicação armazena apenas uma notificação recente. Se várias notificações forem enviadas enquanto um dispositivo está offline, cada nova notificação faz com que a última seja descartada. Manter apenas a mais recente notificação é chamado *de coalescing* em APNs e *colapso* na FCM. (A FCM utiliza uma chave de colapso.) Quando o dispositivo permanece offline durante muito tempo, as notificações que foram armazenadas para o dispositivo são descartadas. Para mais informações, consulte [as mensagens GERAL DAS APNs] e [sobre as mensagens FCM].

Com os Centros de Notificação, pode passar uma chave de coalescing através de um cabeçalho HTTP utilizando a API genérica de SendNotification. Por exemplo, para o .NET SDK, utilizaria `SendNotificationAsync` . A API sendNotification também leva cabeçalhos HTTP que são passados como é para o respetivo serviço de notificação push.

## <a name="self-diagnosis-tips"></a>Dicas de autodiagnóstico

Aqui estão os caminhos para diagnosticar a causa principal das notificações deixadas em Centros de Notificação.

### <a name="verify-credentials"></a>Verificar credenciais

#### <a name="push-notification-service-developer-portal"></a>Portal de programadores de serviço de notificação push

Verifique as credenciais no respetivo portal de programadores de serviços de notificação push (APNs, FCM, Windows Notification Service, e assim por diante). Para obter mais informações, consulte [Tutorial: Envie notificações para as aplicações da Plataforma Universal windows utilizando os Hubs de Notificação Azure](./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

#### <a name="azure-portal"></a>Portal do Azure

Para rever e combinar as credenciais com as que obteve do portal de programadores de serviços de notificação push, aceda ao separador **Políticas de Acesso** no portal Azure.

![Políticas de Acesso ao Portal Azure][4]

### <a name="verify-registrations"></a>Verificar registos

#### <a name="visual-studio"></a>Visual Studio

No Visual Studio, pode ligar-se ao Azure através do Server Explorer para visualizar e gerir vários serviços Azure, incluindo Centros de Notificação. Este atalho é essencialmente útil para o seu ambiente de desenvolvimento/teste.

![Explorador visual do servidor do estúdio][9]

![Explorador de Servidores](media/notification-hubs-push-notification-fixer/vsserverexplorer2.png)

Pode ver e gerir todos os registos no seu hub. As inscrições podem ser categorizadas por plataforma, registo de modelos, etiqueta, identificador de serviço de notificação push, ID de registo e data de validade. Também pode editar uma inscrição nesta página. É especialmente útil para editar tags.

Clique com o botão de notificação para o **Server Explorer** e selecione **Diagnosticar**. 

![Visual Studio Server Explorer: Menu de diagnóstico](./media/notification-hubs-push-notification-fixer/diagnose-menu.png)

Vê a seguinte página:

![Estúdio Visual: Página de diagnóstico](./media/notification-hubs-push-notification-fixer/diagnose-page.png)

Mude para a página **de Registos do Dispositivo:**

![Estúdio Visual: Registos de Dispositivos](./media/notification-hubs-push-notification-fixer/VSRegistrations.png)

Pode utilizar a página **de Enviar o teste** para enviar uma mensagem de notificação de teste:

![Estúdio Visual: Enviar teste](./media/notification-hubs-push-notification-fixer/test-send-vs.png)

> [!NOTE]
> Utilize o Visual Studio para editar registos apenas durante o desenvolvimento/teste, e com um número limitado de registos. Se precisar de editar os seus registos a granel, considere utilizar a funcionalidade de registo de exportação e importação descrita em [Como: Exportar e Modificar Registos em Massa](/previous-versions/azure/azure-services/dn790624(v=azure.100)).

#### <a name="service-bus-explorer"></a>Explorador do Service Bus

Muitos clientes usam [o Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) para visualizar e gerir os seus centros de notificação. Service Bus Explorer é um projeto de código aberto. 

### <a name="verify-message-notifications"></a>Verificar notificações de mensagens

#### <a name="azure-portal"></a>Portal do Azure

Para enviar uma notificação de teste aos seus clientes sem ter um serviço de volta acabando e funcionando, em **SUPORTE + RESOLUÇÃO DE PROBLEMAS**, selecione **Enviar teste**.

![Teste Enviar funcionalidade em Azure][7]

#### <a name="visual-studio"></a>Visual Studio

Também pode enviar notificações de teste do Visual Studio.

![Teste Enviar funcionalidade em Estúdio Visual][10]

Para obter mais informações sobre a utilização de Centros de Notificação com o Visual Studio Server Explorer, consulte estes artigos:

* [Como visualizar registos de dispositivos para centros de notificação](/previous-versions/windows/apps/dn792122(v=win.10))
* [Mergulho profundo: Visual Studio 2013 Update 2 RC e Azure SDK 2.3]
* [Anunciando lançamento do Visual Studio 2013 Update 3 e Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Debug falhou notificações e revisão do resultado da notificação

#### <a name="enabletestsend-property"></a>Propriedade EnableTestSend

Quando envia uma notificação através de Centros de Notificação, a notificação é inicialmente feita. Os Centros de Notificação determinam os alvos corretos e, em seguida, enviam a notificação para o serviço de notificação push. Se estiver a utilizar a API REST ou qualquer um dos SDKs clientes, a devolução da sua chamada significa apenas que a mensagem está em fila com os Centros de Notificação. Não fornece informações sobre o que aconteceu quando os Centros de Notificação acabaram por enviar a notificação para o serviço de notificação push.

Se a sua notificação não chegar ao dispositivo cliente, pode ter ocorrido um erro quando os Centros de Notificação tentaram entregá-lo ao serviço de notificação push. Por exemplo, o tamanho da carga útil pode exceder o máximo permitido pelo serviço de notificação push, ou as credenciais configuradas nos Centros de Notificação podem ser inválidas.

Para obter informações sobre erros de serviço de notificação push, pode utilizar a propriedade [EnableTestSend.] Esta propriedade é automaticamente ativada quando envia mensagens de teste do portal ou cliente do Visual Studio. Você pode usar esta propriedade para ver informações detalhadas de depuragem e também através de APIs. Atualmente, pode usá-lo no .NET SDK. Será adicionado a todos os SDKs clientes eventualmente.

Para utilizar a `EnableTestSend` propriedade com a chamada REST, apeça um parâmetro de cadeia de consulta chamado *teste* para o final da sua chamada de envio. Por exemplo:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>.NET SDK exemplo

Aqui está um exemplo de utilização do .NET SDK para enviar uma notificação pop-up nativa (torrada):

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

No final da execução, `result.State` simplesmente `Enqueued` afirma. Os resultados não fornecem nenhuma visão sobre o que aconteceu com a sua notificação push.

Em seguida, você pode usar a `EnableTestSend` propriedade Boolean. Utilize a `EnableTestSend` propriedade enquanto inicializa `NotificationHubClient` para obter um estado detalhado sobre erros de serviço de notificação push que ocorrem quando a notificação é enviada. A chamada de envio leva tempo adicional para ser retornáda porque primeiro precisa de Centros de Notificação para entregar a notificação ao serviço de notificação push.

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

#### <a name="sample-output"></a>Saída de exemplo

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Esta mensagem indica que as credenciais configuradas nos Centros de Notificação são inválidas ou que há um problema com as inscrições no centro. Elimine este registo e deixe o cliente recriar o registo antes de enviar a mensagem.

> [!NOTE]
> O uso da `EnableTestSend` propriedade é fortemente acelerado. Utilize esta opção apenas num ambiente de desenvolvimento/teste e com um conjunto limitado de registos. As notificações de depurg são enviadas para apenas 10 dispositivos. Há também um limite para o processamento de depurg envia, a 10 por minuto.

### <a name="review-telemetry"></a>Rever telemetria

#### <a name="azure-portal"></a>Portal do Azure

No portal, pode obter uma visão geral rápida de toda a atividade no seu centro de notificações.

1. No **separador 'Visão Geral',** pode ver uma visão agregada de registos, notificações e erros por plataforma.

   ![Painel geral dos Hubs de Notificação][5]

2. No **separador Monitor,** pode adicionar muitas outras métricas específicas da plataforma para um olhar mais profundo. Pode olhar especificamente para os erros que são devolvidos quando os Hubs de Notificação tentam enviar a notificação para o serviço de notificação push.

   ![Log de atividade do portal Azure][6]

3. Comece por rever **mensagens recebidas,** **operações de registo** e **notificações bem sucedidas.** Em seguida, vá ao separador por plataforma para rever erros específicos do serviço de notificação push.

4. Se as definições de autenticação do seu centro de notificação estiverem incorretas, aparece o **Erro de Autenticação PNS** da mensagem. É uma boa indicação para verificar as credenciais do serviço de notificação push.

#### <a name="programmatic-access"></a>Acesso programático

Para obter mais informações sobre o acesso programático, consulte [o acesso programático.](/previous-versions/azure/azure-services/dn458823(v=azure.100))

> [!NOTE]
> Várias características relacionadas com a telemetria, como a exportação e importação de registos e o acesso à telemetria via APIs, estão disponíveis apenas no nível de serviço Standard. Se tentar utilizar estas funcionalidades a partir do nível de serviço Gratuito ou Básico, receberá uma mensagem de exceção se utilizar o SDK. Obterá um erro HTTP 403 (Proibido) se utilizar as funcionalidades diretamente das APIs REST.
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
[Visão geral dos Hubs de Notificação]: notification-hubs-push-notification-overview.md
[Começar com os Hubs de Notificação Azure]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Modelos]: /previous-versions/azure/azure-services/dn530748(v=azure.100)
[Visão geral das APNs]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Sobre as mensagens do FCM]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: /previous-versions/azure/azure-services/dn790624(v=azure.100)
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: /previous-versions/windows/apps/dn792122(v=win.10)
[Mergulho profundo: Visual Studio 2013 Update 2 RC e Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Anunciando lançamento do Visual Studio 2013 Update 3 e Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: /dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend
[Programmatic telemetry access]: /previous-versions/azure/azure-services/dn458823(v=azure.100)
