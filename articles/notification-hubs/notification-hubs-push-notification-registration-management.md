---
title: Gestão de Registos
description: Este tópico explica como registar dispositivos com centros de notificação para receber notificações push.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/08/2019
ms.openlocfilehash: 00de9c803ef796eda8da609a4009e0a8cfcb3664
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455372"
---
# <a name="registration-management"></a>Gestão de registos

Este tópico explica como registar dispositivos com centros de notificação para receber notificações push. O tópico descreve as inscrições a um nível elevado, introduzindo depois os dois principais padrões de registo de dispositivos: registar-se diretamente do dispositivo para o centro de notificação e registar através de um backend de aplicação.

## <a name="what-is-device-registration"></a>O que é o registo do dispositivo

O registo do dispositivo com um Centro de Notificação é realizado através de um **Registo** ou **Instalação.**

### <a name="registrations"></a>Registos

Um registo associa o cabo do Serviço de Notificação da Plataforma (PNS) para um dispositivo com etiquetas e possivelmente um modelo. O cabo PNS pode ser um ChannelURI, um token do dispositivo ou identificação de registo da FCM. As etiquetas são utilizadas para encaminhar notificações para o conjunto correto de pegas do dispositivo. Para mais informações, consulte [Routing e Tag Expressions](notification-hubs-tags-segment-push-message.md). Os modelos são usados para implementar a transformação por registo. Para obter mais informações, veja [Templates](notification-hubs-templates-cross-platform-push-messages.md) (Modelos).

> [!NOTE]
> O Azure Notification Hubs suporta um máximo de 60 tags por dispositivo.

### <a name="installations"></a>Instalações

Uma Instalação é um registo reforçado que inclui um saco de propriedades relacionadas com o impulso. É a mais recente e melhor abordagem para registar os seus dispositivos. No entanto, ainda não é suportado pelo lado do cliente .NET SDK ([Notification Hub SDK para operações de backend).](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)  Isto significa que, se estiver a registar-se a partir do próprio dispositivo cliente, terá de utilizar a abordagem API DO [REST Hubs](/rest/api/notificationhubs/create-overwrite-installation) de Notificação para apoiar as instalações. Se estiver a utilizar um serviço de backend, deverá poder utilizar o [Notification Hub SDK para operações de backend](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Seguem-se algumas vantagens fundamentais para a utilização de instalações:

- Criar ou atualizar uma instalação é totalmente impotente. Para que possa voltar a experimentá-lo sem qualquer preocupação com as inscrições duplicadas.
- O modelo de instalação suporta`$InstallationId:{INSTALLATION_ID}`um formato de etiqueta especial que permite enviar uma notificação diretamente para o dispositivo específico. Por exemplo, se o código da aplicação `joe93developer` definir um ID de instalação para este dispositivo `$InstallationId:{joe93developer}` em particular, um desenvolvedor pode direcionar este dispositivo ao enviar uma notificação para a etiqueta. Isto permite-lhe direcionar um dispositivo específico sem ter de fazer qualquer codificação adicional.
- A utilização de instalações também permite fazer atualizações parciais de registo. A atualização parcial de uma instalação é solicitada com um método PATCH utilizando a [norma JSON-Patch](https://tools.ietf.org/html/rfc6902). Isto é útil quando pretende atualizar as etiquetas no registo. Não precisa retirar todo o registo e depois reenviar todas as etiquetas anteriores novamente.

Uma instalação pode conter as seguintes propriedades. Para obter uma listagem completa das propriedades de instalação, consulte [Criar ou substituir uma Instalação com propriedades](/rest/api/notificationhubs/create-overwrite-installation) REST API ou [Instalação](/dotnet/api/microsoft.azure.notificationhubs.installation).

```json
// Example installation format to show some supported properties
{
    installationId: "",
    expirationTime: "",
    tags: [],
    platform: "",
    pushChannel: "",
    ………
    templates: {
        "templateName1" : {
            body: "",
            tags: [] },
        "templateName2" : {
            body: "",
            // Headers are for Windows Store only
            headers: {
                "X-WNS-Type": "wns/tile" }
            tags: [] }
    },
    secondaryTiles: {
        "tileId1": {
            pushChannel: "",
            tags: [],
            templates: {
                "otherTemplate": {
                    bodyTemplate: "",
                    headers: {
                        ... }
                    tags: [] }
            }
        }
    }
}
```

> [!NOTE]
> Por defeito, as inscrições e instalações não expiram.

As inscrições e instalações devem conter um manijode PNS válido para cada dispositivo/canal. Como as pegas PNS só podem ser obtidas numa aplicação de cliente no dispositivo, um padrão é registar-se diretamente nesse dispositivo com a aplicação do cliente. Por outro lado, considerações de segurança e lógica de negócio relacionadacom tags podem exigir que você gere o registo do dispositivo no back-end da app.

> [!NOTE]
> A API instalações não suporta o serviço Baidu (embora a API de Registos o faça). 

### <a name="templates"></a>Modelos

Se pretender utilizar [modelos,](notification-hubs-templates-cross-platform-push-messages.md)a instalação do dispositivo também contém todos os modelos associados a esse dispositivo num formato JSON (ver amostra acima). Os nomes do modelo ajudam a direcionar diferentes modelos para o mesmo dispositivo.

Cada nome de modelo mapeia para um corpo de modelo e um conjunto opcional de tags. Além disso, cada plataforma pode ter propriedades de modelo adicionais. No caso da Windows Store (utilizando WNS) e Windows Phone 8 (utilizando MPNS), um conjunto adicional de cabeçalhos pode fazer parte do modelo. No caso de APNs, pode definir uma propriedade de validade para uma constante ou para uma expressão de modelo. Para uma listagem completa das propriedades de instalação ver, [criar ou substituir uma instalação com](/rest/api/notificationhubs/create-overwrite-installation) tópico REST.

### <a name="secondary-tiles-for-windows-store-apps"></a>Azulejos secundários para aplicativos de loja de janelas

Para aplicações de clientes da Windows Store, o envio de notificações para azulejos secundários é o mesmo que enviá-los para o principal. Isto também é suportado em instalações. Os azulejos secundários têm um ChannelUri diferente, que o SDK na aplicação do seu cliente lida de forma transparente.

O dicionário SecondaryTiles utiliza o mesmo TileId que é usado para criar o objeto SecondaryTiles na sua aplicação Windows Store. Tal como acontece com o ChannelUri primário, o ChannelUris de azulejos secundários pode mudar a qualquer momento. Para manter as instalações atualizadas no centro de notificação, o dispositivo deve atualizá-las com o canalUris atual dos azulejos secundários.

## <a name="registration-management-from-the-device"></a>Gestão de registos do dispositivo

Ao gerir o registo do dispositivo a partir de aplicações de clientes, o backend só é responsável pelo envio de notificações. As aplicações de clientes mantêm as pegas pNS atualizadas e registam etiquetas. A imagem que se segue ilustra este padrão.

![Registo do dispositivo](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

O dispositivo primeiro recupera o manípulo PNS do PNS e, em seguida, regista-se diretamente com o centro de notificação. Após o sucesso do registo, o backend da aplicação pode enviar uma notificação direcionada a esse registo. Para obter mais informações sobre como enviar notificações, consulte [Routing e Tag Expressions](notification-hubs-tags-segment-push-message.md).

Neste caso, utiliza apenas os direitos de escuta para aceder aos seus centros de notificação a partir do dispositivo. Para mais informações, consulte [segurança.](notification-hubs-push-notification-security.md)

Registar-se a partir do dispositivo é o método mais simples, mas tem algumas desvantagens:

- Uma aplicação de cliente só pode atualizar as suas tags quando a aplicação estiver ativa. Por exemplo, se um utilizador tiver dois dispositivos que registam tags relacionadas com equipas desportivas, quando o primeiro dispositivo se registar para uma etiqueta adicional (por exemplo, Seahawks), o segundo dispositivo não receberá as notificações sobre os Seahawks até que a aplicação no segundo dispositivo esteja executado uma segunda vez. De uma forma mais geral, quando as etiquetas são afetadas por vários dispositivos, gerir etiquetas a partir do backend é uma opção desejável.
- Uma vez que as aplicações podem ser pirateadas, garantir o registo a etiquetas específicas requer cuidados extra, como explica o artigo [Segurança](notification-hubs-push-notification-security.md).

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Código de exemplo para registar com um centro de notificação a partir de um dispositivo usando uma instalação

Neste momento, este é apenas suportado através da [API REST Hubs de Notificação](/rest/api/notificationhubs/create-overwrite-installation).

Também pode utilizar o método PATCH utilizando a [norma JSON-Patch](https://tools.ietf.org/html/rfc6902) para atualizar a instalação.

```csharp
class DeviceInstallation
{
    public string installationId { get; set; }
    public string platform { get; set; }
    public string pushChannel { get; set; }
    public string[] tags { get; set; }
}

private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
        string hubName, string listenConnectionString)
{
    if (deviceInstallation.installationId == null)
        return HttpStatusCode.BadRequest;

    // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
    ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
    string hubResource = "installations/" + deviceInstallation.installationId + "?";
    string apiVersion = "api-version=2015-04";

    // Determine the targetUri that we will sign
    string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

    //=== Generate SaS Security Token for Authorization header ===
    // See, https://msdn.microsoft.com/library/azure/dn495627.aspx
    string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

    using (var httpClient = new HttpClient())
    {
        string json = JsonConvert.SerializeObject(deviceInstallation);

        httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

        var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
        return response.StatusCode;
    }
}

var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

string installationId = null;
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored an installation ID in application data, create and store as application data.
if (!settings.ContainsKey("__NHInstallationId"))
{
    installationId = Guid.NewGuid().ToString();
    settings.Add("__NHInstallationId", installationId);
}

installationId = (string)settings["__NHInstallationId"];

var deviceInstallation = new DeviceInstallation
{
    installationId = installationId,
    platform = "wns",
    pushChannel = channel.Uri,
    //tags = tags.ToArray<string>()
};

var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                    "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

if (statusCode != HttpStatusCode.Accepted)
{
    var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
    dialog.Commands.Add(new UICommand("OK"));
    await dialog.ShowAsync();
}
else
{
    var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
    dialog.Commands.Add(new UICommand("OK"));
    await dialog.ShowAsync();
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Código de exemplo para registar-se com um centro de notificação a partir de um dispositivo que utilize um registo

Estes métodos criam ou atualizam um registo para o dispositivo em que são chamados. Isto significa que, para atualizar o manípulo ou as etiquetas, deve substituir a totalidade do registo. Lembre-se que as inscrições são transitórias, pelo que deve ter sempre uma loja fiável com as etiquetas atuais de que um dispositivo específico necessita.

```csharp
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// The Device ID from the PNS
var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

// If you are registering from the client itself, then store this registration ID in device
// storage. Then when the app starts, you can check if a registration ID already exists or not before
// creating.
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored a registration ID in application data, store in application data.
if (!settings.ContainsKey("__NHRegistrationId"))
{
    // make sure there are no existing registrations for this push handle (used for iOS and Android)    
    string newRegistrationId = null;
    var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
    foreach (RegistrationDescription registration in registrations)
    {
        if (newRegistrationId == null)
        {
            newRegistrationId = registration.RegistrationId;
        }
        else
        {
            await hub.DeleteRegistrationAsync(registration);
        }
    }

    newRegistrationId = await hub.CreateRegistrationIdAsync();

    settings.Add("__NHRegistrationId", newRegistrationId);
}

string regId = (string)settings["__NHRegistrationId"];

RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
registration.RegistrationId = regId;
registration.Tags = new HashSet<string>(YourTags);

try
{
    await hub.CreateOrUpdateRegistrationAsync(registration);
}
catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
{
    settings.Remove("__NHRegistrationId");
}
```

## <a name="registration-management-from-a-backend"></a>Gestão de registos a partir de um backend

Gerir os registos a partir do backend requer a escrita de código adicional. A aplicação do dispositivo deve fornecer o cabo PNS atualizado para o backend sempre que a aplicação começar (juntamente com etiquetas e modelos), e o backend deve atualizar este cabo no centro de notificação. A imagem seguinte ilustra este design.

![Gestão de registos](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

As vantagens de gerir registos a partir do backend incluem a capacidade de modificar etiquetas para registos mesmo quando a aplicação correspondente no dispositivo está inativa, e autenticar a app do cliente antes de adicionar uma etiqueta ao seu registo.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Código de exemplo para registar-se com um centro de notificação a partir de um backend usando uma instalação

O dispositivo cliente ainda obtém o seu manípulo PNS e propriedades de instalação relevantes como antes e chama uma API personalizada no backend que pode realizar o registo e autorizar etiquetas etc. O backend pode alavancar o Centro de [Notificação SDK para operações de backend](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Também pode utilizar o método PATCH utilizando a [norma JSON-Patch](https://tools.ietf.org/html/rfc6902) para atualizar a instalação.

```csharp
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// Custom API on the backend
public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
{

    Installation installation = new Installation();
    installation.InstallationId = deviceUpdate.InstallationId;
    installation.PushChannel = deviceUpdate.Handle;
    installation.Tags = deviceUpdate.Tags;

    switch (deviceUpdate.Platform)
    {
        case "mpns":
            installation.Platform = NotificationPlatform.Mpns;
            break;
        case "wns":
            installation.Platform = NotificationPlatform.Wns;
            break;
        case "apns":
            installation.Platform = NotificationPlatform.Apns;
            break;
        case "fcm":
            installation.Platform = NotificationPlatform.Fcm;
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }


    // In the backend we can control if a user is allowed to add tags
    //installation.Tags = new List<string>(deviceUpdate.Tags);
    //installation.Tags.Add("username:" + username);

    await hub.CreateOrUpdateInstallationAsync(installation);

    return Request.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Código de exemplo para registar-se com um centro de notificação a partir de um dispositivo usando um ID de registo

A partir do seu backend da sua aplicação, pode realizar operações básicas de CRUDS nos registos. Por exemplo:

```csharp
var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

// create a registration description object of the correct type, e.g.
var reg = new WindowsRegistrationDescription(channelUri, tags);

// Create
await hub.CreateRegistrationAsync(reg);

// Get by ID
var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

// update
r.Tags.Add("myTag");

// update on hub
await hub.UpdateRegistrationAsync(r);

// delete
await hub.DeleteRegistrationAsync(r);
```

O backend deve lidar com a moeda entre as atualizações de registo. A Service Bus oferece um controlo de condivisões otimista para a gestão de registos. A nível de HTTP, esta é implementada com a utilização do ETag em operações de gestão de registos. Esta funcionalidade é utilizada de forma transparente pelos SDKs da Microsoft, que atiram uma exceção se uma atualização for rejeitada por razões de condivisa. O backend da aplicação é responsável por lidar com estas exceções e refazer a atualização se necessário.
