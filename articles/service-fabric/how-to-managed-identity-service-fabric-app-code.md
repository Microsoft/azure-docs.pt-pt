---
title: Service Fabric do Azure – usar identidade gerenciada com aplicativos Service Fabric | Microsoft Docs
description: Como usar identidades gerenciadas de Service Fabric código do aplicativo
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 7/25/2019
ms.author: atsenthi
ms.openlocfilehash: 528e1b0a353cdcd716f9bca63c423af7a6f12641
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68958242"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>Como aproveitar a identidade gerenciada de um aplicativo Service Fabric para acessar os serviços do Azure

Service Fabric aplicativos podem aproveitar identidades gerenciadas para acessar outros recursos do Azure que dão suporte à autenticação baseada em Azure Active Directory. Um aplicativo pode obter um [token de acesso](../active-directory/develop/developer-glossary.md#access-token) que representa sua identidade, que pode ser atribuído pelo sistema ou atribuído pelo usuário, e usá-lo como um token de "portador" para se autenticar em outro serviço, também conhecido como um [servidor de recursos protegido](../active-directory/develop/developer-glossary.md#resource-server). O token representa a identidade atribuída ao aplicativo Service Fabric e será emitido somente para recursos do Azure (incluindo aplicativos da it) que compartilham essa identidade. Consulte a documentação [visão geral da identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para obter uma descrição detalhada das identidades gerenciadas, bem como a distinção entre identidades atribuídas pelo sistema e pelo usuário. Vamos nos referir a um aplicativo de Service Fabric habilitado para identidade gerenciada como o [aplicativo cliente](../active-directory/develop/developer-glossary.md#client-application) em todo este artigo.

> [!IMPORTANT]
> Uma identidade gerenciada representa a associação entre um recurso do Azure e uma entidade de serviço no locatário do Azure AD correspondente associado à assinatura que contém o recurso. Como tal, no contexto de Service Fabric, identidades gerenciadas têm suporte apenas para aplicativos implantados como recursos do Azure. 

> [!IMPORTANT]
> Antes de usar a identidade gerenciada de um aplicativo Service Fabric, o aplicativo cliente deve receber acesso ao recurso protegido. Consulte a lista de [Serviços do Azure que dão suporte à autenticação do Azure ad](/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-managed-identities-for-azure-resources) para verificar o suporte e, em seguida, para a documentação do respectivo serviço para obter as etapas específicas para conceder um acesso de identidade aos recursos de interesse. 

## <a name="acquiring-an-access-token-using-rest-api"></a>Adquirindo um token de acesso usando a API REST
Em clusters habilitados para identidade gerenciada, o tempo de execução de Service Fabric expõe um ponto de extremidade de localhost que os aplicativos podem usar para obter tokens de acesso. O ponto de extremidade está disponível em todos os nós do cluster e está acessível a todas as entidades nesse nó. Chamadores autorizados podem obter tokens de acesso chamando esse ponto de extremidade e apresentando um código de autenticação; o código é gerado pelo tempo de execução de Service Fabric para cada ativação de pacote de código de serviço distinto e está associado ao tempo de vida do processo que hospeda esse pacote de código de serviço.

Especificamente, o ambiente de um serviço de Service Fabric habilitado para identidade gerenciada será propagado com as seguintes variáveis:
- ' MSI_ENDPOINT ': o ponto de extremidade de localhost, completo com caminho, versão de API e parâmetros correspondentes à identidade gerenciada desse serviço
- ' MSI_SECRET ': um código de autenticação, que é uma cadeia de caracteres opaca e representa exclusivamente o serviço no nó atual

> [!NOTE]
> Os nomes ' MSI_ENDPOINT ' e ' MSI_SECRET ' referem-se à designação anterior de identidades gerenciadas ("Identidade de Serviço Gerenciada") e que agora está preterida. Os nomes também são consistentes com os nomes de variável de ambiente equivalentes usados por outros serviços do Azure que dão suporte a identidades gerenciadas.

> [!IMPORTANT]
> O código do aplicativo deve considerar o valor da variável de ambiente ' MSI_SECRET ' como dados confidenciais-ele não deve ser registrado em log ou ser disseminado de outra forma. O código de autenticação não tem valor fora do nó local ou depois que o processo que hospeda o serviço termina, mas ele representa a identidade do serviço de Service Fabric e, portanto, deve ser tratado com as mesmas precauções que o token de acesso em si.

Para obter um token, o cliente executa as seguintes etapas:
- forma um URI concatenando o ponto de extremidade de identidade gerenciada (valor MSI_ENDPOINT) com a versão da API e o recurso (público) necessário para o token
- Cria uma solicitação HTTP GET para o URI especificado
- Adiciona o código de autenticação (valor MSI_SECRET) como um cabeçalho à solicitação
- envia a solicitação

Uma resposta bem-sucedida conterá um conteúdo JSON que representa o token de acesso resultante, bem como os metadados que o descrevem. Uma resposta com falha também incluirá uma explicação da falha. Veja abaixo detalhes adicionais sobre o tratamento de erros.

Os tokens de acesso serão armazenados em cache por Service Fabric em vários níveis (nó, cluster, serviço de provedor de recursos), portanto, uma resposta bem-sucedida não significa, necessariamente, que o token foi emitido diretamente em resposta à solicitação do aplicativo do usuário. Os tokens serão armazenados em cache por menos do que o tempo de vida e, portanto, um aplicativo terá a garantia de receber um token válido. É recomendável que o código do aplicativo armazene em cache todos os tokens de acesso adquiridos; a chave de cache deve incluir (uma derivação) do público. 


Exemplo de solicitação:
```http
GET 'http://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://keyvault.azure.com/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
posição

| Elemento | Descrição |
| ------- | ----------- |
| `GET` | O verbo HTTP, indicando que você deseja recuperar dados do ponto de extremidade. Nesse caso, um token de acesso OAuth. | 
| `http://localhost:2377/metadata/identity/oauth2/token` | O ponto de extremidade de identidade gerenciada para aplicativos Service Fabric, fornecido por meio da variável de ambiente MSI_ENDPOINT. |
| `api-version` | Um parâmetro de cadeia de caracteres de consulta, especificando a versão de API do serviço de token de identidade gerenciado; Atualmente, o único valor aceito `2019-07-01-preview`é e está sujeito a alterações. |
| `resource` | Um parâmetro de cadeia de caracteres de consulta, indicando o URI da ID do aplicativo do recurso de destino. Isso será refletido como a `aud` declaração (público) do token emitido. Este exemplo solicita um token para acessar Azure Key Vault, cujo URI de ID do aplicativo https://keyvault.azure.com/ é. |
| `Secret` | Um campo de cabeçalho de solicitação HTTP, exigido pelo Service Fabric serviço de token de identidade gerenciada para serviços de Service Fabric para autenticar o chamador. Esse valor é fornecido pelo tempo de execução da it por meio da variável de ambiente MSI_SECRET. |


Exemplo de resposta:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://keyvault.azure.com/"
}
```
posição

| Elemento | Descrição |
| ------- | ----------- |
| `token_type` | O tipo de token; Nesse caso, um token de acesso "portador", que significa que o apresentador ("portador") desse token é o assunto pretendido do token. |
| `access_token` | O token de acesso solicitado. Ao chamar uma API REST protegida, o token é inserido no `Authorization` campo de cabeçalho de solicitação como um token de "portador", permitindo que a API autentique o chamador. | 
| `expires_on` | O carimbo de data/hora da expiração do token de acesso; representado como o número de segundos de "1970-01-01T0:0: 0Z UTC" e corresponde à declaração do `exp` token. Nesse caso, o token expira em 2019-08-08T06:10:11 + 00:00 (em RFC 3339)|
| `resource` | O recurso para o qual o token de acesso foi emitido, especificado `resource` por meio do parâmetro de cadeia de caracteres de consulta da solicitação; corresponde à declaração ' AUD ' do token. |


## <a name="acquiring-an-access-token-using-c"></a>Adquirindo um token de acesso usandoC#
O acima se torna, C#em:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;

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
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("MSI_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("MSI_SECRET");
            var managedIdentityAuthenticationHeader = "secret";
            var managedIdentityApiVersion = "2019-07-01-preview";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);

            try
            {
                var response = await new HttpClient().SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var accessToken = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                return accessToken.Trim('"');
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
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Acessando Key Vault de um aplicativo Service Fabric usando a identidade gerenciada
Este exemplo baseia-se no acima para demonstrar o acesso a um segredo armazenado em um Key Vault usando a identidade gerenciada.

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

            Console.WriteLine($"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Console.WriteLine("\n== Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    var secretMetadata = secretResponse.Body.ToString();
                    Console.WriteLine($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    Console.WriteLine($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
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

            Console.WriteLine(response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority"></param>
        /// <param name="resource"></param>
        /// <param name="scope"></param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            if (config.DoVerboseLogging)
                Console.WriteLine($"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";
            if (config.DoVerboseLogging)
                Console.WriteLine($"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            if (config.DoVerboseLogging)
                Console.WriteLine($"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            if (config.DoVerboseLogging)
                Console.WriteLine($"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var accessToken = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            if (config.DoVerboseLogging)
                Console.WriteLine("returning access code..");

            return accessToken.Trim('"');
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
```

## <a name="error-handling"></a>Processamento de erros
O campo ' código de status ' do cabeçalho de resposta HTTP indica o status de êxito da solicitação; um status ' 200 OK ' indica êxito e a resposta incluirá o token de acesso conforme descrito acima. Veja a seguir uma breve enumeração de possíveis respostas de erro.

| Código de Estado | Motivo do erro | Como tratar |
| ----------- | ------------ | ------------- |
| 404 não encontrado. | Código de autenticação desconhecido ou o aplicativo não recebeu uma identidade gerenciada. | Corrija a instalação do aplicativo ou o código de aquisição de token. |
| 429 número excessivo de solicitações. |  Limite de limitação atingido, imposto pelo AAD ou pelo it. | Tente novamente com retirada exponencial. Consulte as diretrizes abaixo. |
| Erro de 4xx na solicitação. | Um ou mais dos parâmetros de solicitação estavam incorretos. | Não tente novamente.  Examine os detalhes do erro para obter mais informações.  erros de 4xx são erros de tempo de design.|
| Erro 5xx do serviço. | O subsistema de identidade gerenciada ou Azure Active Directory retornou um erro transitório. | É seguro tentar novamente após alguns instantes. Você pode atingir uma condição de limitação (429) ao tentar novamente.|

Se ocorrer um erro, o corpo da resposta HTTP correspondente conterá um objeto JSON com os detalhes do erro:

| Elemento | Descrição |
| ------- | ----------- |
| code | Código de erro. |
| correlationId | Uma ID de correlação que pode ser usada para depuração. |
| message | Descrição detalhada do erro. **As descrições de erro podem ser alteradas a qualquer momento. Não dependa da própria mensagem de erro.**|

Erro de exemplo:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

A seguir está uma lista de erros típicos de Service Fabric específicos para identidades gerenciadas:

| Código | Message | Descrição | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | O segredo não foi encontrado nos cabeçalhos da solicitação. | O código de autenticação não foi fornecido com a solicitação. | 
| ManagedIdentityNotFound | Identidade gerenciada não encontrada para o host de aplicativo especificado. | O aplicativo não tem nenhuma identidade ou o código de autenticação é desconhecido. |
| ArgumentNullOrEmpty | O parâmetro ' Resource ' não deve ser uma cadeia de caracteres nula ou vazia. | O recurso (público) não foi fornecido na solicitação. |
| InvalidApiVersion | Não há suporte para a versão de API ' '. A versão com suporte é ' 2019-07-01-preview '. | Versão de API ausente ou sem suporte especificada no URI de solicitação. |
| InternalServerError | Ocorreu um erro. | Foi encontrado um erro no subsistema de identidade gerenciada, possivelmente fora da pilha de Service Fabric. A causa mais provável é um valor incorreto especificado para o recurso (verifique se há '/' à direita?) | 

## <a name="retry-guidance"></a>Diretrizes de repetição 

Normalmente, o único código de erro com nova tentativa é 429 (muitas solicitações); erros de servidor interno/códigos de erro 5xx podem ser tentados novamente, embora a causa possa ser permanente. 

Limites de limitação se aplicam ao número de chamadas feitas ao subsistema de identidade gerenciada – especificamente as dependências de "upstream" (o serviço do Azure de identidade gerenciada ou o serviço de token seguro). O Service Fabric armazena em cache tokens em vários níveis no pipeline, mas, considerando a natureza distribuída dos componentes envolvidos, o chamador pode sofrer respostas de limitação inconsistentes (ou seja, limitado em um nó/instância de um aplicativo, mas não em um nó diferente ao solicitar um token para a mesma identidade.) Quando a condição de limitação é definida, as solicitações subsequentes do mesmo aplicativo podem falhar com o código de status HTTP 429 (muitas solicitações) até que a condição seja apagada.  

É recomendável que as solicitações que falharam devido à limitação sejam repetidas com uma retirada exponencial, da seguinte maneira: 

| Índice de chamadas | Ação ao receber 429 | 
| --- | --- | 
| 1 | Aguarde 1 segundo e tente novamente |
| 2 | Aguarde 2 segundos e tente novamente |
| 3 | Aguarde 4 segundos e tente novamente |
| 4 | Aguarde 8 segundos e tente novamente |
| 4 | Aguarde 8 segundos e tente novamente |
| 5 | Aguarde 16 segundos e tente novamente |

## <a name="resource-ids-for-azure-services"></a>IDs de recurso para serviços do Azure
Consulte [Serviços do Azure que dão suporte à autenticação do Azure ad](../active-directory/managed-identities-azure-resources/services-support-msi.md) para obter uma lista de recursos que dão suporte ao Azure AD e suas respectivas IDs de recursos.

## <a name="next-steps"></a>Passos Seguintes
* [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo usuário](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Conceder a um aplicativo de Service Fabric do Azure acesso a outros recursos do Azure](./how-to-grant-access-other-resources.md)

## <a name="see-also"></a>Consulte também

* Examinar o [suporte de identidade gerenciada](./concepts-managed-identity.md) no Azure Service Fabric

* [Implantar um novo](./configure-new-azure-service-fabric-enable-managed-identity.md) Cluster de Service Fabric do Azure com suporte de identidade gerenciada 

* [Habilitar identidade gerenciada](./configure-existing-cluster-enable-managed-identity-token-service.md) em um cluster existente do Service Fabric do Azure