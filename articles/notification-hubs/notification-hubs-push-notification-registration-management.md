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
ms.date: 07/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 04/08/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 7807d28da459656938acb399eb8c621e4c292372
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89001574"
---
# <a name="registration-management"></a>Gestão de registos

Este tópico explica como registar dispositivos com centros de notificação para receber notificações push. O tópico descreve as inscrições a um nível elevado, introduzindo depois os dois principais padrões de registo dos dispositivos: registar-se do dispositivo diretamente para o centro de notificação e registar-se através de um backend de aplicação.

## <a name="what-is-device-registration"></a>O que é o registo do dispositivo

O registo do dispositivo com um Centro de Notificação é realizado através de um **Registo** ou **Instalação.**

### <a name="registrations"></a>Registos

Um registo associa o serviço de notificação da plataforma (PNS) para um dispositivo com etiquetas e possivelmente um modelo. O cabo PNS pode ser um ChannelURI, um símbolo do dispositivo ou um ID de registo FCM. As etiquetas são utilizadas para encaminhar notificações para o conjunto correto de alças do dispositivo. Para obter mais informações, consulte [Expressões de Encaminhamento e Etiquetagem](notification-hubs-tags-segment-push-message.md). Os modelos são usados para implementar a transformação por registo. Para obter mais informações, veja [Templates](notification-hubs-templates-cross-platform-push-messages.md) (Modelos).

> [!NOTE]
> O Azure Notification Hubs suporta um máximo de 60 tags por dispositivo.

### <a name="installations"></a>Instalações

Uma Instalação é um registo melhorado que inclui um saco de propriedades relacionadas com o push. É a mais recente e melhor abordagem para registar os seus dispositivos. No entanto, ainda não é suportado pelo lado do cliente .NET SDK ( Centro de[Notificação SDK para operações](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)de backend ).  Isto significa que, se estiver a registar-se a partir do próprio dispositivo cliente, terá de utilizar a abordagem [API do Centro](/rest/api/notificationhubs/create-overwrite-installation) de Notificações REST para suportar instalações. Se estiver a utilizar um serviço de backend, deverá poder utilizar [o Hub SDK de notificação para operações de backend](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Seguem-se algumas vantagens fundamentais para a utilização de instalações:

- A criação ou atualização de uma instalação é totalmente idempotente. Assim, pode voltar a tentar sem qualquer preocupação com as inscrições duplicadas.
- O modelo de instalação suporta um formato de marcação especial `$InstallationId:{INSTALLATION_ID}` ( ) que permite enviar uma notificação diretamente para o dispositivo específico. Por exemplo, se o código da aplicação definir um ID de instalação `joe93developer` para este dispositivo em particular, um desenvolvedor pode direcionar este dispositivo ao enviar uma notificação para a `$InstallationId:{joe93developer}` etiqueta. Isto permite-lhe direcionar um dispositivo específico sem ter de fazer qualquer codificação adicional.
- A utilização de instalações também permite fazer atualizações parciais de registo. Solicita-se a atualização parcial de uma instalação com um método PATCH utilizando a [norma JSON-Patch](https://tools.ietf.org/html/rfc6902). Isto é útil quando pretende atualizar as etiquetas no registo. Não tens de retirar todo o registo e reencamender todas as etiquetas anteriores outra vez.

Uma instalação pode conter as seguintes propriedades. Para obter uma listagem completa das propriedades de instalação, consulte [Criar ou Substituir uma Instalação com API REST](/rest/api/notificationhubs/create-overwrite-installation) ou Propriedades de [Instalação.](/dotnet/api/microsoft.azure.notificationhubs.installation)

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
> Por predefinição, os registos e instalações não caducam.

Os registos e instalações devem conter uma pega PNS válida para cada dispositivo/canal. Como as pegas PNS só podem ser obtidas numa aplicação de clientes no dispositivo, um padrão é registar-se diretamente nesse dispositivo com a aplicação do cliente. Por outro lado, considerações de segurança e lógica de negócio relacionadas com tags podem exigir que você gere o registo do dispositivo no back-end da app.

> [!NOTE]
> A API de Instalações não suporta o serviço Baidu (embora a API de Registos o faça). 

### <a name="templates"></a>Modelos

Se pretender utilizar [modelos,](notification-hubs-templates-cross-platform-push-messages.md)a instalação do dispositivo também contém todos os modelos associados a esse dispositivo num formato JSON (ver amostra acima). Os nomes do modelo ajudam a direcionar modelos diferentes para o mesmo dispositivo.

Cada nome de modelo mapeia para um corpo de modelo e um conjunto opcional de tags. Além disso, cada plataforma pode ter propriedades adicionais do modelo. Para a Windows Store (utilizando o WNS) e o Windows Phone 8 (utilizando o MPNS), um conjunto adicional de cabeçalhos pode fazer parte do modelo. No caso de APNs, você pode definir uma propriedade de validade para uma constante ou para uma expressão de modelo. Para uma listagem completa das propriedades de instalação ver, [criar ou substituir uma instalação com](/rest/api/notificationhubs/create-overwrite-installation) o tópico REST.

### <a name="secondary-tiles-for-windows-store-apps"></a>Azulejos secundários para apps da Windows Store

Para as aplicações de clientes da Windows Store, o envio de notificações para azulejos secundários é o mesmo que enviá-las para a primeira. Isto também é suportado em instalações. Os azulejos secundários têm um ChannelUri diferente, que o SDK na aplicação do seu cliente lida de forma transparente.

O dicionário SecondaryTiles utiliza o mesmo TileId que é usado para criar o objeto SecondaryTiles na sua aplicação Windows Store. Tal como acontece com o ChannelUri primário, os ChannelUris de azulejos secundários podem mudar a qualquer momento. Para manter as instalações no centro de notificação atualizadas, o dispositivo deve atualizá-las com os atuais ChannelUris dos azulejos secundários.

## <a name="registration-management-from-the-device"></a>Gestão de registo do dispositivo

Ao gerir o registo do dispositivo a partir de aplicações de clientes, o backend é apenas responsável pelo envio de notificações. As aplicações do cliente mantêm as alças de PNS atualizadas e registam tags. A imagem a seguir ilustra este padrão.

![Registo do dispositivo](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

O dispositivo recupera primeiro a pega PNS do PNS e depois regista-se diretamente com o centro de notificação. Após o sucesso do registo, o backend da aplicação pode enviar uma notificação direcionada a esse registo. Para obter mais informações sobre como enviar notificações, consulte [Expressões de Encaminhamento e Etiquetagem](notification-hubs-tags-segment-push-message.md).

Neste caso, utiliza apenas os direitos de escuta para aceder aos seus centros de notificação a partir do dispositivo. Para mais informações, consulte [a Segurança.](notification-hubs-push-notification-security.md)

Registar-se a partir do dispositivo é o método mais simples, mas tem algumas desvantagens:

- Uma aplicação de cliente só pode atualizar as suas etiquetas quando a aplicação está ativa. Por exemplo, se um utilizador tiver dois dispositivos que registam tags relacionadas com equipas desportivas, quando o primeiro dispositivo se regista para uma etiqueta adicional (por exemplo, Seahawks), o segundo dispositivo não receberá as notificações sobre os Seahawks até que a aplicação do segundo dispositivo seja executada uma segunda vez. De uma forma mais geral, quando as etiquetas são afetadas por vários dispositivos, gerir tags a partir do backend é uma opção desejável.
- Uma vez que as aplicações podem ser pirateadas, garantir o registo a etiquetas específicas requer cuidados extra, como explicado no artigo [Segurança](notification-hubs-push-notification-security.md).

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Código de exemplo para registar com um centro de notificação a partir de um dispositivo usando uma instalação

Neste momento, este só é suportado utilizando a API dos [Centros de Notificação](/rest/api/notificationhubs/create-overwrite-installation)REST .

Também pode utilizar o método PATCH utilizando a [norma JSON-Patch](https://tools.ietf.org/html/rfc6902) para atualizar a instalação.

```csharp
class DeviceInstallation
{
    public string installationId { get; set; }
    public string platform { get; set; }
    public string pushChannel { get; set; }
    public string[] tags { get; set; }

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
        // See https://msdn.microsoft.com/library/azure/dn495627.aspx
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
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Código de exemplo para registar com um centro de notificação a partir de um dispositivo usando um registo

Estes métodos criam ou atualizam um registo do dispositivo em que são chamados. Isto significa que, para atualizar o manípulo ou as etiquetas, deve substituir todo o registo. Lembre-se que as inscrições são transitórias, pelo que deve ter sempre uma loja fiável com as etiquetas atuais de que um dispositivo específico necessita.

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

A gestão dos registos a partir do backend requer a elaboração de código adicional. A aplicação do dispositivo deve fornecer o cabo PNS atualizado para o backend sempre que a aplicação começa (juntamente com tags e modelos), e o backend deve atualizar esta pega no centro de notificação. A imagem a seguir ilustra este design.

![Gestão de registos](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

As vantagens de gerir os registos a partir do backend incluem a capacidade de modificar tags para registos mesmo quando a aplicação correspondente no dispositivo está inativa, e de autenticar a app do cliente antes de adicionar uma etiqueta ao seu registo.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Código de exemplo para registar com um centro de notificação a partir de um backend usando uma instalação

O dispositivo cliente ainda obtém o seu cabo PNS e propriedades de instalação relevantes como antes e chama uma API personalizada no backend que pode realizar o registo e autorizar tags etc. O backend pode alavancar o [Hub SDK de notificação para operações de backend](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

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

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Código de exemplo para registar com um centro de notificação a partir de um dispositivo usando um ID de registo

A partir do seu backend da app, pode realizar operações básicas da CRUDS em registos. Por exemplo:

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

O backend deve lidar com a concordância entre as atualizações de registo. A Service Bus oferece um controlo de concordância otimista para a gestão do registo. A nível HTTP, este é implementado com a utilização de ETag em operações de gestão de registos. Esta funcionalidade é utilizada de forma transparente pelos SDKs da Microsoft, que lançam uma exceção se uma atualização for rejeitada por razões de concordância. O backend da aplicação é responsável por lidar com estas exceções e tentar novamente a atualização, se necessário.
