---
title: Criar um modelo de inquilino (pré-visualização) - Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Gera automaticamente um modelo de inquilino seguro e compatível (Discurso Personalizado com dados do Office 365) que utiliza os dados do Office 365 para fornecer o melhor reconhecimento da fala para termos específicos da organização.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: 02ae140568150b0db8ebdea065ee683e4f4eec9c
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189006"
---
# <a name="tutorial-create-a-tenant-model-preview"></a>Tutorial: Criar um modelo de inquilino (pré-visualização)

O Modelo de Inquilino (Discurso Personalizado com dados do Office 365) é um serviço de opt-in para clientes empresariais do Office 365 que gera automaticamente um modelo de reconhecimento de voz personalizado a partir dos dados do Office 365 da sua organização. O modelo está otimizado para termos técnicos, jargão e nomes de pessoas, tudo de forma segura e conforme.

> [!IMPORTANT]
> Se a sua organização se inscrever utilizando o serviço 'Modelo de Inquilino', o Speech Service poderá aceder ao modelo de idiomas da sua organização. O modelo é gerado a partir de e-mails e documentos de grupo público do Office 365, que podem ser vistos por qualquer pessoa da sua organização. O administrador do Office 365 da sua organização pode ligar ou desligar o uso do modelo de linguagem em toda a organização do portal de administração do Office 365.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Inscreva-se no Modelo de Inquilino utilizando o centro de administração da Microsoft 365
> * Obtenha uma chave de subscrição do Discurso
> * Criar um modelo de inquilino
> * Implementar um modelo de inquilino
> * Use o seu modelo de inquilino com o SDK de Fala

## <a name="enroll-in-the-tenant-model-service"></a>Inscreva-se no serviço Modelo de Inquilino

Antes de poder implementar o seu modelo de inquilino, precisa de estar inscrito no serviço Modelo de Inquilino. As inscrições estão concluídas no centro de administração da Microsoft 365 e só podem ser feitas pelo seu administrador Microsoft 365.

1. Inscreva-se no centro de administração da [Microsoft 365](https://admin.microsoft.com).

1. No painel esquerdo, selecione **Definições**, e, em seguida, selecione **Definições** do menu aninhado e, em seguida, selecione Serviços de **Discurso Azure** a partir da janela principal.

   ![O painel "Serviços e add-ins"](media/tenant-language-model/tenant-language-model-enrollment.png)

1. Selecione a caixa de verificação **do modelo de idioma em toda a organização** e, em seguida, selecione Guardar **alterações**.

   ![O painel dos Serviços de Fala Azure](media/tenant-language-model/tenant-language-model-enrollment-2.png)

Para desligar a instância modelo do inquilino:
1. Repita os passos anteriores 1 e 2.
1. Limpe a caixa de verificação **do modelo de idioma em toda a organização** e, em seguida, selecione Guardar **alterações**.

## <a name="get-a-speech-subscription-key"></a>Obtenha uma chave de subscrição do Discurso

Para utilizar o seu modelo de inquilino com o Speech SDK, precisa de um recurso da Fala e da sua chave de subscrição associada.

1. Inicie sessão no [Portal do Azure](https://aka.ms/azureportal).
1. Selecione **Criar um recurso**.
1. Na caixa **de pesquisa,** escreva **Discurso**.
1. Na lista de resultados, selecione **'Falar'** e, em seguida, selecione **Criar**.
1. Siga as instruções no ecrã para criar o seu recurso. Certifique-se de que:
   * **A localização** está definida para **leste** ou **oeste.**
   * **O nível de preços** está definido para **S0**.
1. Selecione **Criar**.

   Após alguns minutos, o seu recurso é criado. A chave de subscrição está disponível na secção **Visão Geral** para o seu recurso.

## <a name="create-a-language-model"></a>Criar um modelo linguístico

Depois de o seu administrador ter ativado o Modelo de Inquilino para a sua organização, pode criar um modelo de linguagem baseado nos dados do seu Office 365.

1. Inscreva-se no [Speech Studio.](https://speech.microsoft.com/)
1. Na parte superior direita, selecione **Definições** (ícone de engrenagem) e, em seguida, selecione **as definições**do Modelo de Inquilino .

   ![O link "Modelo de Inquilino"](media/tenant-language-model/tenant-language-settings.png)

   O Speech Studio exibe uma mensagem que lhe permite saber se está qualificado para criar um modelo de inquilino.

   > [!NOTE]
   > O Office 365 clientes empresariais na América do Norte são elegíveis para criar um modelo de inquilino (inglês). Se é um Customer Lockbox, Chave de Cliente ou cliente do Office 365 Government, esta funcionalidade não está disponível. Para determinar se é um lockbox de cliente ou cliente chave do cliente, consulte:
   > * [Caixa de bloqueio do cliente](/microsoft-365/compliance/customer-lockbox-requests)
   > * [Chave do cliente](/microsoft-365/compliance/customer-key-overview)
   > * [Gabinete 365 Governo](/microsoft-365/government)

1. Selecione **Optar por receber**.

   Quando o seu modelo de inquilino estiver pronto, receberá uma mensagem de e-mail de confirmação com mais instruções.

## <a name="deploy-your-tenant-model"></a>Implante o seu modelo de inquilino

Quando a instância modelo do seu inquilino estiver pronta, desdobre-a fazendo o seguinte:

1. Na sua mensagem de e-mail de confirmação, selecione o botão **'Ver modelo'.** Ou inscreva-se no [Speech Studio.](https://speech.microsoft.com/)
1. Na parte superior direita, selecione **Definições** (ícone de engrenagem) e, em seguida, selecione **as definições**do Modelo de Inquilino .

   ![O link "Modelo de Inquilino"](media/tenant-language-model/tenant-language-settings.png)

1. Selecione **Implementar**.

   Quando o seu modelo foi implementado, o estado muda para *O Desdobrado*.

## <a name="use-your-tenant-model-with-the-speech-sdk"></a>Use o seu modelo de inquilino com o SDK de Fala

Agora que implementou o seu modelo, pode usá-lo com o SDK da Fala. Nesta secção, utiliza o código da amostra para chamar o Serviço de Fala utilizando a autenticação azure Ative Directory (Azure AD).

Vamos ver o código que vai usar para chamar o C#SDK do discurso em . Neste exemplo, realiza o reconhecimento da fala utilizando o seu modelo de inquilino. Este guia presume que a sua plataforma já está montada. Se precisar de ajuda para a configuração, consulte [Quickstart: C# Recognise speech, (.NET Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).

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

1. Substitua `<Username>` e `<Password>` os valores para um utilizador inquilino válido.
1. Substitua `<Subscription-Key>` com a chave de subscrição do seu recurso Speech. Este valor está disponível na secção **Visão Geral** para o seu recurso Speech no [portal Azure](https://aka.ms/azureportal).
1. Substitua `<Endpoint-Uri>` pelo seguinte ponto final. Certifique-se de que substitui `{your region}` pela região onde foi criado o seu recurso Speech. Estas regiões são apoiadas: `westus`, `westus2`e `eastus`. A informação da sua região está disponível na secção **de visão geral** do seu recurso Speech no portal [Azure](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
1. Execute o seguinte comando:

   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

Neste tutorial, aprendeu a usar os dados do Office 365 para criar um modelo personalizado de reconhecimento de voz, implantá-lo e usá-lo com o SDK da fala.

## <a name="next-steps"></a>Passos seguintes

* [Estúdio de Discurso](https://speech.microsoft.com/)
* [SDK de Voz](speech-sdk.md)
