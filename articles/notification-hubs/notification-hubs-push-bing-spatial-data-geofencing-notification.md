---
title: Envie notificações push com os Hubs de Notificação Azure e Bing Spatial Data | Microsoft Docs
description: Neste tutorial, irá aprender a entregar notificações push com base na localização com Hubs de Notificação do Azure e Dados Geográficos do Bing.
services: notification-hubs
documentationcenter: windows
keywords: notificações push,notificações push
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: f41beea1-0d62-4418-9ffc-c9d70607a1b7
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 51ad23e67a77c28d0ad8a147168a0094f5de1796
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91578007"
---
# <a name="tutorial-send-location-based-push-notifications-with-notification-hubs-and-bing-spatial-data"></a>Tutorial: Enviar notificações push baseadas em localização com centros de notificação e dados espaciais de Bing

Neste tutorial, irá aprender a entregar notificações push com base na localização com Hubs de Notificação do Azure e Dados Geográficos do Bing.

Neste tutorial, siga os seguintes passos:

> [!div class="checklist"]
> * Configurar a origem de dados
> * Configurar a aplicação UWP
> * Configurar o back-end
> * Testar as notificações push na aplicação de Plataforma Universal do Windows (UWP)

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura Azure**. Se não tiver uma subscrição do Azure,  [crie uma conta Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Visual Studio 2015 Update 1](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) ou posterior ([Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409)).
* Versão mais recente do [Azure SDK](https://azure.microsoft.com/downloads/).
* [Conta do Dev Center do Bing Maps](https://www.bingmapsportal.com/) (pode criar uma gratuitamente e associá-la à conta Microsoft).

## <a name="set-up-the-data-source"></a>Configurar a origem de dados

1. Inicie sessão no [Dev Center do Bing Maps](https://www.bingmapsportal.com/).
2. Na barra de navegação superior, selecione **Origens de dados** e selecione **Gerir Origens de Dados**.

    ![Screenshot do Bing Maps Dev Center na página 'Gerir fontes de dados' com os dados do Upload como uma opção de fonte de dados delineada a vermelho.](./media/notification-hubs-geofence/bing-maps-manage-data.png)
3. Se não vir uma origem de dados existente, verá uma ligação para criar uma origem de dados. Selecione **Carregar dados como uma origem de dados**. Também pode utilizar o menu **de dados de fontes de dados.**  >  

    ![Screenshot da caixa de diálogo de fonte de dados.](./media/notification-hubs-geofence/bing-maps-create-data.png)
4. Crie um ficheiro `NotificationHubsGeofence.pipe` no seu disco rígido com o seguinte conteúdo: Neste tutorial, utilize um ficheiro baseado em tubos de amostra que enquadra uma área da orla marítima de São Francisco:

    ```text
    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    ```

    O ficheiro de pipe representa esta entidade:

    ![Screenshot de um mapa da orla marítima de São Francisco com um polígono vermelho delineando uma área do cais.](./media/notification-hubs-geofence/bing-maps-geofence.png)
5. Na página **Carregar uma origem de dados**, faça o seguinte:
   1. Selecione **pipe** para **Formato de dados**.
   2. Navegue e selecione o `NotificationHubGeofence.pipe` ficheiro que criou no passo anterior.
   3. Selecione o botão **Carregar**.

      > [!NOTE]
      > Poderá ser-lhe pedido que especifique uma nova chave para a **Chave Mestra** diferente da **Chave de Consulta**. Crie simplesmente uma nova chave através do dashboard e atualize a página de carregamento da origem de dados.
6. Uma vez carregado o ficheiro de dados, terá de se certificar de que pode publicar a origem de dados. Selecione **fontes de dados**  ->  **Gerir fontes de dados** como fez antes.
7. Selecione a origem de dados na lista e selecione **Publicar** na coluna **Ações**.

    ![Screenshot do Bing Maps Dev Center na página 'Gerir fontes de dados' com o separador dados geocodificado selecionado e a opção Publicar delineada a vermelho.](./media/notification-hubs-geofence/publish-button.png)
8. Mude para o separador **Origens dos Dados Publicados** e confirme que vê a origem de dados na lista.

    ![Screenshot do Bing Maps Dev Center na página 'Gerir fontes de dados' com o separador Fontes de Dados Publicados selecionado.](./media/notification-hubs-geofence/bing-maps-published-data.png)
9. Selecione **Editar**. Verá (de relance) as localizações que introduziu nos dados.

    ![Screenshot da página de dados da entidade Editar mostrando um mapa do oeste dos Estados Unidos e um ponto magenta sobre a orla marítima de São Francisco.](./media/notification-hubs-geofence/bing-maps-data-details.png)

    Nesta fase, o portal não mostra os limites do perímetro geográfico que criou – só precisa de uma confirmação de que a localização especificada está na vizinhança certa.
10. Tem, agora, todos os requisitos para a origem de dados. Para obter os detalhes acerca do URL do pedido para a chamada de API, no Bing Maps Dev Center, escolha **Origens de dados** e selecione **Informações das Origens de Dados**.

    ![Screenshot do Bing Maps Dev Center na página de informações de fonte de dados.](./media/notification-hubs-geofence/bing-maps-data-info.png)

    O **URL de Consulta** é o ponto final relativamente ao qual pode executar consultas para verificar se o dispositivo está ou não atualmente dentro dos limites de uma localização. Para executar esta verificação, basta executar uma chamada GET no URL de consulta, com os seguintes parâmetros anexados:

    ```text
    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY
    ```

    O Bing Maps efetua automaticamente os cálculos para ver se o dispositivo está dentro do perímetro geográfico. Uma vez executado o pedido por meio de um browser (ou cURL), obterá uma resposta JSON padrão:

    ![Screenshot da resposta padrão do JSON.](./media/notification-hubs-geofence/bing-maps-json.png)

    Esta resposta só aparece quando o ponto está, de facto, dentro dos limites designados. Se não estiver, obterá um registo de **resultados** vazio:

    ![Screenshot de uma resposta JSON com um balde de resultados vazio.](./media/notification-hubs-geofence/bing-maps-nores.png)

## <a name="set-up-the-uwp-application"></a>Configurar a aplicação UWP

1. No Visual Studio, inicie um novo projeto do tipo **Aplicação em Branco (Universal Windows)**.

    ![Screenshot de uma caixa de diálogo Visual Studio New Project com a app em branco (opção Universal Windows Visual C# em destaque.](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

    Assim que a criação de projeto estiver concluída, deverá ter o escudo para a aplicação em si. Agora vamos preparar tudo para a infraestrutura de geoesgrima. Uma vez que irá utilizar serviços Bing para esta solução, existe um ponto final público da API REST que lhe permite consultar molduras de localização específicas:

    ```text
    http://spatial.virtualearth.net/REST/v1/data/
    ```
    Especifique os seguintes parâmetros para fazer com que funcione:

   * **ID da Origem de Dados** e **Nome da Origem de Dados** – na API Bing Maps, origens de dados contêm vários metadados agrupados, como localizações e horas de expediente da operação.  
   * **Nome da Entidade** – a entidade que pretende utilizar como um ponto de referência para a notificação.
   * **Chave de API do Bing Maps** – a chave que obteve anteriormente quando criou a conta do Dev Center do Bing.

     Agora que já tem a origem de dados pronta, pode começar a trabalhar na aplicação UWP.
2. Ative os serviços de localização para a sua aplicação. Abra o ficheiro `Package.appxmanifest` no **Explorador de Soluções**.

    ![Screenshot do Solution Explorer com o ficheiro Package.appxmanifest em destaque.](./media/notification-hubs-geofence/vs-package-manifest.png)
3. No separador de propriedades do pacote que acabou de abrir, mude para o separador **Capacidades** e selecione **Localização**.

    ![Screenshot da caixa de diálogo Propriedades pacotes mostrando o separador Capabilites com a opção Localização realçada.](./media/notification-hubs-geofence/vs-package-location.png)
4. Crie uma nova pasta na solução com o nome `Core` e adicione um novo ficheiro dentro da mesma, denominado `LocationHelper.cs`:

    ![Screenshot do Solution Explorer com a nova pasta Core em destaque.](./media/notification-hubs-geofence/vs-location-helper.png)

    A classe `LocationHelper` tem código para obter a localização do utilizador através da API do sistema:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }
    ```

    Para saber mais sobre a localização do utilizador em aplicações UWP, consulte[a localização do utilizador.](/windows/uwp/maps-and-location/get-location)
5. Para verificar se a aquisição de localização está, de facto, a funcionar, abra o lado de código da sua página principal (`MainPage.xaml.cs`). Crie um novo processador de eventos para o evento `Loaded` no construtor `MainPage`.

    ```csharp
    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }
    ```

    A implementação do processador de eventos é feita do seguinte modo:

    ```csharp
    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }
    ```
6. Execute a aplicação e permita que a mesma aceda à sua localização.

    ![Screenshot do Let Notification Hubs Geo Fence acede à sua caixa de diálogo de localização.](./media/notification-hubs-geofence/notification-hubs-location-access.png)
7. Uma vez iniciada a aplicação, deverá conseguir ver as coordenadas na janela **Saída**:

    ![Screenshot da janela de saída mostrando as coordenadas.](./media/notification-hubs-geofence/notification-hubs-location-output.png)

    Agora que sabe que a aquisição de localização funciona, pode remover o manipulador de eventos Loaded, se quiser, porque não vai mais usá-lo.
8. O passo seguinte é capturar as alterações de localização. Na classe `LocationHelper`, adicione o processador de eventos para `PositionChanged`:

    ```csharp
    geolocator.PositionChanged += Geolocator_PositionChanged;
    ```

    A implementação apresenta as coordenadas de localização na janela **Saída**:

    ```csharp
    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }
    ```

## <a name="set-up-the-backend"></a>Configurar o back-end

1. Transfira o [Exemplo de Back-end do .NET a partir do GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).
2. Uma vez concluída a transferência, abra a pasta `NotifyUsers` e, em seguida, abra o ficheiro `NotifyUsers.sln` no Visual Studio.
3. Defina o projeto `AppBackend` como **Projeto de Arranque** e inicie-o.

    ![Screenshot do menu de clique direito da solução com a opção set como StartUp Project realçada.](./media/notification-hubs-geofence/vs-startup-project.png)

    O projeto já está configurado para enviar notificações push para os dispositivos de destino, por isso só precisa de fazer duas coisas – especificar a cadeia de ligação correta para o Hub de Notificação e adicionar identificação de limites para enviar a notificação apenas quando o utilizador estiver dentro do perímetro geográfico.

4. Para configurar a cadeia de ligação, na pasta `Models` abra `Notifications.cs`. A função `NotificationHubClient.CreateClientFromConnectionString` deve conter as informações acerca do Hub de Notificação que pode obter no [portal do Azure](https://portal.azure.com) (procure na página **Políticas de Acesso** em **Definições**). Guarde a configuração de ficheiro atualizada.
5. Crie um modelo para o resultado da API do Bing Maps. A maneira mais fácil de o fazer é abrir a `Models` pasta e escolher **Adicionar**  >  **Classe**. Dê-lhe o nome `GeofenceBoundary.cs`. Quando tiver terminado, copie o JSON da resposta da API que obteve na primeira secção. No Estúdio Visual, utilize **editar**  >  **pasta especial**  >  **JSON como classes**.

    Desta forma, garante que será anulada a serialização do objeto exatamente como se projetou. O conjunto de classes resultante deve assemelhar-se à seguinte classe:

    ```csharp
    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }
    ```
6. Em seguida, abra `Controllers` > `NotificationsController.cs`. Atualize a chamada Publicar para ter em conta a latitude e longitude do destino. Para o fazer, adicione duas cadeias à assinatura da função: `latitude` e `longitude`.

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)
    ```
7. Crie uma nova classe no projeto denominada `ApiHelper.cs` – irá utilizá-la para ligar ao Bing para verificar intersecções de limites do ponto. Implemente uma função `IsPointWithinBounds` conforme apresentado no seguinte código:

    ```csharp
    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }
    ```

    > [!IMPORTANT]
    > Não se esqueça de substituir o Ponto Final de API com o URL de consulta que obteve anteriormente no Dev Center do Bing (o mesmo se aplica à chave de API).

    Se não existirem resultados da consulta, isso significa que o ponto especificado está dentro dos limites do perímetro geográfico, pelo que a função devolve `true`. Se não existirem resultados, o Bing estará a informar que o ponto está fora do intervalo de pesquisa, pelo que a função devolve `false`.
8. No `NotificationsController.cs`, crie uma verificação mesmo antes da instrução switch:

    ```csharp
    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }
    ```

## <a name="test-push-notifications-in-the-uwp-app"></a>Testar as notificações push na aplicação UWP

1. Na aplicação UWP, já deve poder testar as notificações. N classe `LocationHelper`, crie uma nova função – `SendLocationToBackend`:

    ```csharp
    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }
    ```

    > [!NOTE]
    > Defina o `POST_URL` para a localização da sua aplicação Web implementada. Por enquanto, não faz mal executá-lo localmente, mas à medida que trabalha na implementação de uma versão pública, precisa de acolhê-la com um fornecedor externo.
2. Registe a aplicação UWP para enviar notificações push. No Estúdio Visual, escolha **a** aplicação Project  >  **Store** Associate com  >  **a loja.**

    ![Screenshot do menu de clique à direita da solução com a Loja e App Associada com as opções da Loja em destaque.](./media/notification-hubs-geofence/vs-associate-with-store.png)
3. Depois de iniciar sessão na conta de programador, certifique-se de que seleciona uma aplicação existente ou cria uma nova e associe-lhe o pacote.
4. Aceda ao Dev Center e abra a aplicação que criou. Escolha   >  **serviços Push Notificações**  >  **Ao Vivo Site**.

    ![Screenshot do Windows Dev Center exibindo a página de notificações Push com o site live services em destaque.](./media/notification-hubs-geofence/ms-live-services.png)
5. No site, tome nota do **Segredo da Aplicação** e do **SID do Pacote**. Precisa tanto no portal Azure – abra o seu centro de notificações, escolha **o Windows**  >  (WNS) dos **Serviços de Notificação** de Definições  >  **e** introduza as informações nos campos necessários.

    ![Screenshot mostrando a página de Definições com as opções de Serviços de Notificação e Windows (WNS) realçadas e os valores de SID e Chave de Segurança do Pacote preenchidos.](./media/notification-hubs-geofence/notification-hubs-wns.png)
6. Escolha **guardar**.
7. Abra **Referências**, no **Explorador de Soluções**, e selecione **Gerir Pacotes NuGet**. Adicione uma referência à **Biblioteca gerida do Microsoft Azure Service Bus**. Para tal, procure `WindowsAzure.Messaging.Managed` e adicione ao projeto.

    ![Screenshot da caixa de diálogo Manage Nuget Packages com o pacote WindowsAzure.Messaging.Managed em destaque.](./media/notification-hubs-geofence/vs-nuget.png)
8. Para fins de teste, crie novamente o processador de eventos `MainPage_Loaded` e adicione-lhe este fragmento de código:

    ```csharp
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }
    ```

    O código regista a aplicação com o Hub de Notificação. Está pronto para funcionar!
9. Em `LocationHelper`, no processador `Geolocator_PositionChanged`, pode adicionar um fragmento de código de teste que coloca forçadamente a localização dentro do perímetro geográfico:

    ```csharp
    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");
    ```

10. Como não está a transmitir as coordenadas reais (que podem não estar de momento dentro dos limites) e está a utilizar valores de teste predefinidos, verá aparecer uma notificação na atualização:

    ![Screenshot de um ambiente de trabalho do Windows exibindo a mensagem TEST.](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

## <a name="next-steps"></a>Passos seguintes

Existem alguns passos que poderá ter de seguir para se certificar de que a solução está pronta para produção.

1. Em primeiro lugar, tem de garantir que os perímetros geográficos são dinâmicos. Isto irá necessitar de algum trabalho adicional com a API do Bing para carregar novos limites na origem de dados existente. Para obter mais informações, veja [Documentação da API dos Serviços de Dados Geográficos do Bing](/bingmaps/spatial-data-services/).
2. Segundo, à medida que vai trabalhando para garantir que a entrega é efetuada aos participantes corretos, poderá querer segmentá-los através de [etiquetagem](notification-hubs-tags-segment-push-message.md).

A solução mostrada neste tutorial descreve um cenário em que poderá ter uma grande variedade de plataformas de destino, por isso não limita o perímetro geográfico para as capacidades específicas do sistema. Dito isto, a Plataforma Universal do Windows oferece capacidades para [detetar perímetros geográficos logo na primeira execução](/windows/uwp/maps-and-location/set-up-a-geofence).
