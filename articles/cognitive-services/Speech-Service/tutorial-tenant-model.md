---
title: Criar um modelo de locatário (versão prévia) – serviço de fala
titleSuffix: Azure Cognitive Services
description: Gere automaticamente um modelo de locatário (Fala Personalizada com dados do Office 365) que aproveita seus dados do Office 365 para fornecer o reconhecimento de fala ideal para termos específicos da organização que são seguros e compatíveis.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: 8ca31dcadebf2dc47d5a4b4db715f26fb38e204e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816377"
---
# <a name="create-a-tenant-model-preview"></a>Criar um modelo de locatário (visualização)

O modelo de locatário (Fala Personalizada com dados do Office 365) é um serviço de aceitação para clientes do Office 365 Enterprise que gera automaticamente um modelo de reconhecimento de fala personalizado dos dados do Office365 da sua organização. O modelo criado é otimizado para termos técnicos, nomes de jargão e de pessoas, tudo de maneira segura e em conformidade.

> [!IMPORTANT]
> Se sua organização registrar-se com o modelo de locatário, o serviço de fala poderá acessar o modelo de linguagem da sua organização, que é gerado a partir de emails e documentos do grupo público do Office 365 que podem ser vistos por qualquer pessoa em sua organização. O administrador do Office 365 da sua organização pode ativar/desativar o uso do modelo de linguagem em toda a organização usando o portal de administração do Office 365.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Registre-se para usar um modelo de locatário no centro de administração do Microsoft 365
> * Obter uma chave de assinatura de fala
> * Criar um modelo de locatário
> * Implantar um modelo de locatário
> * Usar um modelo de locatário com o SDK de fala

## <a name="enroll-using-the-microsoft-365-admin-center"></a>Registrar-se usando o centro de administração Microsoft 365

Antes de implantar seu modelo de locatário, primeiro é necessário registrá-lo usando o centro de administração Microsoft 365. Essa tarefa só pode ser concluída pelo administrador do Microsoft 365.

1. Entre no [centro de administração do Microsoft 365](https://admin.microsoft.com ).
2. No painel esquerdo, selecione **configurações** e, em seguida, **aplicativos**.

   ![Registro de modelo de locatário](media/tenant-language-model/tenant-language-model-enrollment.png)

3. Localize e selecione **serviços de fala do Azure**.

   ![Registro de modelo de locatário 2](media/tenant-language-model/tenant-language-model-enrollment-2.png)

4. Clique na caixa de seleção e salve.

Se você precisar desativar o modelo de locatário, navegue de volta para esta tela, desmarque a caixa de seleção e salve.

## <a name="get-a-speech-subscription-key"></a>Obter uma chave de assinatura de fala

Para usar um modelo de locatário com o SDK de fala, você precisará de um recurso de fala e sua chave de assinatura associada.

1. Inicie sessão no [Portal do Azure](https://aka.ms/azureportal).
2. Selecione **criar um recurso**.
3. Na barra de pesquisa, digite: **fala**.
4. Selecione **fala**e clique em **criar**.
5. Siga as instruções na tela para criar o recurso. Certificar-se:
   * O **local** é definido como **lesteus** ou **westus**.
   * O **tipo de preço** é definido como **S0**.
6. Clique em **Criar**.
7. Depois de alguns minutos, o recurso é criado. A chave de assinatura está disponível na seção **visão geral** do recurso.

## <a name="create-a-model"></a>Criar um modelo

Depois que o administrador tiver habilitado o modelo de locatário para sua organização, você poderá criar um modelo de linguagem com base em seus dados do office365.

1. Entre no [Speech Studio](https://speech.microsoft.com/).
2. No canto superior direito, localize e clique no ícone de engrenagem (configurações) e selecione **configurações de modelo de locatário**.

   ![Menu Definições](media/tenant-language-model/tenant-language-settings.png)

3. Neste ponto, você verá uma mensagem informando que você sabe se está qualificado para criar um modelo de locatário.
   > [!NOTE]
   > Os clientes do Office 365 Enterprise no América do Norte estão qualificados para criar um modelo de locatário (inglês). Se você for um Sistema de Proteção de Dados do Cliente (CLB), chave do cliente (CK) ou cliente do governo do Office 365, esse recurso não estará disponível. Para determinar se você é um cliente de Sistema de Proteção de Dados do Cliente ou chave do cliente, siga estas instruções:
   > * [Sistema de Proteção de Dados do Cliente](https://docs.microsoft.com/office365/securitycompliance/controlling-your-data-using-customer-key#FastTrack)
   > * [Chave do cliente](https://docs.microsoft.com/microsoft-365/compliance/customer-lockbox-requests)
   > * [Governo do Office 365](https://www.microsoft.com/microsoft-365/government)

4. Em seguida, selecione **aceitar**. Você receberá um email com instruções quando seu modelo de locatário estiver pronto.

## <a name="deploy-your-model"></a>Implantar seu modelo

Quando o modelo de locatário estiver pronto, siga estas etapas para implantar seu modelo:

1. Clique no botão **Exibir modelo** no email de confirmação que você recebeu ou entre no [Speech Studio](https://speech.microsoft.com/).
2. No canto superior direito, localize e clique no ícone de engrenagem (configurações) e selecione **configurações de modelo de locatário**.

   ![Menu Definições](media/tenant-language-model/tenant-language-settings.png)

3. Clique em **implementar**.
4. Quando o modelo for implantado, o status será alterado para **implantado**.

## <a name="use-your-model-with-the-speech-sdk"></a>Usar seu modelo com o SDK de fala

Agora que você implantou seu modelo, você pode usá-lo com o SDK de fala. Nesta seção, você usará o código de exemplo fornecido para chamar o serviço de fala usando a autenticação do Azure AD.

Vamos examinar o código que você usará para chamar o SDK de fala no C#. Neste exemplo, você executará o reconhecimento de fala usando um modelo de locatário. Este guia presume que sua plataforma já esteja configurada. Se precisar de ajuda com a configuração do, consulte [início rápido: reconhecer C# fala, (.NET Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).

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

    // Note: ServiceApplicationId is a fixed value.  No need to change.

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

Em seguida, você precisará recompilar e executar o projeto na linha de comando. Há alguns parâmetros que você precisará atualizar antes de executar o comando.

1. Substitua `<Username>` e `<Password>` pelos valores de um usuário de locatário válido.
2. Substitua `<Subscription-Key>` pela chave de assinatura do recurso de fala. Esse valor está disponível na seção **visão geral** do recurso de fala no [portal do Azure](https://aka.ms/azureportal).
3. Substitua `<Endpoint-Uri>` pelo ponto de extremidade abaixo. Certifique-se de substituir `{your-region}` pela região em que o recurso de fala foi criado. Há suporte para essas regiões: `westus`, `westus2`e `eastus`. As informações de sua região estão disponíveis na seção **visão geral** do recurso de fala no [portal do Azure](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
4. Execute o comando:
   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

## <a name="next-steps"></a>Passos seguintes

* [Estúdio de fala](https://speech.microsoft.com/)
* [SDK de Voz](speech-sdk.md)
