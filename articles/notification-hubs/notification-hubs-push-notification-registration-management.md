---
title: Gestão de registos
description: Este tópico explica como registar dispositivos com os hubs de notificação para receber notificações push.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.author: jowargo
ms.date: 01/23/2019
ms.openlocfilehash: 028e9a2973ed524037f6415d9e802f947458cfa6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58166774"
---
# <a name="registration-management"></a>Gestão de registos

## <a name="overview"></a>Descrição geral

Este tópico explica como registar dispositivos com os hubs de notificação para receber notificações push. O tópico descreve os registros num alto nível, em seguida, apresenta dois padrões principais para registar dispositivos: registo do dispositivo diretamente para o hub de notificação e registar por meio de um back-end de aplicação.

## <a name="what-is-device-registration"></a>O que é o registo de dispositivos

Registo de dispositivos no Hub de notificação é realizado através de um **Registro** ou **instalação**.

### <a name="registrations"></a>Registos

Um registo associa o identificador de serviço de notificação de plataforma (PNS) para um dispositivo com etiquetas e, possivelmente, um modelo. O identificador PNS poderia ser um ChannelURI, o token do dispositivo ou o id de registo do FCM. As etiquetas são utilizadas para encaminhar as notificações para o conjunto correto de identificadores de dispositivo. Para obter mais informações, consulte [encaminhamento e expressões de etiqueta](notification-hubs-tags-segment-push-message.md). Os modelos são utilizados para implementar a transformação de por registo. Para obter mais informações, veja [Templates](notification-hubs-templates-cross-platform-push-messages.md) (Modelos).

> [!NOTE]
> Os Hubs de notificação do Azure suporta um máximo de 60 etiquetas por registo.

### <a name="installations"></a>Instalações

Uma instalação é uma avançada propriedades relacionadas de registo que inclui uma matriz de push. É a abordagem melhor e mais recente para registar os seus dispositivos. No entanto, não é suportado pelo SDK de .NET do lado do cliente ([SDK do Hub de notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) até o momento.  Isso significa que se está a registar a partir do próprio dispositivo cliente, teria de utilizar o [API de REST dos Hubs de notificação](https://msdn.microsoft.com/library/mt621153.aspx) abordagem para dar suporte a instalações. Se estiver a utilizar um serviço de back-end, deverá conseguir utilizar [SDK do Hub de notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Seguem-se algumas vantagens principais para utilizar as instalações:

- Criar ou atualizar uma instalação totalmente é idempotente. Por isso, pode repetir sem que se preocupar registos duplicados.
- O modelo de instalação torna mais fácil fazer pushes individuais - direcionamento de dispositivo específico. Uma etiqueta de sistema **"$InstallationId: [installationId]"** é adicionado automaticamente com cada registo de instalação com base. Portanto, pode chamar o envio para esta etiqueta para um dispositivo específico de destino sem ter de fazer qualquer codificação adicionais.
- Utilizar as instalações também lhe permite fazer atualizações parciais de registo. A atualização parcial de uma instalação for pedida com um método PATCH a utilizar o [padrão de JSON-Patch](https://tools.ietf.org/html/rfc6902). Isto é útil quando pretende atualizar as etiquetas no registo. Não precisa obter o registo completo e, em seguida, reenviar novamente todas as marcas anteriores.

Uma instalação pode conter as seguintes propriedades. Para obter uma lista completa das propriedades de instalação, consulte [criar ou substituir uma instalação com a REST API](https://msdn.microsoft.com/library/azure/mt621153.aspx) ou [das propriedades de instalação](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation_properties.aspx).

```javascript
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
> Por predefinição, registos e as instalações não expiram.

Instalações de registos e tem de conter um identificador PNS válido para cada canal/dispositivo. Como identificadores PNS só podem ser adquiridas num aplicativo de cliente no dispositivo, um padrão é Registre-se diretamente no que o dispositivo com a aplicação de cliente. Por outro lado, considerações de segurança e a lógica de negócios relacionados com as etiquetas podem implicar a Gerir registo de dispositivos no back-end de aplicação.

### <a name="templates"></a>Modelos

Se quiser usar [modelos](notification-hubs-templates-cross-platform-push-messages.md), a instalação de dispositivo também contém todos os modelos associados esse dispositivo num JSON formatar (veja o exemplo acima). Os nomes de modelo ajudam a modelos diferentes de destino para o mesmo dispositivo.

Cada nome de modelo é mapeado para um corpo de modelo e um conjunto opcional de etiquetas. Além disso, cada plataforma pode ter propriedades de modelo adicional. Para Store do Windows (com o WNS) e Windows Phone 8 (usando o MPNS), um conjunto adicional de cabeçalhos pode ser parte do modelo. No caso do APNs, pode definir uma propriedade de expiração para uma constante ou para uma expressão de modelo. Para obter uma listagem completa da instalação propriedades, consulte [criar ou substituir uma instalação com REST](https://msdn.microsoft.com/library/azure/mt621153.aspx) tópico.

### <a name="secondary-tiles-for-windows-store-apps"></a>Blocos secundários para aplicativos da Windows Store

Para aplicativos de cliente da Windows Store, enviar notificações para os blocos secundários é igual a enviá-los para o primário. Isso também é suportado em instalações. Blocos secundários têm um ChannelUri diferente, o que o SDK na sua aplicação de cliente processa de forma transparente.

O dicionário SecondaryTiles utiliza o mesmo TileId que é utilizado para criar o objeto de SecondaryTiles em seu aplicativo da Windows Store.
Tal como acontece com o ChannelUri primário, ChannelUris de blocos secundários pode alterar a qualquer momento. Para manter as instalações no hub de notificações atualizado, o dispositivo deve atualizá-los com o atual ChannelUris dos mosaicos secundários.

## <a name="registration-management-from-the-device"></a>Gestão de registo do dispositivo

Quando gerir registo de dispositivos a partir de aplicações de cliente, o back-end só é responsável pelo envio de notificações. Aplicações do cliente manter os identificadores PNS atualizados e registe-se as etiquetas. A imagem seguinte ilustra este padrão.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

O dispositivo primeiro recupera o identificador PNS do PNS, em seguida, regista com o hub de notificação diretamente. Após o registo for bem sucedido, o back-end de aplicação pode enviar uma notificação de direcionamento esse registo. Para obter mais informações sobre como enviar notificações, consulte [encaminhamento e expressões de etiqueta](notification-hubs-tags-segment-push-message.md).

Neste caso, usar apenas direitos de escuta para acessar os hubs de notificação do dispositivo. Para obter mais informações, consulte [segurança](notification-hubs-push-notification-security.md).

Registro do dispositivo é o método mais simples, mas tem algumas desvantagens:

- Uma aplicação de cliente só pode atualizar suas marcas quando a aplicação está ativa. Por exemplo, se um utilizador tiver dois dispositivos registar marcas relacionadas às equipes de desporto, quando o primeiro dispositivo registra uma marca adicionais (por exemplo, Seahawks), o segundo dispositivo não irá receber as notificações sobre os Seahawks até que a aplicação no dispositivo segundo está executar uma segunda vez. Geralmente, quando as etiquetas são afetadas por vários dispositivos, gerir etiquetas de back-end é uma opção de desejável.
- Uma vez que as aplicações podem ser invadido por hackers, proteger o registo para etiquetas específicas requer cuidado extra, conforme explicado na seção "segurança ao nível da marca."

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Código de exemplo para registar com um hub de notificação de um dispositivo com uma instalação

Neste momento, isso só é suportado com o [API de REST dos Hubs de notificação](https://msdn.microsoft.com/library/mt621153.aspx).

Também pode utilizar o método PATCH a utilizar o [padrão de JSON-Patch](https://tools.ietf.org/html/rfc6902) para atualizar a instalação.

```
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

// If we have not stored an installation id in application data, create and store as application data.
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

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Código de exemplo para registar com um hub de notificação de um dispositivo com um registo

Esses métodos criar ou atualizar um registo para o dispositivo no qual eles são chamados. Isso significa que, para atualizar o identificador ou as etiquetas, deve substituir o registo completo. Lembre-se de que os registos são transitórios, pelo que deve ter sempre um armazenamento fiável com as etiquetas atuais que precisa de um dispositivo específico.

```
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// The Device id from the PNS
var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

// If you are registering from the client itself, then store this registration id in device
// storage. Then when the app starts, you can check if a registration id already exists or not before
// creating.
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored a registration id in application data, store in application data.
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

## <a name="registration-management-from-a-backend"></a>Gestão de registo de um back-end

A gestão de registos do back-end requer a escrever código adicional. A aplicação do dispositivo tem de fornecer o PNS atualizado identificador para o back-end, sempre que a aplicação for iniciada (juntamente com etiquetas e modelos), e o back-end tem de atualizar este identificador no hub de notificação. A imagem seguinte ilustra esta estrutura.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

As vantagens da gestão de registos do back-end incluem a capacidade de alterar etiquetas para registos, mesmo quando a aplicação correspondente no dispositivo está inativa e autenticar a aplicação de cliente antes de adicionar uma etiqueta para seu registro.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Código de exemplo para registar com um hub de notificação de um back-end com uma instalação

O dispositivo de cliente ainda obtém seu identificador PNS e propriedades de instalação relevantes como antes e chama uma API personalizada no back-end que pode efetuar o registo e autorizar etiquetas etc. O back-end pode tirar partido da [SDK do Hub de notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Também pode utilizar o método PATCH a utilizar o [padrão de JSON-Patch](https://tools.ietf.org/html/rfc6902) para atualizar a instalação.

```
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

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Código de exemplo para registar com um hub de notificação de um dispositivo com um ID de registo

De sua back-end da aplicação, pode executar operações básicas de CRUDS registarem. Por exemplo:

```
var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

// create a registration description object of the correct type, e.g.
var reg = new WindowsRegistrationDescription(channelUri, tags);

// Create
await hub.CreateRegistrationAsync(reg);

// Get by id
var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

// update
r.Tags.Add("myTag");

// update on hub
await hub.UpdateRegistrationAsync(r);

// delete
await hub.DeleteRegistrationAsync(r);
```

O back-end tem de processar a simultaneidade entre as atualizações do registo. Do Service Bus oferece controle de simultaneidade otimista para a gestão de registo. No nível HTTP, isso é implementado com o uso de ETag nas operações de gestão do registo. Esta funcionalidade de forma transparente é utilizada pelo SDKs da Microsoft, que lança uma exceção se uma atualização é rejeitada por motivos de simultaneidade. O back-end de aplicação é responsável por manipular essas exceções e tentar novamente a atualização, se necessário.
