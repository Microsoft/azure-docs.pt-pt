---
title: Os Hubs de notificação do Azure - diagnosticar notificações removidas
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
ms.openlocfilehash: 4af86025e714c65d0ae225b271a2d0970bb96ee8
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281646"
---
# <a name="azure-notification-hubs---diagnose-dropped-notifications"></a>Os Hubs de notificação do Azure - diagnosticar notificações removidas

Uma das perguntas mais comuns de clientes de Notification Hubs do Azure é como resolver problemas ao notificações que são enviadas a partir de um aplicativo não aparecem nos dispositivos cliente. Elas querem saber onde e por que as notificações foram removidas e como corrigir o problema. Este artigo identifica a razão pela qual as notificações podem desconectadas ou não ser recebidas por dispositivos. Saiba como analisar e determinar a causa raiz.

É fundamental compreender primeiro como o serviço de Hubs de notificação envia notificações para um dispositivo.

![Arquitetura de Hubs de notificação][0]

Num fluxo de notificação de envio normal, a mensagem for enviada dos *aplicação de back-end* para os Hubs de notificação. Os Hubs de notificação faz o processamento em todos os registros. O processamento leva em conta as etiquetas configuradas e expressões de etiqueta para determinar o "destinos". Destinos são todos os registos que têm de receber a notificação push. Estes registos podem abranger qualquer uma ou todas as nossas plataformas suportadas: iOS, Google, Windows, Windows Phone, Kindle e Baidu para Android na China.

Com os destinos estabelecidos, o serviço de Hubs de notificação envia notificações para o *push do serviço de notificação* para a plataforma do dispositivo. Os exemplos incluem o serviço Apple Push Notification (APNs) para a Apple e o Firebase Cloud Messaging (FCM) para o Google. Notificações de pushes de Hubs de notificação é dividido em vários lotes de registos. Os Hubs de notificação é autenticado com o serviço de notificação push respectivos com base nas credenciais que definiu no portal do Azure, em **configurar o Notification Hub**. A notificação push do serviço, em seguida, reencaminha as notificações para os respectivos *dispositivos cliente*.

O leg final de entrega da notificação ocorre entre o serviço de notificação de push de plataforma e o dispositivo. Qualquer um dos quatro componentes principais do processo de notificação push (cliente, aplicação de back-end, os Hubs de notificação e o serviço de notificação de push de plataforma) pode causar notificações para ser removido. Para obter mais informações sobre a arquitetura de Hubs de notificação, consulte [descrição geral dos Hubs de notificação].

Falha na entrega de notificações pode ocorrer durante o primeiro teste/fase de teste. Notificações ignoradas neste estágio podem indicar um problema de configuração. Se ocorrer uma falha a entrega de notificações em produção, algumas ou todas as notificações podem ser removidas. Neste caso, um aplicativo mais aprofundado ou problema de padrão de mensagens é indicado.

A próxima seção aborda cenários em que podem ser removidas notificações, desde o comuns a mais raros.

## <a name="notification-hubs-misconfiguration"></a>Configuração incorreta de Hubs de notificação

Com êxito, enviar notificações para o serviço de notificação push respectivos, o serviço de Hubs de notificação precisa para se autenticar no contexto do aplicativo do desenvolvedor. O desenvolvedor cria uma conta de programador com a respetiva plataforma (Google, Apple, Windows e assim por diante). Em seguida, o desenvolvedor registra seus aplicativos com a plataforma onde obter as credenciais.

Tem de adicionar as credenciais de plataforma para o portal do Azure. Se não existem notificações Contatando o dispositivo, deve ser o primeiro passo para se certificar de que as credenciais corretas estão configuradas nos Hubs de notificação. As credenciais têm de corresponder a aplicação que é criada uma conta de desenvolvedor específicos da plataforma.

Para obter instruções passo a passo concluir este processo, consulte [introdução aos Hubs de notificação do Azure].

Aqui estão algumas configurações incorretas comuns para procurar:

**Geral**

Certifique-se de que o nome do hub de notificação (sem erros de digitação) é o mesmo em cada um destes locais:
   * Onde se registrar do cliente.
   * Onde envia notificações de back-end.
   * Qual configurou as credenciais de serviço de notificação push.

Certifique-se de que usar cadeias de configuração de assinatura de acesso partilhado corretos no cliente e no back-end da aplicação. Em geral, tem de utilizar **DefaultListenSharedAccessSignature** no cliente e **DefaultFullSharedAccessSignature** na aplicação de back-end (concede permissões para enviar notificações para Hubs de notificação).

**Configuração do APN**

Tem de manter dois hubs diferentes: um hub para produção e outro hub para fins de teste. Isso significa que tem de carregar o certificado que utilizar num ambiente de área de segurança para um hub separado que o certificado e o hub que vai utilizar na produção. Não tente carregar diferentes tipos de certificados para o mesmo hub. Isto poderá provocar falhas de notificação.

Se carregar inadvertidamente diferentes tipos de certificados para o mesmo hub, recomendamos que eliminar o hub e comece do zero com um novo hub. Se por alguma razão não é possível eliminar o hub, no mínimo, tem de eliminar todos os registos existentes do hub.

**Configuração do FCM**

1. Certifique-se de que o *chave de servidor* que obteve da Firebase corresponde a chave do servidor que registou no portal do Azure.

   ![Chave de servidor do firebase][3]

2. Certifique-se de que configurou **ID do projeto** no cliente. Pode obter o valor para **ID do projeto** a partir do Firebase dashboard.

   ![ID do projeto firebase][1]

## <a name="application-issues"></a>Problemas de aplicativos

**As etiquetas e expressões de etiqueta**

Se utilizar etiquetas ou expressões de etiqueta para segmentar o público-alvo, é possível que ao enviar a notificação, nenhum destino é encontrado com base no etiquetas ou expressões de etiqueta que especificou em sua chamada de envio.

Reveja os registos para se certificar de que existem etiquetas correspondentes ao enviar uma notificação. Em seguida, verifique se o recebimento de notificação apenas a partir de clientes que têm esses registos.

Por exemplo, se todos os registos dos hubs de notificação foram feitos usando a marca "Política" e enviar uma notificação com a etiqueta "Desporto", a notificação não é enviada a todos os dispositivos. Um caso complexo pode envolver a expressões de etiqueta na qual registado utilizando "Marca A" ou "B de etiqueta", mas durante o envio de notificações, o direcionamento de "Marca A e & etiqueta B." Na secção de dicas de auto-diagnóstico posteriormente neste artigo, mostraremos como rever os registos dos e respetivas etiquetas.

**Problemas do modelo**

Se utilizar modelos, certifique-se de que segue as diretrizes descritas em [modelos].

**Registos inválidos**

Se o hub de notificação foi configurado corretamente e se quaisquer etiquetas ou expressões de etiqueta foram usadas corretamente, foram encontrados destinos válidos. Notificações devem ser enviadas para esses destinos. O serviço de Hubs de notificação dispara, em seguida, vários de processamento de lotes em paralelo. Cada batch envia mensagens para um conjunto de registos.

> [!NOTE]
> Uma vez que o processamento é efetuado em paralelo, a ordem na qual as notificações são entregues não é garantida.

Os Hubs de notificação está otimizado para um modelo de entrega de mensagem de "em mais uma vez". Vamos tentar a eliminação de duplicados, para que não existem notificações são fornecidas mais de uma vez a um dispositivo. Para garantir esta situação, verifique os registos e certifique-se de que apenas uma mensagem é enviada por identificador de dispositivo antes da mensagem é enviada para o serviço de notificação push.

À medida que cada lote é enviado para o serviço de notificação push, que por sua vez é aceitar e validar os registros, é possível que o serviço de notificação push irá detetar um erro com um ou mais dos registros num lote. Neste caso, o serviço de notificação push devolve um erro para os Hubs de notificação e interrompe o processo. O serviço de notificação push ignora completamente esse lote. Isso é especialmente verdadeiro com APNS, que utiliza um protocolo de fluxo TCP.

Podemos estão otimizados para a maioria uma entrega. Mas, neste caso, o registo com falha é removido da base de dados. Em seguida, podemos Repita entrega da notificação para o resto dos dispositivos em que o batch.

Para obter mais informações de erro sobre a tentativa de entrega com falhas em relação a um registo, pode utilizar as APIs de REST de Hubs de notificação [telemetria por mensagem: Obter telemetria de mensagem de notificação](https://msdn.microsoft.com/library/azure/mt608135.aspx) e [comentários PNS](https://msdn.microsoft.com/library/azure/mt705560.aspx). Para o código de exemplo, consulte a [exemplo de enviar REST](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample).

## <a name="push-notification-service-issues"></a>Problemas do serviço de notificação de push

Depois da mensagem de notificação foi recebida pelo serviço de notificação push de plataforma, é da responsabilidade do serviço de notificação push para enviar a notificação para o dispositivo. Neste momento, o serviço de Hubs de notificação está fora da imagem e não tem controle sobre quando ou se a notificação é entregue no dispositivo.

Como os serviços de notificação de plataforma são robustos, notificações tendem para chegarem aos dispositivos do serviço de notificação push em poucos segundos. Se está a limitar o serviço de notificação push, o serviço de Hubs de notificação aplica-se uma estratégia de término exponencial. Se o serviço de notificação push permanece inacessível durante 30 minutos, temos uma política para expirar e remover as mensagens de forma permanente.

Se um serviço de notificações push tenta enviar uma notificação, mas o dispositivo estiver offline, a notificação é armazenada pelo serviço de notificação push para um período de tempo limitado. A notificação é entregue no dispositivo quando o dispositivo fica disponível.

Para cada aplicação, apenas uma notificação recente é armazenada. Se várias notificações são enviadas enquanto um dispositivo estiver offline, cada nova notificação faz com que a notificação anterior que serão ignorados. Manter apenas a notificação mais recente é referida como *notificações agregação* no APN, e *recolhimento* no FCM (que utiliza uma chave de recolhimento). Se o dispositivo pode permanecer offline por muito tempo, são eliminadas quaisquer notificações que estavam sendo armazenadas para o dispositivo. Para obter mais informações, consulte [descrição geral do APN] e [mensagens sobre FCM].

Com os Hubs de notificação do Azure, pode transmitir uma chave de agregação através de um cabeçalho HTTP com a API de SendNotification genérico. Por exemplo, para o SDK de .NET, usaria `SendNotificationAsync`. A API de SendNotification também usa cabeçalhos HTTP que são transmitidos como-é o serviço de notificação push respectivos.

## <a name="self-diagnosis-tips"></a>Dicas de auto-diagnóstico

Aqui estão os caminhos para diagnosticar a causa de raiz de notificações ignoradas nos Hubs de notificação.

### <a name="verify-credentials"></a>Verificar credenciais

**Portal de Programador de serviço de notificação Push**

Verifique se as credenciais no respectivo push notification service portal do programador (APNs, FCM, serviço de notificação do Windows e assim por diante). Para obter mais informações, consulte [introdução aos Hubs de notificação do Azure].

**Portal do Azure**

Para rever e corresponder às credenciais com as que são obtidos a partir do portal de programador do push notification service, no portal do Azure, vá para o **políticas de acesso** separador.

![Portal do Azure, as políticas de acesso][4]

### <a name="verify-registrations"></a>Verifique os registos

**Visual Studio**

Se utilizar o Visual Studio para desenvolvimento, pode ligar ao Azure através do Explorador de servidores para ver e gerir vários serviços do Azure, incluindo os Hubs de notificação. Isto é principalmente útil para o seu ambiente de desenvolvimento/teste.

![Explorador de servidores do Visual Studio][9]

Pode ver e gerir todos os registos do seu hub, categorizados pela plataforma, de modo nativa ou registo de modelo, quaisquer etiquetas, identificador de serviço de notificação push, ID de registo e data de expiração. Também pode editar um registo nesta página. É especialmente útil para a edição de etiquetas.

Com o botão direito no seu **hub de notificação** no **Explorador de servidores**e selecione **diagnosticar**. 

![Visual Studio – Explorador de servidores - diagnosticar menu](./media/notification-hubs-diagnosing/diagnose-menu.png)

Verá a página seguinte: 

![Visual Studio – diagnosticar página](./media/notification-hubs-diagnosing/diagnose-page.png)

Mude para o **registos de dispositivos** página: 

![Registos de dispositivos do Visual Studio](./media/notification-hubs-diagnosing/VSRegistrations.png)

Pode usar **envio de teste** página para enviar uma mensagem de notificação de teste:

![Visual Studio – envio de teste](./media/notification-hubs-diagnosing/test-send-vs.png)

> [!NOTE]
> Utilize o Visual Studio para editar registos apenas durante o desenvolvimento e teste e com um número limitado de registos. Se precisar de editar os seus registos em massa, considere utilizar a exportar e importar funcionalidade de registo descrita em [exportar e modificar registos em massa](https://msdn.microsoft.com/library/dn790624.aspx).

**Explorador do Service Bus**

Muitos clientes utilizam [Explorador do Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer) para ver e gerir o seu hub de notificação. Explorador do Service Bus é um projeto de código-fonte aberto. 

### <a name="verify-message-notifications"></a>Certifique-se de notificações de mensagens

**Portal do Azure**

Para enviar uma notificação de teste para os seus clientes sem ter um serviço back-end em funcionamento, em **suporte + resolução de problemas**, selecione **envio de teste**.

![Funcionalidade de envio de teste no Azure][7]

**Visual Studio**

Também pode enviar notificações de teste a partir do Visual Studio.

![Testar a funcionalidade de envio no Visual Studio][10]

Para obter mais informações sobre como utilizar os Hubs de notificação com o Visual Studio Server Explorer, veja estes artigos:

* [Ver os registos de dispositivos para os hubs de notificação]
* [Aprofunde-se: Visual Studio 2013 Update 2 RC e o Azure SDK 2.3]
* [Apresentamos o lançamento do Visual Studio 2013 Update 3 e o Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Notificações de falha de depuração e reveja o resultado da notificação

**Propriedade de EnableTestSend**

Quando envia uma notificação através de Hubs de notificação, inicialmente, a notificação é colocada na fila para processamento nos Hubs de notificação. Os Hubs de notificação determina os destinos corretos e, em seguida, envia a notificação para o serviço de notificação push. Se estiver a utilizar a API REST ou qualquer um dos SDKs do cliente, com o retorno de chamada seu envio apenas significa que a mensagem foi enfileirada com êxito com os Hubs de notificação. Não tem quaisquer informações sobre o que aconteceu quando os Hubs de notificação, eventualmente, a mensagem enviada para o serviço de notificação push.

Se a notificação não chegue ao dispositivo cliente, é possível que ocorreu um erro quando os Hubs de notificação tentou entregar a mensagem para o serviço de notificação push. Por exemplo, o tamanho de payload pode exceder o máximo permitido pelo serviço de notificação push, ou as credenciais configuradas nos Hubs de notificação poderão ser inválidas.

Para obter informações sobre push erros de serviço de notificação, pode utilizar o [EnableTestSend] propriedade. Esta propriedade é ativada automaticamente quando enviar mensagens de teste a partir do portal ou do cliente do Visual Studio. Pode utilizar esta propriedade para ver informações detalhadas de depuração. Também pode utilizar a propriedade através de APIs. Atualmente, pode usá-lo no SDK do .NET. Eventualmente, serão adicionado para todos os SDKs do cliente.

Para utilizar o `EnableTestSend` propriedade com a chamada REST, acrescentar um parâmetro de cadeia de caracteres de consulta denominado *testar* ao final da sua chamada de envio. Por exemplo:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

**Exemplo (SDK do .NET)**

Eis um exemplo de como utilizar o SDK do .NET para enviar uma notificação de pop-up de nativo (alerta):

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

No final da execução, `result.State` declara `Enqueued`. Os resultados não fornecem qualquer informações sobre o que aconteceu com a notificação push.

Em seguida, pode usar o `EnableTestSend` propriedade booleana. Utilize o `EnableTestSend` propriedade enquanto inicializar `NotificationHubClient` para obter um estado detalhado sobre push erros de serviço de notificação que ocorrem quando a notificação é enviada. A chamada de envio demora mais tempo para retornar seja porque ele é retornado apenas depois dos Hubs de notificação forneceu a notificação para o serviço de notificação push para determinar o resultado.

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

**Resultado da amostra**

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Esta mensagem indica que credenciais inválidas estão configuradas nos Hubs de notificação, ou que existe um problema com os registros no hub. Recomendamos que elimine este registo e deixar que o cliente voltar a criar o registo antes de enviar a mensagem.

> [!NOTE]
> Utilizar o `EnableTestSend` propriedade é bastante limitada. Utilize esta opção apenas num ambiente de desenvolvimento/teste e com um conjunto limitado de registos. Iremos enviar notificações de depuração para apenas 10 dispositivos. Também temos um limite de envios de depuração para 10 por minuto de processamento.

### <a name="review-telemetry"></a>Rever a telemetria

**Portal do Azure**

No portal, pode obter uma visão geral de toda a atividade do seu hub de notificação.

1. Sobre o **descrição geral** separador, pode ver uma exibição agregada de registos, notificações e erros pela plataforma.

   ![Dashboard de descrição geral dos Hubs de notificação][5]

2. Sobre o **Monitor** separador, pode adicionar muitas outras métricas específicas da plataforma para uma visão mais profunda. Pode ver especificamente quaisquer erros relacionados com o serviço de notificação push que são devolvidos quando o serviço de Hubs de notificação tenta enviar a notificação para o serviço de notificação push.

   ![Registo de atividades de portal do Azure][6]

3. Comece por rever **mensagens de entrada**, **operações de registo**, e **notificações com êxito**. Em seguida, vá à aba por plataforma para rever erros que são específicos para o serviço de notificação push.

4. Se as definições de autenticação para o seu hub de notificação estão incorretas, a mensagem **erro de autenticação de PNS** aparece. É uma boa indicação para verificar as credenciais de serviço de notificação push.

**Acesso programático**

Para obter mais informações sobre o acesso programático, consulte [acesso programático telemetria].

> [!NOTE]
> Várias funcionalidades relacionadas com a telemetria, como a exportação e importação de registos e acesso de telemetria através de APIs, estão disponíveis apenas no escalão de serviço Standard. Se tentar usar esses recursos do gratuito ou básico de camada de serviço, receber uma mensagem de exceção, se utilizar o SDK e um erro HTTP 403 (proibido) se usar os recursos diretamente a partir de REST APIs.
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
[Descrição Geral dos Hubs de Notificação]: notification-hubs-push-notification-overview.md
[Introdução aos Hubs de notificação do Azure]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Modelos]: https://msdn.microsoft.com/library/dn530748.aspx
[APNs overview]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Acerca das mensagens do FCM]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Ver os registos de dispositivos para os hubs de notificação]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Aprofunde-se: Visual Studio 2013 Update 2 RC e o Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Apresentamos o lançamento do Visual Studio 2013 Update 3 e o Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Acesso programático telemetria]: https://msdn.microsoft.com/library/azure/dn458823.aspx
