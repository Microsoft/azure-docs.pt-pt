---
title: Gerenciamento de registro
description: Este tópico explica como registrar dispositivos com hubs de notificação para receber notificações por push.
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
ms.date: 04/08/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/08/2019
ms.openlocfilehash: 0725b4fc80fc3a41491bdb9ed084d33b36b490b8
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213086"
---
# <a name="registration-management"></a>Gestão de registos

## <a name="overview"></a>Descrição geral

Este tópico explica como registrar dispositivos com hubs de notificação para receber notificações por push. O tópico descreve os registros em um alto nível e, em seguida, apresenta os dois padrões principais para o registro de dispositivos: registro do dispositivo diretamente no Hub de notificação e registro por meio de um back-end do aplicativo.

## <a name="what-is-device-registration"></a>O que é registro de dispositivo

O registro de dispositivo com um hub de notificação é realizado usando um **registro** ou uma **instalação**.

### <a name="registrations"></a>Registos

Um registro associa o identificador PNS (serviço de notificação de plataforma) para um dispositivo com marcas e possivelmente um modelo. O identificador PNS pode ser um ChannelURI, um token de dispositivo ou uma ID de registro FCM. As marcas são usadas para rotear notificações para o conjunto correto de identificadores de dispositivo. Para obter mais informações, consulte [expressões de marca e de roteamento](notification-hubs-tags-segment-push-message.md). Os modelos são usados para implementar a transformação por registro. Para obter mais informações, veja [Templates](notification-hubs-templates-cross-platform-push-messages.md) (Modelos).

> [!NOTE]
> Os hubs de notificação do Azure dão suporte a um máximo de 60 marcas por dispositivo.

### <a name="installations"></a>Permanecer

Uma instalação é um registro aprimorado que inclui uma bolsa de propriedades relacionadas ao Push. Trata-se da abordagem mais recente e melhor para registrar seus dispositivos. No entanto, ele não tem suporte do SDK do .NET do lado do cliente ([SDK do hub de notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) a partir de ainda.  Isso significa que se você estiver registrando a partir do próprio dispositivo cliente, precisaria usar a abordagem da [API REST dos hubs de notificação](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation) para dar suporte a instalações. Se você estiver usando um serviço de back-end, deverá ser capaz de usar o [SDK do hub de notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

A seguir estão algumas das principais vantagens para o uso de instalações do:

- Criar ou atualizar uma instalação é totalmente idempotente. Portanto, você pode tentar novamente sem qualquer preocupação sobre registros duplicados.
- O modelo de instalação do oferece suporte a um`$InstallationId:{INSTALLATION_ID}`formato de marca especial () que permite enviar uma notificação diretamente para o dispositivo específico. Por exemplo, se o código do aplicativo definir uma ID de `joe93developer` instalação para esse dispositivo específico, um desenvolvedor poderá direcionar esse dispositivo ao enviar uma notificação para a `$InstallationId:{joe93developer}` marca. Isso permite que você direcione um dispositivo específico sem precisar fazer nenhuma codificação adicional.
- O uso de instalações também permite que você faça atualizações parciais de registro. A atualização parcial de uma instalação é solicitada com um método de PATCH usando o [padrão JSON-patch](https://tools.ietf.org/html/rfc6902). Isso é útil quando você deseja atualizar as marcas no registro. Você não precisa retirar todo o registro e reenviar todas as marcas anteriores novamente.

Uma instalação pode conter as propriedades a seguir. Para obter uma lista completa das propriedades de instalação, consulte [criar ou substituir uma instalação com a API REST](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation) ou [as propriedades de instalação](https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.installation).

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
> Por padrão, os registros e as instalações não expiram.

Os registros e as instalações devem conter um identificador PNS válido para cada dispositivo/canal. Como os identificadores de PNS só podem ser obtidos em um aplicativo cliente no dispositivo, um padrão é registrar-se diretamente no dispositivo com o aplicativo cliente. Por outro lado, as considerações de segurança e a lógica de negócios relacionadas a marcas podem exigir que você gerencie o registro de dispositivos no back-end do aplicativo.

> [!NOTE]
> A API de instalações não oferece suporte ao serviço Baidu (embora a API de registros faça isso). 

### <a name="templates"></a>Modelos

Se você quiser usar [modelos](notification-hubs-templates-cross-platform-push-messages.md), a instalação do dispositivo também manterá todos os modelos associados a esse dispositivo em um formato JSON (consulte o exemplo acima). Os nomes de modelo ajudam a direcionar modelos diferentes para o mesmo dispositivo.

Cada nome de modelo é mapeado para um corpo de modelo e um conjunto opcional de marcas. Além disso, cada plataforma pode ter propriedades de modelo adicionais. Para a Windows Store (usando WNS) e o Windows Phone 8 (usando MPNS), um conjunto adicional de cabeçalhos pode fazer parte do modelo. No caso do APNs, você pode definir uma propriedade de expiração como uma constante ou uma expressão de modelo. Para obter uma lista completa das propriedades de instalação, consulte [criar ou substituir um tópico de instalação com REST](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation) .

### <a name="secondary-tiles-for-windows-store-apps"></a>Blocos secundários para aplicativos da Windows Store

Para aplicativos cliente da Windows Store, o envio de notificações para blocos secundários é o mesmo que enviá-los para o primário. Isso também é suportado em instalações do. Os blocos secundários têm um ChannelUri diferente, que o SDK em seu aplicativo cliente manipula de forma transparente.

O dicionário SecondaryTiles usa o mesmo Tileid usado para criar o objeto SecondaryTiles em seu aplicativo da Windows Store. Assim como acontece com o ChannelUri primário, os ChannelUris de blocos secundários podem mudar a qualquer momento. Para manter as instalações no Hub de notificação atualizadas, o dispositivo deve atualizá-las com a ChannelUris atual dos blocos secundários.

## <a name="registration-management-from-the-device"></a>Gerenciamento de registro do dispositivo

Ao gerenciar o registro de dispositivos de aplicativos cliente, o back-end é responsável somente pelo envio de notificações. Os aplicativos cliente mantêm PNS identificadores atualizados e registram as marcas. A figura a seguir ilustra esse padrão.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

O dispositivo primeiro recupera o identificador PNS do PNS e, em seguida, registra-se diretamente no Hub de notificação. Depois que o registro for bem-sucedido, o back-end do aplicativo poderá enviar uma notificação direcionando esse registro. Para obter mais informações sobre como enviar notificações, consulte [expressões de marca e de roteamento](notification-hubs-tags-segment-push-message.md).

Nesse caso, você usa apenas os direitos de escuta para acessar os hubs de notificação do dispositivo. Para obter mais informações, consulte [segurança](notification-hubs-push-notification-security.md).

O registro do dispositivo é o método mais simples, mas tem algumas desvantagens:

- Um aplicativo cliente só pode atualizar suas marcas quando o aplicativo está ativo. Por exemplo, se um usuário tiver dois dispositivos que registram marcas relacionadas às equipes de esporte, quando o primeiro dispositivo se registra para uma marca adicional (por exemplo, Seahawks), o segundo dispositivo não receberá as notificações sobre o Seahawks até que o aplicativo no segundo dispositivo seja executado uma segunda vez. Em geral, quando as marcas são afetadas por vários dispositivos, o gerenciamento de marcas do back-end é uma opção desejável.
- Como os aplicativos podem ser invadidos, proteger o registro para marcas específicas requer cuidado extra, conforme explicado na seção "segurança em nível de marca".

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Exemplo de código para registrar com um hub de notificação de um dispositivo usando uma instalação

Neste momento, só há suporte para isso usando a [API REST dos hubs de notificação](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation).

Você também pode usar o método PATCH usando o [padrão JSON-patch](https://tools.ietf.org/html/rfc6902) para atualizar a instalação.

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

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Exemplo de código para registrar com um hub de notificação de um dispositivo usando um registro

Esses métodos criam ou atualizam um registro para o dispositivo no qual eles são chamados. Isso significa que, para atualizar o identificador ou as marcas, você deve substituir todo o registro. Lembre-se de que os registros são transitórios, portanto, você deve sempre ter um armazenamento confiável com as marcas atuais de que um dispositivo específico precisa.

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

## <a name="registration-management-from-a-backend"></a>Gerenciamento de registro de um back-end

O gerenciamento de registros do back-end requer a escrita de código adicional. O aplicativo do dispositivo deve fornecer o identificador PNS atualizado para o back-end sempre que o aplicativo é iniciado (juntamente com marcas e modelos) e o back-end deve atualizar esse identificador no Hub de notificação. A figura a seguir ilustra esse design.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

As vantagens de gerenciar os registros do back-end incluem a capacidade de modificar marcas para registros, mesmo quando o aplicativo correspondente no dispositivo está inativo e para autenticar o aplicativo cliente antes de adicionar uma marca ao seu registro.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Exemplo de código para registrar com um hub de notificação de um back-end usando uma instalação

O dispositivo cliente ainda obtém seu identificador PNS e as propriedades de instalação relevantes como antes e chama uma API personalizada no back-end que pode executar o registro e autorizar as marcas etc. O back-end pode aproveitar o [SDK do hub de notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Você também pode usar o método PATCH usando o [padrão JSON-patch](https://tools.ietf.org/html/rfc6902) para atualizar a instalação.

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

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Exemplo de código para registrar com um hub de notificação de um dispositivo usando uma ID de registro

No back-end do aplicativo, você pode executar operações CRUD básicas em registros. Por exemplo:

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

O back-end deve lidar com a simultaneidade entre atualizações de registro. O barramento de serviço oferece controle de simultaneidade otimista para gerenciamento de registro. No nível de HTTP, isso é implementado com o uso de ETag nas operações de gerenciamento de registro. Esse recurso é usado de forma transparente pelos SDKs da Microsoft, que geram uma exceção se uma atualização é rejeitada por motivos de simultaneidade. O back-end do aplicativo é responsável por lidar com essas exceções e repetir a atualização, se necessário.
