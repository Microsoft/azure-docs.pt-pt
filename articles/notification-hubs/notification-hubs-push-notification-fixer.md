---
title: Diagnosticar notificações ignoradas nos Hubs de notificação do Azure
description: Saiba como diagnosticar problemas comuns com notificações ignoradas nos Hubs de notificação do Azure.
services: notification-hubs
documentationcenter: Mobile
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 04/04/2019
ms.author: jowargo
ms.openlocfilehash: 4fc4175c03baa4ddb81507dd4001fcdbe7c7058b
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149551"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Diagnosticar notificações ignoradas nos Hubs de notificação do Azure

Uma pergunta comum sobre os Hubs de notificação do Azure é como resolver problemas ao notificações a partir de um aplicativo não aparecem nos dispositivos cliente. Os clientes querem saber onde e por que as notificações foram removidas e como resolver o problema. Este artigo identifica a razão pela qual as notificações podem desconectadas ou não ser recebidas por dispositivos. Também explica como determinar a causa raiz.

É fundamental compreender primeiro como os Hubs de notificação envia notificações para um dispositivo.

![Arquitetura de Hubs de notificação][0]

Num fluxo de notificação de envio normal, a mensagem for enviada dos *aplicação de back-end* para os Hubs de notificação. Os Hubs de notificação processa todos os registros. Ele leva em conta as etiquetas configuradas e expressões de etiqueta para determinar os destinos. Destinos forem os registros que têm de receber a notificação push. Estes registos podem abranger qualquer uma das nossas plataformas suportadas: Android, Baidu (dispositivos Android na China), SO de fogo (Amazon) em iOS, Windows e Windows Phone.

Com os destinos estabelecidos, os Hubs de notificação envia notificações para o *push do serviço de notificação* para a plataforma do dispositivo. Os exemplos incluem o serviço Apple Push Notification (APNs) para a Apple e o Firebase Cloud Messaging (FCM) para o Google. Notificações de pushes de Hubs de notificação é dividido em vários lotes de registos. Autentica com o serviço de notificação push respectivos, com base nas credenciais definido no portal do Azure, em **configurar o Notification Hub**. A notificação push do serviço, em seguida, reencaminha as notificações para os respectivos *dispositivos cliente*.

É o leg final de notificação de entrega entre o serviço de notificações push da plataforma e dispositivo. Entrega da notificação pode falhar em qualquer um dos quatro fases do processo de notificação push (cliente, aplicação de back-end, os Hubs de notificação e serviço de notificações push da plataforma). Para obter mais informações sobre a arquitetura de Hubs de notificação, consulte [descrição geral dos Hubs de notificação].

Pode ocorrer uma falha a entrega de notificações inicial durante a fase de teste/teste. Notificações ignoradas neste estágio podem indicar um problema de configuração. Se ocorrer uma falha a entrega de notificações em produção, algumas ou todas as notificações podem ser removidas. Um aplicativo mais aprofundado ou problema de padrão de mensagens é indicado neste caso.

A próxima seção aborda cenários em que poderão ser removidas notificações, que vão desde comuns para raras.

## <a name="notification-hubs-misconfiguration"></a>Configuração incorreta de Hubs de notificação ##

Para enviar notificações para o serviço de notificação push respectivos, os Hubs de notificação deve autenticar-se no contexto da sua aplicação. Tem de criar uma conta de programador com o serviço de notificação da plataforma de destino (Microsoft, Apple, Google, etc.). Em seguida, deve registrar seu aplicativo com o sistema operacional em que obtém um token ou chave que utiliza para trabalhar com o PNS de destino.

Tem de adicionar as credenciais de plataforma para o portal do Azure. Se não existem notificações Contatando o dispositivo, a primeira etapa é garantir que as credenciais corretas estão configuradas nos Hubs de notificação. As credenciais têm de corresponder a aplicação que é criada uma conta de desenvolvedor específicos da plataforma.

Para obter instruções passo a passo concluir este processo, consulte [introdução aos Hubs de notificação do Azure].

Aqui estão algumas configurações incorretas comuns para procurar:

### <a name="notification-hub-name-location"></a>Localização de nome do hub de notificação

Certifique-se de que o nome do hub de notificação (sem erros de digitação) é o mesmo em cada um destes locais:
   * Em que Registre-se do cliente
   * Para onde enviar notificações de back-end
   * Qual configurou as credenciais de serviço de notificação push

Certifique-se de usar cadeias de configuração de assinatura de acesso partilhado corretos no cliente e de fim de volta o aplicativo. Em geral, tem de utilizar **DefaultListenSharedAccessSignature** no cliente e **DefaultFullSharedAccessSignature** na aplicação de back-end. Esta ação garante permissões para enviar notificações para os Hubs de notificação.

### <a name="apn-configuration"></a>Configuração do APN ###

Tem de manter dois hubs diferentes: um para produção e outra para testes. Tem de carregar o certificado que utilizar num ambiente de área de segurança para um hub separado que o hub/certificado que irá utilizar na produção. Não tente carregar diferentes tipos de certificados para o mesmo hub. Irá causar falhas de notificação.

Se carregar inadvertidamente diferentes tipos de certificados para o mesmo hub, deve eliminar o hub e comece do zero com um novo hub. Se por alguma razão não é possível eliminar o hub, tem de eliminar, pelo menos, todos os registos existentes do hub.

### <a name="fcm-configuration"></a>Configuração do FCM ###

1. Certifique-se de que o *chave de servidor* obteve das correspondências Firebase a chave do servidor registado no portal do Azure.

   ![Chave de servidor do firebase][3]

2. Certifique-se de que configurou **ID do projeto** no cliente. Pode obter o valor para **ID do projeto** a partir do Firebase dashboard.

   ![ID do projeto firebase][1]

## <a name="application-issues"></a>Problemas de aplicativos ##

### <a name="tags-and-tag-expressions"></a>As etiquetas e expressões de etiqueta ###

Se utilizar etiquetas ou expressões de etiqueta para segmentar o público-alvo, é possível que, ao enviar a notificação, nenhum destino está localizado. Este erro baseia-se nas tags especificadas ou expressões de etiqueta em sua chamada de envio.

Reveja os registos para garantir que as etiquetas de correspondem ao enviar uma notificação. Em seguida, verifique se o recebimento de notificação de apenas os clientes que tenham esses registos.

Por exemplo, suponha que todos os registos dos hubs de notificação usar a marca "Política". Se, em seguida, envia uma notificação com a etiqueta "Desporto", a notificação não enviada para qualquer dispositivo. Um caso complexo pode envolver a expressões de etiqueta onde registadas utilizando "Marca A" *ou* "B de etiqueta", mas direcionados "Marca A e & etiqueta B." A secção de dicas de auto-diagnóstico posteriormente neste artigo mostra-lhe como rever os registos dos e respetivas etiquetas.

### <a name="template-issues"></a>Problemas do modelo ###

Se utilizar modelos, certifique-se de que segue as diretrizes descritas em [modelos].

### <a name="invalid-registrations"></a>Registos inválidos ###

Se o hub de notificação foi configurado corretamente e etiquetas ou expressões de etiqueta foram utilizadas corretamente, foram encontrados destinos válidos. Notificações devem ser enviadas para esses destinos. Os Hubs de notificação dispara, em seguida, vários de processamento de lotes em paralelo. Cada batch envia mensagens para um conjunto de registos.

> [!NOTE]
> Uma vez que os Hubs de notificação processa lotes em paralelo, a ordem na qual as notificações são entregues não é garantida.

Os Hubs de notificação está otimizado para um modelo de entrega de mensagem de "no-mais-uma vez". Vamos tentar a eliminação de duplicados, para que não existem notificações são fornecidas mais de uma vez a um dispositivo. Registos são verificados para garantir que apenas uma mensagem é enviada por identificador de dispositivo antes de ser enviada para o serviço de notificação push.

Cada batch é enviado para o serviço de notificação push, que por sua vez aceita e valida os registros. Durante este processo, é possível que o serviço de notificação push irá detetar um erro com um ou mais registos num batch. O serviço de notificação push, em seguida, devolve um erro para os Hubs de notificação e interrompe o processo. O serviço de notificação push ignora completamente esse lote. Isso é especialmente verdadeiro com APNs, que utiliza um protocolo de fluxo TCP.

Neste caso, o registo com falha é removido da base de dados. Em seguida, podemos Repita entrega da notificação para o resto dos dispositivos em que o batch.

Para obter mais informações de erro sobre a tentativa de entrega com falhas em relação a um registo, pode utilizar as APIs de REST de Hubs de notificação [telemetria por mensagem: Obter telemetria de mensagem de notificação](https://msdn.microsoft.com/library/azure/mt608135.aspx) e [comentários PNS](https://msdn.microsoft.com/library/azure/mt705560.aspx). Para o código de exemplo, consulte a [exemplo de enviar REST](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/).

## <a name="push-notification-service-issues"></a>Problemas do serviço de notificação de push

Depois do serviço de notificação push recebe a notificação, ele fornece a notificação para o dispositivo. Neste momento, os Hubs de notificação tem nenhum controle sobre a entrega da notificação para o dispositivo.

Como os serviços de notificação de plataforma são robustos, notificações tendem para chegarem aos dispositivos dentro de alguns segundos. Se está a limitar o serviço de notificação push, os Hubs de notificação aplica-se uma estratégia de término exponencial. Se o serviço de notificação push permanece inacessível durante 30 minutos, há uma diretiva para expirar e remover as mensagens de forma permanente.

Se um serviço de notificações push tenta enviar uma notificação, mas o dispositivo estiver offline, a notificação é armazenada pelo serviço de notificação push. Ele é armazenado por um período limitado de tempo. A notificação é entregue no dispositivo quando o dispositivo fica disponível.

Cada aplicação armazena apenas uma notificação recente. Se várias notificações são enviadas enquanto um dispositivo estiver offline, cada nova notificação faz com que a última que foi que serão ignorados. Manter apenas a notificação mais recente é chamado *União* no APNs e *recolhimento* no FCM. (O FCM utiliza uma chave de recolhimento). Quando o dispositivo permanece offline por muito tempo, as notificações que foram armazenadas para o dispositivo são eliminadas. Para obter mais informações, consulte [descrição geral do APNs] e [Acerca das mensagens do FCM].

Com os Hubs de notificação, pode transmitir uma chave de agregação através de um cabeçalho HTTP com a API de SendNotification genérico. Por exemplo, para o SDK de .NET, usaria `SendNotificationAsync`. A API de SendNotification também usa cabeçalhos HTTP que são transmitidos como é o serviço de notificação push respectivos.

## <a name="self-diagnosis-tips"></a>Dicas de auto-diagnóstico

Aqui estão os caminhos para diagnosticar a causa de raiz de notificações ignoradas nos Hubs de notificação.

### <a name="verify-credentials"></a>Verificar credenciais ###

#### <a name="push-notification-service-developer-portal"></a>Portal de Programador de serviço de notificação Push ####

Verifique se as credenciais no respectivo push notification service portal do programador (APNs, FCM, serviço de notificação do Windows e assim por diante). Para obter mais informações, consulte [Tutorial: Enviar notificações para aplicações da plataforma Universal do Windows com os Hubs de notificação do Azure](https://docs.microsoft.com/en-us/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification).

#### <a name="azure-portal"></a>Portal do Azure ####

Para rever e corresponder às credenciais com aqueles obtido a partir do portal do Programador de serviço de notificação push, vá para o **políticas de acesso** separador no portal do Azure.

![Portal do Azure, as políticas de acesso][4]

### <a name="verify-registrations"></a>Verifique os registos

#### <a name="visual-studio"></a>Visual Studio ####

No Visual Studio, pode ligar ao Azure através do Explorador de servidores para ver e gerir vários serviços do Azure, incluindo os Hubs de notificação. Este atalho é principalmente útil para o seu ambiente de desenvolvimento/teste.

![Explorador de servidores do Visual Studio][9]

Pode ver e gerir todos os registros do seu hub. Os registos podem ser categorizados pela plataforma, nativo ou modelo de registo, marca, identificador de serviço de notificação push, ID de registo e data de expiração. Também pode editar um registo nesta página. É especialmente útil para a edição de etiquetas.

Com o botão direito do hub de notificação no **Explorador de servidores**e selecione **diagnosticar**. 

![Explorador de servidores do Visual Studio: Diagnosticar menu](./media/notification-hubs-diagnosing/diagnose-menu.png)

Verá a página seguinte:

![Visual Studio: Diagnosticar a página](./media/notification-hubs-diagnosing/diagnose-page.png)

Mude para o **registos de dispositivos** página:

![Visual Studio: Registos de dispositivos](./media/notification-hubs-diagnosing/VSRegistrations.png)

Pode usar **envio de teste** página para enviar uma mensagem de notificação de teste:

![Visual Studio: Testar Envio](./media/notification-hubs-diagnosing/test-send-vs.png)

> [!NOTE]
> Utilize o Visual Studio para editar registos apenas durante o desenvolvimento/teste e, com um número limitado de registos. Se precisar de editar os seus registos em massa, considere utilizar a exportação e importar a funcionalidade de registo descrita [como: Exportar e modificar registos em massa](https://msdn.microsoft.com/library/dn790624.aspx).

#### <a name="service-bus-explorer"></a>Explorador do Service Bus ####

Muitos clientes utilizam [Explorador do Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer) para ver e gerir os hubs de notificação. Explorador do Service Bus é um projeto de código-fonte aberto. 

### <a name="verify-message-notifications"></a>Certifique-se de notificações de mensagens

#### <a name="azure-portal"></a>Portal do Azure ####

Para enviar uma notificação de teste para os seus clientes sem ter um serviço back-end em funcionamento, em **suporte + resolução de problemas**, selecione **envio de teste**.

![Funcionalidade de envio de teste no Azure][7]

#### <a name="visual-studio"></a>Visual Studio ####

Também pode enviar notificações de teste a partir do Visual Studio.

![Testar a funcionalidade de envio no Visual Studio][10]

Para obter mais informações sobre como utilizar os Hubs de notificação com o Visual Studio Server Explorer, veja estes artigos:

* [Como ver os registos de dispositivos para os hubs de notificação](https://docs.microsoft.com/en-us/previous-versions/windows/apps/dn792122(v=win.10))
* [Aprofunde-se: Visual Studio 2013 Update 2 RC e o Azure SDK 2.3]
* [Apresentamos o lançamento do Visual Studio 2013 Update 3 e o Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Notificações de falha de depuração e reveja o resultado da notificação

#### <a name="enabletestsend-property"></a>Propriedade de EnableTestSend ####

Quando envia uma notificação através dos Hubs de notificação, a notificação inicialmente é colocada na fila. Os Hubs de notificação determina os destinos corretos e, em seguida, envia a notificação para o serviço de notificação push. Se estiver a utilizar a API REST ou qualquer um dos SDKs do cliente, o retorno de chamada seu envio apenas significa que a mensagem é colocada na fila com os Hubs de notificação. Ela não fornece informações sobre o que aconteceu quando os Hubs de notificação enviados, por fim, a notificação para o serviço de notificação push.

Se a notificação não chegue ao dispositivo cliente, poderá ter Ocorreu um erro quando os Hubs de notificação tentou entregá-la para o serviço de notificação push. Por exemplo, o tamanho de payload pode exceder o máximo permitido pelo serviço de notificação push, ou as credenciais configuradas nos Hubs de notificação poderão ser inválidas.

Para obter informações sobre push erros de serviço de notificação, pode utilizar o [EnableTestSend] propriedade. Esta propriedade é ativada automaticamente quando enviar mensagens de teste a partir do portal ou do cliente do Visual Studio. Pode utilizar esta propriedade para ver informações detalhadas de depuração e também através de APIs. Atualmente, pode usá-lo no SDK do .NET. Este será adicionado para todos os SDKs do cliente no final.

Para utilizar o `EnableTestSend` propriedade com a chamada REST, acrescentar um parâmetro de cadeia de caracteres de consulta denominado *testar* ao final da sua chamada de envio. Por exemplo:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>Exemplo de .NET SDK ####

Eis um exemplo de como utilizar o SDK do .NET para enviar uma notificação de pop-up de nativo (alerta):

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

No final da execução, `result.State` declara `Enqueued`. Os resultados não fornecem qualquer informações sobre o que aconteceu com a notificação push.

Em seguida, pode usar o `EnableTestSend` propriedade booleana. Utilize o `EnableTestSend` propriedade enquanto inicializar `NotificationHubClient` para obter um estado detalhado sobre push erros de serviço de notificação que ocorrem quando a notificação é enviada. A chamada de envio demora mais tempo para retornar porque tem primeiro de Notification Hubs para enviar a notificação para o serviço de notificação push.

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

#### <a name="sample-output"></a>Resultado da amostra ####

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Esta mensagem indica que as credenciais configuradas nos Hubs de notificação são inválidas ou que existe um problema com os registros no hub. Odstranit tuto registraci e deixar que o cliente voltar a criar o registo antes de enviar a mensagem.

> [!NOTE]
> Utilizar o `EnableTestSend` propriedade é bastante limitada. Utilize esta opção apenas num ambiente de desenvolvimento/teste e com um conjunto limitado de registos. Depurar as notificações são enviadas para apenas 10 dispositivos. Também existe um limite no processamento envia de depuração, em 10 por minuto.

### <a name="review-telemetry"></a>Rever a telemetria ###

#### <a name="azure-portal"></a>Portal do Azure ####

No portal, pode obter uma visão geral de toda a atividade do seu hub de notificação.

1. Sobre o **descrição geral** separador, pode ver uma exibição agregada de registos, notificações e erros pela plataforma.

   ![Dashboard de descrição geral dos Hubs de notificação][5]

2. Sobre o **Monitor** separador, pode adicionar muitas outras métricas específicas da plataforma para uma visão mais profunda. Pode ver especificamente a erros que são devolvidos quando tenta de Hubs de notificação enviar a notificação para o serviço de notificação push.

   ![Registo de atividades de portal do Azure][6]

3. Comece por rever **mensagens de entrada**, **operações de registo**, e **notificações com êxito**. Em seguida, vá à aba por plataforma para rever erros que são específicos para o serviço de notificação push.

4. Se as definições de autenticação para o seu hub de notificação estão incorretas, a mensagem **erro de autenticação de PNS** aparece. É uma boa indicação para verificar as credenciais de serviço de notificação push.

#### <a name="programmatic-access"></a>Acesso programático ####

Para obter mais informações sobre o acesso programático, consulte [acesso programático](https://docs.microsoft.com/en-us/previous-versions/azure/azure-services/dn458823(v=azure.100)).

> [!NOTE]
> Várias funcionalidades relacionadas com a telemetria, como a exportação e importação de registos e acesso de telemetria através de APIs, estão disponíveis apenas no escalão de serviço Standard. Se tentar usar esses recursos do gratuito ou básico escalão de serviço, obterá uma mensagem de exceção se usar o SDK. Obterá um erro HTTP 403 (proibido) se usar os recursos diretamente a partir de REST APIs.
>
> Para utilizar as funcionalidades relacionadas com a telemetria, certifique-se primeiro no portal do Azure que está a utilizar o escalão de serviço Standard.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/FCMConfigure.png
[3]: ./media/notification-hubs-diagnosing/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Descrição geral dos Hubs de notificação]: notification-hubs-push-notification-overview.md
[Introdução aos Hubs de notificação do Azure]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Modelos]: https://msdn.microsoft.com/library/dn530748.aspx
[Descrição geral do APNs]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Acerca das mensagens do FCM]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Aprofunde-se: Visual Studio 2013 Update 2 RC e o Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Apresentamos o lançamento do Visual Studio 2013 Update 3 e o Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx
