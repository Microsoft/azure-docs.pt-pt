---
title: Usar identidade gerida com uma aplicação
description: Como utilizar identidades geridas no código de aplicação Azure Service Fabric para aceder aos Serviços Azure.
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: 8f1f355d6add16f3b3ec25bc569f9b198a8d6778
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81461570"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>Como alavancar a identidade gerida de uma aplicação de Tecido de Serviço para aceder aos serviços Azure

As aplicações service Fabric podem aproveitar identidades geridas para aceder a outros recursos Azure que suportam a autenticação baseada no Diretório Ativo azure. Uma aplicação pode obter um [token](../active-directory/develop/developer-glossary.md#access-token) de acesso que represente a sua identidade, que pode ser atribuída ao sistema ou atribuída ao utilizador, e usá-la como um símbolo 'portador' para se autenticar a outro serviço - também conhecido como servidor de [recursos protegidos.](../active-directory/develop/developer-glossary.md#resource-server) O símbolo representa a identidade atribuída à aplicação Service Fabric, e só será emitida aos recursos da Azure (incluindo aplicações SF) que partilhem essa identidade. Consulte a documentação de visão geral de [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) para uma descrição detalhada das identidades geridas, bem como a distinção entre identidades atribuídas ao sistema e identidades atribuídas ao utilizador. Referimo-nos a uma aplicação de Tecido de Serviço com identidade gerida como [aplicação](../active-directory/develop/developer-glossary.md#client-application) do cliente ao longo deste artigo.

> [!IMPORTANT]
> Uma identidade gerida representa a associação entre um recurso Azure e um diretor de serviço no correspondente inquilino da AD Azure associado à subscrição que contém o recurso. Como tal, no contexto do Service Fabric, as identidades geridas só são suportadas para aplicações implementadas como recursos Azure. 

> [!IMPORTANT]
> Antes de utilizar a identidade gerida de uma aplicação Service Fabric, a aplicação do cliente deve ter acesso ao recurso protegido. Consulte a lista de [serviços azure que suportam a autenticação da Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) para verificar o apoio e, em seguida, a documentação do respetivo serviço para medidas específicas para conceder acesso de identidade aos recursos de interesse. 

## <a name="acquiring-an-access-token-using-rest-api"></a>Adquirir um token de acesso utilizando a API REST
Em clusters habilitados para a identidade gerida, o tempo de execução do Tecido de Serviço expõe um ponto final do hospedeiro local que as aplicações podem usar para obter fichas de acesso. O ponto final está disponível em cada nó do cluster, e é acessível a todas as entidades nesse nó. Os chamadores autorizados podem obter fichas de acesso, ligando para este ponto final e apresentando um código de autenticação; o código é gerado pelo tempo de execução do Tecido de Serviço para cada ativação de pacote de código de serviço distinto, e está ligado ao tempo de vida do processo que acolhe o pacote de código de serviço.

Especificamente, o ambiente de um serviço de tecido de serviço gerido com identidade será semeado com as seguintes variáveis:
- 'IDENTITY_ENDPOINT': o ponto final do local correspondente à identidade gerida do serviço
- 'IDENTITY_HEADER': um código de autenticação único que representa o serviço no nó atual
- 'IDENTITY_SERVER_THUMBPRINT' : Impressão digital do servidor de identidade gerido por tecido de serviço

> [!IMPORTANT]
> O código de aplicação deve considerar o valor da variável ambiente "IDENTITY_HEADER" como dados sensíveis - não deve ser registado ou divulgado de outra forma. O código de autenticação não tem qualquer valor fora do nó local, ou após o processo de hospedagem do serviço ter terminado, mas representa a identidade do serviço de Tecido de Serviço, pelo que deve ser tratado com as mesmas precauções que o próprio token de acesso.

Para obter um símbolo, o cliente executa os seguintes passos:
- forma um URI concatenando o ponto final de identidade gerido (valor IDENTITY_ENDPOINT) com a versão API e o recurso (público) necessário para o símbolo
- cria um pedido GET http(s) para o URI especificado
- adiciona lógica de validação de certificado de servidor apropriado
- adiciona o código de autenticação (IDENTITY_HEADER valor) como cabeçalho ao pedido
- submete o pedido

Uma resposta bem sucedida conterá uma carga útil JSON que representa o token de acesso resultante, bem como metadados descrevendo-o. Uma resposta falhada incluirá também uma explicação do fracasso. Veja abaixo mais detalhes sobre o manuseamento de erros.

As fichas de acesso serão cached by Service Fabric a vários níveis (nó, cluster, serviço de prestador de recursos), pelo que uma resposta bem sucedida não implica necessariamente que o símbolo tenha sido emitido diretamente em resposta ao pedido da aplicação do utilizador. Os tokens serão cached por menos do que a sua vida, e assim uma aplicação é garantida para receber um token válido. Recomenda-se que o código de aplicação caches quaisquer fichas de acesso que adquire; a chave de cache deve incluir (uma derivação de) o público. 

Pedido de amostra:
```http
GET 'https://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://vault.azure.net/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
em que:

| Elemento | Descrição |
| ------- | ----------- |
| `GET` | O verbo HTTP, indicando que pretende recuperar dados do ponto final. Neste caso, um sinal de acesso da OAuth. | 
| `https://localhost:2377/metadata/identity/oauth2/token` | O ponto final de identidade gerido para aplicações de Tecido de Serviço, fornecido através da variável ambiente IDENTITY_ENDPOINT. |
| `api-version` | Um parâmetro de corda de consulta, especificando a versão API do Serviço de Fichas de Identidade Gerida; atualmente o único `2019-07-01-preview`valor aceite é , e está sujeito a alterações. |
| `resource` | Um parâmetro de corda de consulta, indicando o ID da aplicação URI do recurso alvo. Isto será refletido `aud` como a (audiência) reivindicação do símbolo emitido. Este exemplo solicita um sinal de acesso ao Azure Key Vault, cujo ID de aplicação URI é https:\//vault.azure.net/. |
| `Secret` | Um campo de cabeçalho de pedido HTTP, exigido pelo Serviço de Ficha de Identidade Gerida por Tecidos de Serviço para os serviços de tecido de serviço para autenticar o chamador. Este valor é fornecido pelo sf tempo de execução através de IDENTITY_HEADER variável ambiental. |


Resposta da amostra:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://vault.azure.net/"
}
```
em que:

| Elemento | Descrição |
| ------- | ----------- |
| `token_type` | O tipo de ficha; neste caso, um símbolo de acesso "Portador", o que significa que o apresentador ('portador') deste símbolo é o tema pretendido do símbolo. |
| `access_token` | O sinal de acesso solicitado. Ao chamar uma API DE REPOUSO segura, `Authorization` o símbolo está incorporado no campo de cabeçalho de pedido como um símbolo "portador", permitindo que a API autenticar o chamador. | 
| `expires_on` | O carimbo de tempo da expiração do sinal de acesso; representado como o número de segundos de "1970-01-01T0:0:0Z UTC" `exp` e corresponde à reivindicação do token. Neste caso, o token expira em 2019-08-08T06:10:11+00:00 (em RFC 3339)|
| `resource` | O recurso para o qual o sinal de `resource` acesso foi emitido, especificado através do parâmetro de corda de consulta do pedido; corresponde à alegação "aud" do token. |


## <a name="acquiring-an-access-token-using-c"></a>Adquirir um token de acesso usando C #
O acima torna-se, em C#:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;
    using Newtonsoft.Json;

    /// <summary>
    /// Type representing the response of the SF Managed Identity endpoint for token acquisition requests.
    /// </summary>
    [JsonObject]
    public sealed class ManagedIdentityTokenResponse
    {
        [JsonProperty(Required = Required.Always, PropertyName = "token_type")]
        public string TokenType { get; set; }

        [JsonProperty(Required = Required.Always, PropertyName = "access_token")]
        public string AccessToken { get; set; }

        [JsonProperty(PropertyName = "expires_on")]
        public string ExpiresOn { get; set; }

        [JsonProperty(PropertyName = "resource")]
        public string Resource { get; set; }
    }

    /// <summary>
    /// Sample class demonstrating access token acquisition using Managed Identity.
    /// </summary>
    public sealed class AccessTokenAcquirer
    {
        /// <summary>
        /// Acquire an access token.
        /// </summary>
        /// <returns>Access token</returns>
        public static async Task<string> AcquireAccessTokenAsync()
        {
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("IDENTITY_HEADER");
            var managedIdentityServerThumbprint = Environment.GetEnvironmentVariable("IDENTITY_SERVER_THUMBPRINT");
            // Latest api version, 2019-07-01-preview is still supported.
            var managedIdentityApiVersion = Environment.GetEnvironmentVariable("IDENTITY_API_VERSION");
            var managedIdentityAuthenticationHeader = "secret";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);
            
            var handler = new HttpClientHandler();
            handler.ServerCertificateCustomValidationCallback = (httpRequestMessage, cert, certChain, policyErrors) =>
            {
                // Do any additional validation here
                if (policyErrors == SslPolicyErrors.None)
                {
                    return true;
                }
                return 0 == string.Compare(cert.GetCertHashString(), managedIdentityServerThumbprint, StringComparison.OrdinalIgnoreCase);
            };

            try
            {
                var response = await new HttpClient(handler).SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var tokenResponseString = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                var tokenResponseObject = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);

                return tokenResponseObject.AccessToken;
            }
            catch (Exception ex)
            {
                string errorText = String.Format("{0} \n\n{1}", ex.Message, ex.InnerException != null ? ex.InnerException.Message : "Acquire token failed");

                Console.WriteLine(errorText);
            }

            return String.Empty;
        }
    } // class AccessTokenAcquirer
} // namespace Azure.ServiceFabric.ManagedIdentity.Samples
```
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Aceder ao cofre chave a partir de uma aplicação de tecido de serviço usando identidade gerida
Esta amostra baseia-se no acima para demonstrar o acesso a um segredo armazenado num Cofre chave usando identidade gerida.

```C#
        /// <summary>
        /// Probe the specified secret, displaying metadata on success.  
        /// </summary>
        /// <param name="vault">vault name</param>
        /// <param name="secret">secret name</param>
        /// <param name="version">secret version id</param>
        /// <returns></returns>
        public async Task<string> ProbeSecretAsync(string vault, string secret, string version)
        {
            // initialize a KeyVault client with a managed identity-based authentication callback
            var kvClient = new Microsoft.Azure.KeyVault.KeyVaultClient(new Microsoft.Azure.KeyVault.KeyVaultClient.AuthenticationCallback((a, r, s) => { return AuthenticationCallbackAsync(a, r, s); }));

            Log(LogLevel.Info, $"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Log(LogLevel.Info, "\n== {DateTime.UtcNow.ToString()}: Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    response = String.Format($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    response = String.Format($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
                }
            }
            catch (Microsoft.Rest.ValidationException ve)
            {
                response = String.Format($"encountered REST validation exception 0x{ve.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}' from {ve.Source}: {ve.Message}");
            }
            catch (KeyVaultErrorException kvee)
            {
                response = String.Format($"encountered KeyVault exception 0x{kvee.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {kvee.Response.ReasonPhrase} ({kvee.Response.StatusCode})");
            }
            catch (Exception ex)
            {
                // handle generic errors here
                response = String.Format($"encountered exception 0x{ex.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {ex.Message}");
            }

            Log(LogLevel.Info, response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority">The expected issuer of the access token, from the KV authorization challenge.</param>
        /// <param name="resource">The expected audience of the access token, from the KV authorization challenge.</param>
        /// <param name="scope">The expected scope of the access token; not currently used.</param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            Log(LogLevel.Verbose, $"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");
            var encodedResource = HttpUtility.UrlEncode(resource);

            // This sample does not illustrate the caching of the access token, which the user application is expected to do.
            // For a given service, the caching key should be the (encoded) resource uri. The token should be cached for a period
            // of time at most equal to its remaining validity. The 'expires_on' field of the token response object represents
            // the number of seconds from Unix time when the token will expire. You may cache the token if it will be valid for at
            // least another short interval (1-10s). If its expiration will occur shortly, don't cache but still return it to the 
            // caller. The MI endpoint will not return an expired token.
            // Sample caching code:
            //
            // ManagedIdentityTokenResponse tokenResponse;
            // if (responseCache.TryGetCachedItem(encodedResource, out tokenResponse))
            // {
            //     Log(LogLevel.Verbose, $"cache hit for key '{encodedResource}'");
            //
            //     return tokenResponse.AccessToken;
            // }
            //
            // Log(LogLevel.Verbose, $"cache miss for key '{encodedResource}'");
            //
            // where the response cache is left as an exercise for the reader. MemoryCache is a good option, albeit not yet available on .net core.

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={encodedResource}";
            Log(LogLevel.Verbose, $"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            Log(LogLevel.Verbose, $"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            Log(LogLevel.Verbose, $"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var tokenResponseString = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            var tokenResponse = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);
            Log(LogLevel.Verbose, "deserialized token response; returning access code..");

            // Sample caching code (continuation):
            // var expiration = DateTimeOffset.FromUnixTimeSeconds(Int32.Parse(tokenResponse.ExpiresOn));
            // if (expiration > DateTimeOffset.UtcNow.AddSeconds(5.0))
            //    responseCache.AddOrUpdate(encodedResource, tokenResponse, expiration);

            return tokenResponse.AccessToken;
        }

        private string PrintSecretBundleMetadata(SecretBundle bundle)
        {
            StringBuilder strBuilder = new StringBuilder();

            strBuilder.AppendFormat($"\n\tid: {bundle.Id}\n");
            strBuilder.AppendFormat($"\tcontent type: {bundle.ContentType}\n");
            strBuilder.AppendFormat($"\tmanaged: {bundle.Managed}\n");
            strBuilder.AppendFormat($"\tattributes:\n");
            strBuilder.AppendFormat($"\t\tenabled: {bundle.Attributes.Enabled}\n");
            strBuilder.AppendFormat($"\t\tnbf: {bundle.Attributes.NotBefore}\n");
            strBuilder.AppendFormat($"\t\texp: {bundle.Attributes.Expires}\n");
            strBuilder.AppendFormat($"\t\tcreated: {bundle.Attributes.Created}\n");
            strBuilder.AppendFormat($"\t\tupdated: {bundle.Attributes.Updated}\n");
            strBuilder.AppendFormat($"\t\trecoveryLevel: {bundle.Attributes.RecoveryLevel}\n");

            return strBuilder.ToString();
        }

        private enum LogLevel
        {
            Info,
            Verbose
        };

        private void Log(LogLevel level, string message)
        {
            if (level != LogLevel.Verbose
                || config.DoVerboseLogging)
            {
                Console.WriteLine(message);
            }
        }

```

## <a name="error-handling"></a>Processamento de erros
O campo "código de estado" do cabeçalho de resposta HTTP indica o estado de sucesso do pedido; um estado de '200 OK' indica sucesso, e a resposta incluirá o sinal de acesso descrito acima. Seguem-se uma breve enumeração de possíveis respostas de erro.

| Código de Estado | Razão do erro | Como lidar |
| ----------- | ------------ | ------------- |
| 404 não encontrados. | Código de autenticação desconhecido, ou o pedido não foi atribuído a uma identidade gerida. | Retificar a configuração da aplicação ou o código de aquisição simbólico. |
| 429 pedidos a mais. |  Limite de aceleração atingido, imposto pela AAD ou SF. | Retry com Backoff Exponencial. Consulte a orientação abaixo. |
| Erro 4xx a pedido. | Um ou mais dos parâmetros de pedido estavam incorretos. | Não tente de novo.  Examine os detalhes do erro para mais informações.  Erros 4xx são erros de tempo de design.|
| Erro 5xx do serviço. | O subsistema de identidade gerido ou o Diretório Ativo Azure devolveu um erro transitório. | É seguro voltar a tentar depois de um curto período de tempo. Pode atingir uma condição de estrangulamento (429) ao tentar novamente.|

Se ocorrer um erro, o corpo de resposta http correspondente contém um objeto JSON com os detalhes do erro:

| Elemento | Descrição |
| ------- | ----------- |
| code | Código de erro. |
| correlationId | Uma identificação de correlação que pode ser usada para depurar. |
| message | Verbose descrição de erro. **As descrições de erros podem mudar a qualquer momento. Não dependa da mensagem de erro em si.**|

Erro da amostra:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

Segue-se uma lista de erros típicos do Tecido de Serviço específicos das identidades geridas:

| Código | Mensagem | Descrição | 
| ----------- | ----- | ----------------- |
| Cabeçalho SecretoNão Encontrado | O segredo não se encontra nos cabeçalhos do pedido. | O código de autenticação não foi fornecido com o pedido. | 
| Identidade GeridaNãoEncontrado | Identidade gerida não encontrada para o anfitrião de aplicação especificado. | O pedido não tem identidade, ou o código de autenticação é desconhecido. |
| ArgumentnullorEmpty | O parâmetro "recurso" não deve ser nulo ou de corda vazia. | O recurso (público) não foi fornecido no pedido. |
| InvalidApiVersion | A versão api '' não é suportada. A versão suportada é '2019-07-01-preview'. | Versão API em falta ou não suportada especificada no pedido URI. |
| InternalServerError | Ocorreu um erro. | Foi encontrado um erro no subsistema de identidade gerido, possivelmente fora da pilha de Tecido de Serviço. A causa mais provável é um valor incorreto especificado para o recurso (verifique se está a seguir '/'?) | 

## <a name="retry-guidance"></a>Orientação de retry 

Tipicamente, o único código de erro retível é 429 (Pedidos demasiados); erros internos do servidor/códigos de erro 5xx podem ser retível, embora a causa possa ser permanente. 

Os limites de estrangulamento aplicam-se ao número de chamadas efetuadas ao subsistema de identidade gerido - especificamente as dependências "a montante" (o serviço Managed Identity Azure, ou o serviço de fichas seguras). Serviço Tecido caches tokens em vários níveis do oleoduto, mas dada a natureza distribuída dos componentes envolvidos, o chamador pode experimentar respostas de estrangulamento inconsistentes (isto é, ser estrangulado em um nó/instância de uma aplicação, mas não em um nó diferente enquanto solicita um símbolo para a mesma identidade.) Quando a condição de estrangulamento estiver definida, os pedidos subsequentes da mesma aplicação podem falhar com o código de estado HTTP 429 (Demasiados Pedidos) até que a condição seja desmarcada.  

Recomenda-se que os pedidos falhados devido à estrangulamento sejam novamente experimentados com um recuo exponencial, da seguinte forma: 

| Índice de chamada | Ação para receber 429 | 
| --- | --- | 
| 1 | Espere 1 segundo e tente novamente |
| 2 | Aguarde 2 segundos e tente novamente |
| 3 | Aguarde 4 segundos e tente novamente |
| 4 | Espere 8 segundos e tente novamente |
| 4 | Espere 8 segundos e tente novamente |
| 5 | Aguarde 16 segundos e tente novamente |

## <a name="resource-ids-for-azure-services"></a>IDs de recursos para serviços Azure
Consulte [os serviços Azure que suportam a autenticação Azure AD](../active-directory/managed-identities-azure-resources/services-support-msi.md) para uma lista de recursos que suportam a Azure AD, e as respetivas IDs de recursos.

## <a name="next-steps"></a>Passos seguintes
* [Implementar uma aplicação Azure Service Fabric com uma identidade gerida atribuída pelo sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Implementar uma aplicação Azure Service Fabric com uma identidade gerida atribuída pelo utilizador](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Conceder um acesso à aplicação Azure Service Fabric a outros recursos azure](./how-to-grant-access-other-resources.md)
