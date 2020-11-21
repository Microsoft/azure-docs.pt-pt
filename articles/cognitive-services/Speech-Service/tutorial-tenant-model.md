---
title: Criar um modelo de inquilino (pré-visualização) - Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Geram automaticamente um modelo de inquilino seguro e compatível (Custom Speech with Microsoft 365 data) que utiliza os dados do Microsoft 365 para fornecer o melhor reconhecimento de voz para termos específicos da organização.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 06/25/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 48cde51ee9941f705aa848d121c419a8f0c9ad1a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013705"
---
# <a name="tutorial-create-a-tenant-model-preview"></a>Tutorial: Criar um modelo de inquilino (pré-visualização)

O Modelo de Inquilino (Custom Speech with Microsoft 365 data) é um serviço de opt-in para clientes empresariais microsoft 365 que gera automaticamente um modelo de reconhecimento de voz personalizado a partir dos dados microsoft 365 da sua organização. O modelo é otimizado para termos técnicos, jargão e nomes de pessoas, tudo de forma segura e conforme.

> [!IMPORTANT]
> Se a sua organização se inscrever utilizando o serviço Modelo de Inquilino, o Serviço de Fala poderá aceder ao modelo linguístico da sua organização. O modelo é gerado a partir de e-mails e documentos de grupo público microsoft 365, que podem ser vistos por qualquer pessoa da sua organização. O administrador da sua organização pode ligar ou desligar o uso do modelo linguístico em toda a organização a partir do portal de administração.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Inscreva-se no Modelo de Inquilino utilizando o centro de administração Microsoft 365
> * Obtenha uma chave de subscrição de discurso
> * Criar um modelo de inquilino
> * Implementar um modelo de inquilino
> * Use o seu modelo de inquilino com o Speech SDK

## <a name="enroll-in-the-tenant-model-service"></a>Inscreva-se no serviço Modelo de Inquilino

Antes de poder implementar o seu modelo de inquilino, tem de estar inscrito no serviço Modelo de Inquilino. A inscrição é concluída no centro de administração microsoft 365 e só pode ser feita pelo seu administrador.

1. Inicie sessão no [centro de administração do Microsoft 365](https://admin.microsoft.com).

1. No painel esquerdo, selecione **Definições** e, em seguida, selecione **Definições** do menu aninhado e, em seguida, selecione **Azure Speech Services** a partir da janela principal.

   ![O painel "Serviços & add-ins"](media/tenant-language-model/tenant-language-model-enrollment.png)

1. Selecione a caixa **de verificação do modelo de linguagem em toda a organização** e, em seguida, selecione Guardar **alterações**.

   ![O painel de serviços de discurso do Azure](media/tenant-language-model/tenant-language-model-enrollment-2.png)

Para desligar a instância modelo do inquilino:
1. Repita os passos anteriores 1 e 2.
1. Limpe a caixa **de verificação do modelo de linguagem em toda a organização** e, em seguida, selecione Guardar **alterações**.

## <a name="get-a-speech-subscription-key"></a>Obtenha uma chave de subscrição de discurso

Para utilizar o seu modelo de inquilino com o Speech SDK, precisa de um recurso Speech e da sua chave de subscrição associada.

1. Inicie sessão no [portal do Azure](https://aka.ms/azureportal).
1. Selecione **Criar um recurso**.
1. Na caixa **de Pesquisa,** escreva **Discurso**.
1. Na lista de resultados, selecione **Speech** e, em seguida, selecione **Criar**.
1. Siga as instruções no ecrã para criar o seu recurso. Certifique-se de que:
   * **A localização** está definida para **este** ou **oeste.**
   * **O nível de preços** está definido para **S0**.
1. Selecione **Criar**.

   Após alguns minutos, o seu recurso é criado. A chave de subscrição está disponível na secção **Visão Geral** para o seu recurso.

## <a name="create-a-language-model"></a>Criar um modelo de linguagem

Depois de o seu administrador ter ativado o Modelo de Inquilino para a sua organização, pode criar um modelo linguístico baseado nos seus dados Microsoft 365.

1. Inscreva-se no [Speech Studio.](https://speech.microsoft.com/)
1. No topo direito, selecione **Definições** (ícone de engrenagem) e, em seguida, selecione **as definições do Modelo do Inquilino**.

   ![O link "Configurações do Modelo do Inquilino"](media/tenant-language-model/tenant-language-settings.png)

   O Speech Studio exibe uma mensagem que lhe permite saber se está qualificado para criar um modelo de inquilino.

   > [!NOTE]
   > Os clientes empresariais na América do Norte são elegíveis para criar um modelo de inquilino (inglês). Se for um cliente do Customer Lockbox, da Chave de Clientes ou do Office 365, esta funcionalidade não está disponível. Para determinar se é um cliente de bloqueio ao cliente ou chave de cliente, consulte:
   > * [Sistema de Proteção de Dados do Cliente](/microsoft-365/compliance/customer-lockbox-requests)
   > * [Chave do Cliente](/microsoft-365/compliance/customer-key-overview)
   > * [Gabinete 365 Governo](https://www.microsoft.com/microsoft-365/government)

1. Selecione **Optar por receber**.

   Quando o modelo do seu inquilino estiver pronto, receberá uma mensagem de correio eletrónico de confirmação com mais instruções.

## <a name="deploy-your-tenant-model"></a>Implemente o seu modelo de inquilino

Quando a sua instância modelo de inquilino estiver pronta, desloque-a fazendo o seguinte:

1. Na sua mensagem de correio eletrónico de confirmação, selecione o botão **'Ver modelo'.** Ou inscreva-se no [Speech Studio.](https://speech.microsoft.com/)
1. No topo direito, selecione **Definições** (ícone de engrenagem) e, em seguida, selecione **as definições do Modelo do Inquilino**.

   ![O link "Configurações do Modelo do Inquilino"](media/tenant-language-model/tenant-language-settings.png)

1. Selecione **Implementar**.

   Quando o seu modelo foi implantado, o estado muda para *Implementar*.

## <a name="use-your-tenant-model-with-the-speech-sdk"></a>Use o seu modelo de inquilino com o Speech SDK

Agora que implementou o seu modelo, pode usá-lo com o SDK do discurso. Nesta secção, utiliza o código de amostra para chamar o Serviço de Fala utilizando a autenticação do Azure Ative Directory (Azure AD).

Vamos ver o código que vais usar para chamar o Discurso SDK em C#. Neste exemplo, realiza-se o reconhecimento da fala utilizando o seu modelo de inquilino. Este guia pressupõe que a sua plataforma já está configurada. Se precisar de ajuda de configuração, consulte [Quickstart: Reconheça a fala, C# (.NET Core)](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore).

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

Em seguida, você precisa reconstruir e executar o projeto a partir da linha de comando. Antes de executar o comando, atualize alguns parâmetros fazendo o seguinte:

1. Substitua `<Username>` e `<Password>` pelos valores de um utilizador inquilino válido.
1. `<Subscription-Key>`Substitua-a pela tecla de subscrição do seu recurso Speech. Este valor está disponível na secção **Visão Geral** para o seu recurso Speech no [portal Azure](https://aka.ms/azureportal).
1. `<Endpoint-Uri>`Substitua-o pelo seguinte ponto final. Certifique-se de que substitui `{your region}` pela região onde o seu recurso Discurso foi criado. Estas regiões são apoiadas: `westus` `westus2` e `eastus` . A informação da sua região está disponível na secção **Visão Geral** do seu recurso Speech no [portal Azure](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
1. Execute o seguinte comando:

   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

Neste tutorial, aprendeu a usar os dados do Microsoft 365 para criar um modelo personalizado de reconhecimento de voz, implantá-lo e usá-lo com o Speech SDK.

## <a name="next-steps"></a>Próximos passos

* [Speech Studio](https://speech.microsoft.com/)
* [API de Voz](speech-sdk.md)