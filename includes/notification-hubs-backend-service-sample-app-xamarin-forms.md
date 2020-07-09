---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: fc479522b3fd436ff02ff6b8985bdeddb66da90a
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85448779"
---
### <a name="create-the-xamarinforms-solution"></a>Criar a solução Xamarin.Forms

1. No **Visual Studio,** crie uma nova solução **Xamarin.Forms** utilizando a **App formas em branco** como modelo e inserindo *pushDemo* para o nome do **projeto.**

    > [!NOTE]
    > No **diálogo Configure** o seu diálogo de aplicação formulários em branco, certifique-se de que o **Identificador de Organização** corresponde ao valor utilizado anteriormente e que tanto os alvos **Android** como **iOS** são verificados.

1. **Controlo**  +  **Clique** na solução *PushDemo* e, em seguida, escolha **Pacotes NuGet de Atualização**.
1. **Controlo**  +  **Clique** na solução *PushDemo* e, em seguida, escolha **Gerir pacotes NuGet..**.
1. Procure **Newtonsoft.Js** e certifique-se de que está verificado.
1. Clique **em Adicionar Pacotes**e, em seguida, clique em **Aceitar** quando solicitado para aceitar os termos da licença.
1. Construa e execute a aplicação em cada plataforma-alvo **(Command**  +  **Enter**) para testar a aplicação modelo executada no(s) do seu dispositivo.

### <a name="implement-the-cross-platform-components"></a>Implementar os componentes de plataforma cruzada

1. **Controlo**  +  **Clique** no projeto **PushDemo,** escolha **Nova Pasta** no menu **Adicionar** e, em seguida, clique em **Adicionar** usando *modelos* como nome de **pasta**.

1. **Controlo**  +  **Clique** na pasta **Modelos** e, em seguida, escolha **Novo Ficheiro...** no menu **Adicionar.**

1. Selecione **General**  >  **Classe Geral Vazia**, *introduza DeviceInstallation.cs,* em seguida, adicione a seguinte implementação.

    ```csharp
    using System.Collections.Generic;
    using Newtonsoft.Json;

    namespace PushDemo.Models
    {
        public class DeviceInstallation
        {
            [JsonProperty("installationId")]
            public string InstallationId { get; set; }

            [JsonProperty("platform")]
            public string Platform { get; set; }

            [JsonProperty("pushChannel")]
            public string PushChannel { get; set; }

            [JsonProperty("tags")]
            public List<string> Tags { get; set; } = new List<string>();
        }
    }
    ```

1. Adicione uma **enumeração vazia** à pasta **Modelos** chamada *PushDemoAction.cs* com a seguinte implementação.

    ```csharp
    namespace PushDemo.Models
    {
        public enum PushDemoAction
        {
            ActionA,
            ActionB
        }
    }
    ```

1. Adicione uma nova pasta ao projeto **PushDemo** chamado *Services* e adicione uma **Classe Vazia** à pasta chamada *ServiceContainer.cs* com a seguinte implementação.

     ```csharp
    using System;
    using System.Collections.Generic;

    namespace PushDemo.Services
    {
        public static class ServiceContainer
        {
            static readonly Dictionary<Type, Lazy<object>> services
                = new Dictionary<Type, Lazy<object>>();

            public static void Register<T>(Func<T> function)
                => services[typeof(T)] = new Lazy<object>(() => function());

            public static T Resolve<T>()
                => (T)Resolve(typeof(T));

            public static object Resolve(Type type)
            {
                {
                    if (services.TryGetValue(type, out var service))
                        return service.Value;

                    throw new KeyNotFoundException($"Service not found for type '{type}'");
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Esta é uma versão aparada da classe [ServiceContainer](https://github.com/xamcat/mobcat-library/blob/master/MobCAT/ServiceContainer.cs) do repositório [XamCAT.](https://github.com/xamcat/mobcat-library) Será utilizado como um recipiente IoC leve (Inversão do Controlo).

1. Adicione uma **Interface Vazia** à pasta **serviços** chamada *IDeviceInstallationService.cs,* em seguida, adicione o seguinte código.

    ```csharp
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IDeviceInstallationService
        {
            string Token { get; set; }
            bool NotificationsSupported { get; }
            string GetDeviceId();
            DeviceInstallation GetDeviceInstallation(params string[] tags);
        }
    }
    ```

    > [!NOTE]
    > Esta interface será implementada e será implementada por cada alvo mais tarde para fornecer as informações de instalação específicas da plataforma e **de instalação do DeviceInstall** requeridas pelo serviço de backend.

1. Adicione outra **Interface Vazia** à pasta **Serviços** chamada *INotificationRegistrationService.cs*, em seguida, adicione o seguinte código.  

    ```csharp
    using System.Threading.Tasks;

    namespace PushDemo.Services
    {
        public interface INotificationRegistrationService
        {
            Task DeregisterDeviceAsync();
            Task RegisterDeviceAsync(params string[] tags);
            Task RefreshRegistrationAsync();
        }
    }
    ```

    > [!NOTE]
    > Isto irá lidar com a interação entre o cliente e o serviço backend.

1. Adicione outra **Interface Vazia** à pasta **Serviços** chamada *INotificationActionService.cs*, em seguida, adicione o seguinte código.  

    ```csharp
    namespace PushDemo.Services
    {
        public interface INotificationActionService
        {
            void TriggerAction(string action);
        }
    }
    ```

    > [!NOTE]
    > Isto é usado como um mecanismo simples para centralizar o manuseamento de ações de notificação.

1. Adicione uma **Interface Vazia** à pasta **de Serviços** chamada *IPushDemoNotificationActionService.cs* que deriva do *INotificationActionService,* com a seguinte implementação.  

    ```csharp
    using System;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IPushDemoNotificationActionService : INotificationActionService
        {
            event EventHandler<PushDemoAction> ActionTriggered;
        }
    }
    ```

    > [!NOTE]
    > Este tipo é específico da aplicação **PushDemo** e utiliza a enumeração **PushDemoAction** para identificar a ação que está a ser desencadeada de uma forma fortemente doctilografada.

1. Adicione uma **Classe Vazia** à pasta **serviços** chamada *NotificationRegistrationService.cs* implementando o *INotificationRegistrationService* com o seguinte código.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using PushDemo.Models;
    using Xamarin.Essentials;

    namespace PushDemo.Services
    {
        public class NotificationRegistrationService : INotificationRegistrationService
        {
            const string RequestUrl = "api/notifications/installations";
            const string CachedDeviceTokenKey = "cached_device_token";
            const string CachedTagsKey = "cached_tags";

            string _baseApiUrl;
            HttpClient _client;
            IDeviceInstallationService _deviceInstallationService;

            public NotificationRegistrationService(string baseApiUri, string apiKey)
            {
                _client = new HttpClient();
                _client.DefaultRequestHeaders.Add("Accept", "application/json");
                _client.DefaultRequestHeaders.Add("apikey", apiKey);

                _baseApiUrl = baseApiUri;
            }

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService = ServiceContainer.Resolve<IDeviceInstallationService>());

            public async Task DeregisterDeviceAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                if (cachedToken == null)
                    return;

                var deviceId = DeviceInstallationService?.GetDeviceId();

                if (string.IsNullOrWhiteSpace(deviceId))
                    throw new Exception("Unable to resolve an ID for the device.");

                await SendAsync(HttpMethod.Delete, $"{RequestUrl}/{deviceId}")
                    .ConfigureAwait(false);

                SecureStorage.Remove(CachedDeviceTokenKey);
                SecureStorage.Remove(CachedTagsKey);
            }

            public async Task RegisterDeviceAsync(params string[] tags)
            {
                var deviceInstallation = DeviceInstallationService?.GetDeviceInstallation(tags);

                await SendAsync<DeviceInstallation>(HttpMethod.Put, RequestUrl, deviceInstallation)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedDeviceTokenKey, deviceInstallation.PushChannel)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedTagsKey, JsonConvert.SerializeObject(tags));
            }

            public async Task RefreshRegistrationAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                var serializedTags = await SecureStorage.GetAsync(CachedTagsKey)
                    .ConfigureAwait(false);

                if (string.IsNullOrWhiteSpace(cachedToken) ||
                    string.IsNullOrWhiteSpace(serializedTags) ||
                    string.IsNullOrWhiteSpace(DeviceInstallationService.Token) ||
                    cachedToken == DeviceInstallationService.Token)
                    return;

                var tags = JsonConvert.DeserializeObject<string[]>(serializedTags);

                await RegisterDeviceAsync(tags);
            }

            async Task SendAsync<T>(HttpMethod requestType, string requestUri, T obj)
            {
                string serializedContent = null;

                await Task.Run(() => serializedContent = JsonConvert.SerializeObject(obj))
                    .ConfigureAwait(false);

                await SendAsync(requestType, requestUri, serializedContent);
            }

            async Task SendAsync(
                HttpMethod requestType,
                string requestUri,
                string jsonRequest = null)
            {
                var request = new HttpRequestMessage(requestType, new Uri($"{_baseApiUrl}{requestUri}"));

                if (jsonRequest != null)
                    request.Content = new StringContent(jsonRequest, Encoding.UTF8, "application/json");

                var response = await _client.SendAsync(request).ConfigureAwait(false);

                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

    > [!NOTE]
    > O argumento **apiKey** só é necessário se optar por completar os [clientes Authenticate utilizando uma secção chave API.](#authenticate-clients-using-an-api-key-optional)

1. Adicione uma **Classe Vazia** à pasta **serviços** chamada *PushDemoNotificationActionService.cs* implementando o *IPushDemoNotificationActionService* com o seguinte código.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public class PushDemoNotificationActionService : IPushDemoNotificationActionService
        {
            readonly Dictionary<string, PushDemoAction> _actionMappings = new Dictionary<string, PushDemoAction>
            {
                { "action_a", PushDemoAction.ActionA },
                { "action_b", PushDemoAction.ActionB }
            };

            public event EventHandler<PushDemoAction> ActionTriggered = delegate { };

            public void TriggerAction(string action)
            {
                if (!_actionMappings.TryGetValue(action, out var pushDemoAction))
                    return;

                List<Exception> exceptions = new List<Exception>();

                foreach (var handler in ActionTriggered?.GetInvocationList())
                {
                    try
                    {
                        handler.DynamicInvoke(this, pushDemoAction);
                    }
                    catch (Exception ex)
                    {
                        exceptions.Add(ex);
                    }
                }

                if (exceptions.Any())
                    throw new AggregateException(exceptions);
            }
        }
    }
    ```

1. Adicione uma **Classe Vazia** ao projeto **PushDemo** chamado *Config.cs* com a seguinte implementação.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            public static string ApiKey = "API_KEY";
            public static string BackendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
        }
    }
    ```

    > [!NOTE]
    > Isto é usado como uma forma simples de manter os segredos fora do controlo de fontes. Pode substituir estes valores como parte de uma construção automatizada ou sobreponhá-los utilizando uma classe parcial local. Vais fazer isto no próximo passo.
    >
    > O campo **ApiKey** só é necessário se optar por completar os [clientes Authenticate utilizando uma secção chave API.](#authenticate-clients-using-an-api-key-optional)

1. Adicione mais uma **Classe Vazia** ao projeto **PushDemo** desta vez chamado *Config.local_secrets.cs* com a seguinte implementação.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            static Config()
            {
                ApiKey = "<your_api_key>";
                BackendServiceEndpoint = "<your_api_app_url>";
            }
        }
    }
    ```

    > [!NOTE]
    > Substitua os valores do espaço reservado pelos seus. Devia ter anotar isto quando construiu o serviço de backend. O URL **da App API** deve ser ``https://<api_app_name>.azurewebsites.net/`` . Lembre-se de adicionar ``*.local_secrets.*`` ao seu ficheiro gitignore para evitar cometer este ficheiro.
    >
    > O campo **ApiKey** só é necessário se optar por completar os [clientes Authenticate utilizando uma secção chave API.](#authenticate-clients-using-an-api-key-optional)

1. Adicione uma **Classe Vazia** ao projeto **PushDemo** chamado *Bootstrap.cs* com a seguinte implementação.  

    ```csharp
    using System;
    using PushDemo.Services;

    namespace PushDemo
    {
        public static class Bootstrap
        {
            public static void Begin(Func<IDeviceInstallationService> deviceInstallationService)
            {
                ServiceContainer.Register(deviceInstallationService);

                ServiceContainer.Register<IPushDemoNotificationActionService>(()
                    => new PushDemoNotificationActionService());

                ServiceContainer.Register<INotificationRegistrationService>(()
                    => new NotificationRegistrationService(
                        Config.BackendServiceEndpoint,
                        Config.ApiKey));
            }
        }
    }
    ```

    > [!NOTE]
    > O método **Iniciar** será chamado por cada plataforma quando a aplicação for lançada através de uma implementação específica da plataforma do **IDeviceInstallationService**.
    >
    > O argumento de construtor **do** **NotificationRegistrationService** só é necessário se optar por completar os [clientes Authenticate utilizando uma secção chave API.](#authenticate-clients-using-an-api-key-optional)

### <a name="implement-the-cross-platform-ui"></a>Implementar a UI transversal

1. No projeto **PushDemo,** abra **o MainPage.xaml** e substitua o controlo **StackLayout** pelo seguinte.

    ```xml
    <StackLayout VerticalOptions="EndAndExpand"  
                 HorizontalOptions="FillAndExpand"
                 Padding="20,40">
        <Button x:Name="RegisterButton"
                Text="Register"
                Clicked="RegisterButtonClicked" />
        <Button x:Name="DeregisterButton"
                Text="Deregister"
                Clicked="DeregisterButtonClicked" />
    </StackLayout>
    ```

1. Agora, em **MainPage.xaml.cs**, adicione um campo de suporte **readonly** para armazenar uma referência à implementação do **INotificationRegistrationService.**

    ```csharp
    readonly INotificationRegistrationService _notificationRegistrationService;
    ```

1. No construtor **MainPage,** resolva a implementação do **INotificationRegistrationService** utilizando o **ServiceContainer** e atribua-o ao campo de suporte do *_NotificationRegistrationService._*

    ```csharp
    public MainPage()
    {
        InitializeComponent();

        _notificationRegistrationService =
            ServiceContainer.Resolve<INotificationRegistrationService>();
    }
    ```

1. Implementar os manipuladores de eventos para os botões **RegisterButton** e **Register** **DeregisterButton** Clique em eventos **clicados** que chamem os / métodos**correspondentes de registo de desregister.**

    ```csharp
    void RegisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.RegisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device registered"); });

    void DeregisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.DeregisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device deregistered"); });

    void ShowAlert(string message)
        => MainThread.BeginInvokeOnMainThread(()
            => DisplayAlert("PushDemo", message, "OK").ContinueWith((task)
                => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. Agora, em **App.xaml.cs,** certifique-se de que os seguintes espaços de nome são referenciados.

    ```csharp
    using PushDemo.Models;
    using PushDemo.Services;
    using Xamarin.Essentials;
    using Xamarin.Forms;
    ```

1. Implemente o manipulador de eventos para o evento **IPushDemoNotificationActionService** **ActionTriggered.**

    ```csharp
    void NotificationActionTriggered(object sender, PushDemoAction e)
        => ShowActionAlert(e);

    void ShowActionAlert(PushDemoAction action)
        => MainThread.BeginInvokeOnMainThread(()
            => MainPage?.DisplayAlert("PushDemo", $"{action} action received", "OK")
                .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. No construtor da **App,** resolva a implementação do **IPushNotificationActionService** utilizando o **ServiceContainer** e subscreva o evento **IPushDemoNotificationActionService** **ActionTriggered.**

    ```csharp
    public App()
    {
        InitializeComponent();

        ServiceContainer.Resolve<IPushDemoNotificationActionService>()
            .ActionTriggered += NotificationActionTriggered;

        MainPage = new MainPage();
    }
    ```

    > [!NOTE]
    > Isto é simplesmente para demonstrar a receção e propagação de ações de notificação de push. Normalmente, estes seriam manuseados silenciosamente, por exemplo, navegando para uma visão específica ou refrescando alguns dados em vez de exibir um alerta através da **página**raiz , **MainPage** neste caso.
