---
title: Criar um modelo de locatário (versão prévia) – serviço de fala
titleSuffix: Azure Cognitive Services
description: Gere automaticamente um modelo de locatário seguro e compatível (Fala Personalizada com dados do Office 365) que usa seus dados do Office 365 para fornecer o reconhecimento de fala ideal para os termos específicos da organização.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: 4fec6b93ad206ae3052df5f7763f3c146b7aa680
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446790"
---
# <a name="tutorial-create-a-tenant-model-preview"></a>Tutorial: criar um modelo de locatário (visualização)

O modelo de locatário (Fala Personalizada com dados do Office 365) é um serviço de aceitação para clientes do Office 365 Enterprise que gera automaticamente um modelo de reconhecimento de fala personalizado dos dados do Office 365 da sua organização. O modelo é otimizado para termos técnicos, jargão e nomes de pessoas, tudo de maneira segura e em conformidade.

> [!IMPORTANT]
> Se sua organização registrar-se usando o serviço de modelo de locatário, o serviço de fala poderá acessar o modelo de linguagem da sua organização. O modelo é gerado a partir de documentos e emails do grupo público do Office 365, que podem ser vistos por qualquer pessoa em sua organização. O administrador do Office 365 da sua organização pode ativar ou desativar o uso do modelo de linguagem em toda a organização no portal de administração do Office 365.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Registrar no modelo de locatário usando o centro de administração Microsoft 365
> * Obter uma chave de assinatura de fala
> * Criar um modelo de locatário
> * Implantar um modelo de locatário
> * Usar seu modelo de locatário com o SDK de fala

## <a name="enroll-in-the-tenant-model-service"></a>Registrar no serviço de modelo de locatário

Antes de implantar seu modelo de locatário, você precisa estar registrado no serviço de modelo de locatário. O registro é concluído no centro de administração Microsoft 365 e pode ser feito somente pelo administrador do Microsoft 365.

1. Inicie sessão no [centro de administração do Microsoft 365](https://admin.microsoft.com).

1. No painel esquerdo, selecione **configurações**, selecione **aplicativos**e, em seguida, selecione **serviços de fala do Azure**.

   ![O painel "suplementos de & de serviços"](media/tenant-language-model/tenant-language-model-enrollment.png)

1. Marque a caixa de seleção **permitir o modelo de linguagem em toda a organização** e, em seguida, selecione **salvar alterações**. 

   ![O painel serviços de fala do Azure](media/tenant-language-model/tenant-language-model-enrollment-2.png)

Para desativar a instância do modelo de locatário:
1. Repita as etapas 1 e 2 anteriores.
1. Desmarque a caixa de seleção **permitir o modelo de linguagem em toda a organização** e, em seguida, selecione **salvar alterações**.

## <a name="get-a-speech-subscription-key"></a>Obter uma chave de assinatura de fala

Para usar o modelo de locatário com o SDK de fala, você precisa de um recurso de fala e sua chave de assinatura associada.

1. Inicie sessão no [Portal do Azure](https://aka.ms/azureportal).
1. Selecione **criar um recurso**.
1. Na caixa de **pesquisa** , digite **fala**.
1. Na lista de resultados, selecione **fala**e, em seguida, selecione **criar**.
1. Siga as instruções na tela para criar o recurso. Confirme que:
   * O **local** é definido como **lesteus** ou **westus**.
   * O **tipo de preço** é definido como **S0**.
1. Selecione **Criar**.

   Depois de alguns minutos, o recurso é criado. A chave de assinatura está disponível na seção **visão geral** do recurso.

## <a name="create-a-language-model"></a>Criar um modelo de linguagem

Depois que o administrador tiver habilitado o modelo de locatário para sua organização, você poderá criar um modelo de linguagem baseado em seus dados do Office 365.

1. Entre no [Speech Studio](https://speech.microsoft.com/).
1. No canto superior direito, selecione **configurações** (ícone de engrenagem) e, em seguida, selecione **configurações de modelo de locatário**.

   ![O link "configurações do modelo de locatário"](media/tenant-language-model/tenant-language-settings.png)

   O Speech Studio exibe uma mensagem que informa se você está qualificado para criar um modelo de locatário.

   > [!NOTE]
   > Os clientes do Office 365 Enterprise no América do Norte estão qualificados para criar um modelo de locatário (inglês). Se você for um Sistema de Proteção de Dados do Cliente, chave do cliente ou cliente do governo do Office 365, esse recurso não estará disponível. Para determinar se você é um cliente de Sistema de Proteção de Dados do Cliente ou de chave do cliente, consulte:
   > * [Sistema de Proteção de Dados do Cliente](https://docs.microsoft.com/office365/securitycompliance/controlling-your-data-using-customer-key#FastTrack)
   > * [Chave do cliente](https://docs.microsoft.com/microsoft-365/compliance/customer-lockbox-requests)
   > * [Governo do Office 365](https://www.microsoft.com/microsoft-365/government)

1. Selecione **Optar por receber**. 

   Quando o modelo de locatário estiver pronto, você receberá uma mensagem de email de confirmação com mais instruções.

## <a name="deploy-your-tenant-model"></a>Implantar seu modelo de locatário

Quando a instância do modelo de locatário estiver pronta, implante-a fazendo o seguinte:

1. Na mensagem de email de confirmação, selecione o botão **Exibir modelo** . Ou entre no [Speech Studio](https://speech.microsoft.com/).
1. No canto superior direito, selecione **configurações** (ícone de engrenagem) e, em seguida, selecione **configurações de modelo de locatário**.

   ![O link "configurações do modelo de locatário"](media/tenant-language-model/tenant-language-settings.png)

1. Selecione **Implementar**.

   Quando o modelo tiver sido implantado, o status será alterado para *implantado*.

## <a name="use-your-tenant-model-with-the-speech-sdk"></a>Usar seu modelo de locatário com o SDK de fala

Agora que você implantou seu modelo, você pode usá-lo com o SDK de fala. Nesta seção, você usa o código de exemplo para chamar o serviço de fala usando a autenticação do Azure Active Directory (AD do Azure).

Vamos examinar o código que você usará para chamar o SDK de fala no C#. Neste exemplo, você executa o reconhecimento de fala usando seu modelo de locatário. Este guia presume que sua plataforma já esteja configurada. Se precisar de ajuda para instalação, consulte [início rápido: reconhecer C# fala, (.NET Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).

Copie este código para o seu projeto:

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // ServiceApplicationId is a fixed value. No need to change it.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

Em seguida, você precisa recompilar e executar o projeto na linha de comando. Antes de executar o comando, atualize alguns parâmetros fazendo o seguinte:

1. Substitua `<Username>` e `<Password>` pelos valores de um usuário de locatário válido.
1. Substitua `<Subscription-Key>` pela chave de assinatura do recurso de fala. Esse valor está disponível na seção **visão geral** do recurso de fala no [portal do Azure](https://aka.ms/azureportal).
1. Substitua `<Endpoint-Uri>` pelo ponto de extremidade a seguir. Certifique-se de substituir `{your region}` pela região em que o recurso de fala foi criado. Há suporte para essas regiões: `westus`, `westus2`e `eastus`. As informações de sua região estão disponíveis na seção **visão geral** do seu recurso de fala no [portal do Azure](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
1. Execute o seguinte comando:

   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

Neste tutorial, você aprendeu a usar os dados do Office 365 para criar um modelo de reconhecimento de fala personalizado, implantá-lo e usá-lo com o SDK de fala.

## <a name="next-steps"></a>Passos seguintes

* [Estúdio de fala](https://speech.microsoft.com/)
* [SDK de Voz](speech-sdk.md)
