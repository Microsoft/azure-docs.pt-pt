---
title: Usar identidades gerenciadas em uma máquina virtual para adquirir o token de acesso-Azure AD
description: Instruções passo a passo e exemplos para usar identidades gerenciadas para recursos do Azure em máquinas virtuais para adquirir um token de acesso OAuth.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 443f1eb1576f2d6eb28d0de16f37e37912b707b9
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547360"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Como usar identidades gerenciadas para recursos do Azure em uma VM do Azure para adquirir um token de acesso 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que ofereça suporte à autenticação do Azure AD, sem ter credenciais em seu código. 

Este artigo fornece vários exemplos de código e script para aquisição de token, bem como orientações sobre tópicos importantes, como tratamento de expiração de token e erros HTTP. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Se você planeja usar os exemplos de Azure PowerShell neste artigo, certifique-se de instalar a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps).


> [!IMPORTANT]
> - Todo o código/script de exemplo neste artigo pressupõe que o cliente está sendo executado em uma máquina virtual com identidades gerenciadas para recursos do Azure. Use o recurso "conectar" da máquina virtual no portal do Azure para conectar-se remotamente à sua VM. Para obter detalhes sobre como habilitar identidades gerenciadas para recursos do Azure em uma VM, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](qs-configure-portal-windows-vm.md), ou um dos artigos variantes (usando o PowerShell, a CLI, um modelo ou um SDK do Azure). 

> [!IMPORTANT]
> - O limite de segurança de identidades gerenciadas para recursos do Azure é o recurso em que ele está sendo usado. Todos os códigos/scripts em execução em uma máquina virtual podem solicitar e recuperar tokens para quaisquer identidades gerenciadas disponíveis nele. 

## <a name="overview"></a>Visão geral

Um aplicativo cliente pode solicitar identidades gerenciadas para o [token de acesso somente de aplicativo](../develop/developer-glossary.md#access-token) de recursos do Azure para acessar um determinado recurso. O token é [baseado nas identidades gerenciadas para a entidade de serviço de recursos do Azure](overview.md#how-does-the-managed-identities-for-azure-resources-work). Assim, não há necessidade de o cliente se registrar para obter um token de acesso em sua própria entidade de serviço. O token é adequado para uso como um token de portador em [chamadas de serviço a serviço que exigem credenciais de cliente](../develop/v1-oauth2-client-creds-grant-flow.md).

|  |  |
| -------------- | -------------------- |
| [Obter um token usando HTTP](#get-a-token-using-http) | Detalhes de protocolo para identidades gerenciadas do ponto de extremidade de token de recursos do Azure |
| [Obter um token usando a biblioteca Microsoft. Azure. Services. AppAuthentication para .NET](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Exemplo de uso da biblioteca Microsoft. Azure. Services. AppAuthentication de um cliente .NET
| [Obter um token usandoC#](#get-a-token-using-c) | Exemplo de uso de identidades gerenciadas para o ponto de C# extremidade REST de recursos do Azure de um cliente |
| [Obter um token usando o Java](#get-a-token-using-java) | Exemplo de uso de identidades gerenciadas para o ponto de extremidade REST de recursos do Azure de um cliente Java |
| [Obter um token usando go](#get-a-token-using-go) | Exemplo de uso de identidades gerenciadas para o ponto de extremidade REST de recursos do Azure de um cliente go |
| [Obter um token usando Azure PowerShell](#get-a-token-using-azure-powershell) | Exemplo de uso de identidades gerenciadas para o ponto de extremidade REST de recursos do Azure de um cliente do PowerShell |
| [Obter um token usando ONDULAção](#get-a-token-using-curl) | Exemplo de uso de identidades gerenciadas para o ponto de extremidade REST de recursos do Azure de um cliente de bash/ONDULAção |
| Lidando com cache de token | Diretrizes para manipulação de tokens de acesso expirados |
| [Processamento de erros](#error-handling) | Diretrizes para tratamento de erros de HTTP retornados do ponto de extremidade de identidades gerenciadas para o token de recursos do Azure |
| [IDs de recurso para serviços do Azure](#resource-ids-for-azure-services) | Onde obter as IDs de recurso para os serviços do Azure com suporte |

## <a name="get-a-token-using-http"></a>Obter um token usando HTTP 

A interface fundamental para adquirir um token de acesso é baseada em REST, tornando-o acessível para qualquer aplicativo cliente em execução na VM que possa fazer chamadas HTTP REST. Isso é semelhante ao modelo de programação do Azure AD, exceto que o cliente usa um ponto de extremidade na máquina virtual (vs. um ponto de extremidade do Azure AD).

Exemplo de solicitação usando o ponto de extremidade do IMDS (serviço de metadados de instância) do Azure *(recomendado)* :

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Elemento | Descrição |
| ------- | ----------- |
| `GET` | O verbo HTTP, indicando que você deseja recuperar dados do ponto de extremidade. Nesse caso, um token de acesso OAuth. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | O ponto de extremidade de identidades gerenciadas para recursos do Azure para o serviço de metadados de instância. |
| `api-version`  | Um parâmetro de cadeia de caracteres de consulta, que indica a versão da API para o ponto de extremidade IMDS. Use a versão da API `2018-02-01` ou superior. |
| `resource` | Um parâmetro de cadeia de caracteres de consulta, indicando o URI da ID do aplicativo do recurso de destino. Ele também aparece na declaração de `aud` (público) do token emitido. Este exemplo solicita um token para acessar Azure Resource Manager, que tem um URI de ID de aplicativo de https://management.azure.com/. |
| `Metadata` | Um campo de cabeçalho de solicitação HTTP, exigido por identidades gerenciadas para recursos do Azure como uma mitigação contra o ataque de falsificação de solicitação do lado do servidor (SSRF). Esse valor deve ser definido como "true", em todas as letras minúsculas. |
| `object_id` | Adicional Um parâmetro de cadeia de caracteres de consulta, que indica a object_id da identidade gerenciada para a qual você gostaria de obter o token. Necessário, se sua VM tiver várias identidades gerenciadas atribuídas pelo usuário.|
| `client_id` | Adicional Um parâmetro de cadeia de caracteres de consulta, que indica a client_id da identidade gerenciada para a qual você gostaria de obter o token. Necessário, se sua VM tiver várias identidades gerenciadas atribuídas pelo usuário.|
| `mi_res_id` | Adicional Um parâmetro de cadeia de caracteres de consulta, que indica a mi_res_id (ID de recurso do Azure) da identidade gerenciada para a qual você gostaria de obter o token. Necessário, se sua VM tiver várias identidades gerenciadas atribuídas pelo usuário. |

Exemplo de solicitação usando as identidades gerenciadas para os recursos do Azure ponto de extremidade de extensão de VM *(planejado para substituição em janeiro de 2019)* :

```http
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elemento | Descrição |
| ------- | ----------- |
| `GET` | O verbo HTTP, indicando que você deseja recuperar dados do ponto de extremidade. Nesse caso, um token de acesso OAuth. | 
| `http://localhost:50342/oauth2/token` | O ponto de extremidade de identidades gerenciadas para recursos do Azure, em que 50342 é a porta padrão e é configurável. |
| `resource` | Um parâmetro de cadeia de caracteres de consulta, indicando o URI da ID do aplicativo do recurso de destino. Ele também aparece na declaração de `aud` (público) do token emitido. Este exemplo solicita um token para acessar Azure Resource Manager, que tem um URI de ID de aplicativo de https://management.azure.com/. |
| `Metadata` | Um campo de cabeçalho de solicitação HTTP, exigido por identidades gerenciadas para recursos do Azure como uma mitigação contra o ataque de falsificação de solicitação do lado do servidor (SSRF). Esse valor deve ser definido como "true", em todas as letras minúsculas.|
| `object_id` | Adicional Um parâmetro de cadeia de caracteres de consulta, que indica a object_id da identidade gerenciada para a qual você gostaria de obter o token. Necessário, se sua VM tiver várias identidades gerenciadas atribuídas pelo usuário.|
| `client_id` | Adicional Um parâmetro de cadeia de caracteres de consulta, que indica a client_id da identidade gerenciada para a qual você gostaria de obter o token. Necessário, se sua VM tiver várias identidades gerenciadas atribuídas pelo usuário.|

Exemplo de resposta:

```json
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Elemento | Descrição |
| ------- | ----------- |
| `access_token` | O token de acesso solicitado. Ao chamar uma API REST protegida, o token é inserido no campo de cabeçalho de solicitação de `Authorization` como um token de "portador", permitindo que a API autentique o chamador. | 
| `refresh_token` | Não é usado por identidades gerenciadas para recursos do Azure. |
| `expires_in` | O número de segundos que o token de acesso continua a ser válido, antes de expirar, a partir do momento da emissão. O tempo de emissão pode ser encontrado na declaração de `iat` do token. |
| `expires_on` | O TimeSpan quando o token de acesso expira. A data é representada como o número de segundos de "1970-01-01T0:0: 0Z UTC" (corresponde à declaração de `exp` do token). |
| `not_before` | O TimeSpan quando o token de acesso entra em vigor e pode ser aceito. A data é representada como o número de segundos de "1970-01-01T0:0: 0Z UTC" (corresponde à declaração de `nbf` do token). |
| `resource` | O recurso para o qual o token de acesso foi solicitado, que corresponde ao parâmetro de cadeia de caracteres de consulta `resource` da solicitação. |
| `token_type` | O tipo de token, que é um token de acesso "portador", que significa que o recurso pode dar acesso ao portador desse token. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Obter um token usando a biblioteca Microsoft. Azure. Services. AppAuthentication para .NET

Para aplicativos e funções .NET, a maneira mais simples de trabalhar com identidades gerenciadas para recursos do Azure é por meio do pacote Microsoft. Azure. Services. AppAuthentication. Essa biblioteca também permitirá que você teste seu código localmente em seu computador de desenvolvimento, usando sua conta de usuário do Visual Studio, o [CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)ou Active Directory autenticação integrada. Para obter mais informações sobre opções de desenvolvimento local com essa biblioteca, consulte a [referência Microsoft. Azure. Services. AppAuthentication](/azure/key-vault/service-to-service-authentication). Esta seção mostra como começar a usar a biblioteca em seu código.

1. Adicione referências aos pacotes NuGet [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [Microsoft. Azure. keyvault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) ao seu aplicativo.

2.  Adicione o seguinte código ao seu aplicativo:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```
    
Para saber mais sobre o Microsoft. Azure. Services. AppAuthentication e as operações que ele expõe, consulte a [referência do Microsoft. Azure. Services. AppAuthentication](/azure/key-vault/service-to-service-authentication) e o [serviço de aplicativo e o keyvault com identidades gerenciadas para o exemplo do .net de recursos do Azure](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

## <a name="get-a-token-using-c"></a>Obter um token usandoC#

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire managed identities for Azure resources token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

## <a name="get-a-token-using-java"></a>Obter um token usando o Java

Use esta [biblioteca JSON](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) para recuperar um token usando Java.

```Java
import java.io.*;
import java.net.*;
import com.fasterxml.jackson.core.*;
 
class GetMSIToken {
    public static void main(String[] args) throws Exception {
 
        URL msiEndpoint = new URL("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
        HttpURLConnection con = (HttpURLConnection) msiEndpoint.openConnection();
        con.setRequestMethod("GET");
        con.setRequestProperty("Metadata", "true");
 
        if (con.getResponseCode()!=200) {
            throw new Exception("Error calling managed identity token endpoint.");
        }
 
        InputStream responseStream = con.getInputStream();
 
        JsonFactory factory = new JsonFactory();
        JsonParser parser = factory.createParser(responseStream);
 
        while(!parser.isClosed()){
            JsonToken jsonToken = parser.nextToken();
 
            if(JsonToken.FIELD_NAME.equals(jsonToken)){
                String fieldName = parser.getCurrentName();
                jsonToken = parser.nextToken();
 
                if("access_token".equals(fieldName)){
                    String accesstoken = parser.getValueAsString();
                    System.out.println("Access Token: " + accesstoken.substring(0,5)+ "..." + accesstoken.substring(accesstoken.length()-5));
                    return;
                }
            }
        }
    }
}
```

## <a name="get-a-token-using-go"></a>Obter um token usando go

```
package main

import (
  "fmt"
  "io/ioutil"
  "net/http"
  "net/url"
  "encoding/json"
)

type responseJson struct {
  AccessToken string `json:"access_token"`
  RefreshToken string `json:"refresh_token"`
  ExpiresIn string `json:"expires_in"`
  ExpiresOn string `json:"expires_on"`
  NotBefore string `json:"not_before"`
  Resource string `json:"resource"`
  TokenType string `json:"token_type"`
}

func main() {
    
    // Create HTTP request for a managed services for Azure resources token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01")
    if err != nil {
      fmt.Println("Error creating URL: ", err)
      return 
    }
    msi_parameters := url.Values{}
    msi_parameters.Add("resource", "https://management.azure.com/")
    msi_endpoint.RawQuery = msi_parameters.Encode()
    req, err := http.NewRequest("GET", msi_endpoint.String(), nil)
    if err != nil {
      fmt.Println("Error creating HTTP request: ", err)
      return 
    }
    req.Header.Add("Metadata", "true")

    // Call managed services for Azure resources token endpoint
    client := &http.Client{}
    resp, err := client.Do(req) 
    if err != nil{
      fmt.Println("Error calling token endpoint: ", err)
      return
    }

    // Pull out response body
    responseBytes,err := ioutil.ReadAll(resp.Body)
    defer resp.Body.Close()
    if err != nil {
      fmt.Println("Error reading response body : ", err)
      return
    }

    // Unmarshall response body into struct
    var r responseJson
    err = json.Unmarshal(responseBytes, &r)
    if err != nil {
      fmt.Println("Error unmarshalling the response:", err)
      return
    }

    // Print HTTP response and marshalled response body elements to console
    fmt.Println("Response status:", resp.Status)
    fmt.Println("access_token: ", r.AccessToken)
    fmt.Println("refresh_token: ", r.RefreshToken)
    fmt.Println("expires_in: ", r.ExpiresIn)
    fmt.Println("expires_on: ", r.ExpiresOn)
    fmt.Println("not_before: ", r.NotBefore)
    fmt.Println("resource: ", r.Resource)
    fmt.Println("token_type: ", r.TokenType)
}
```

## <a name="get-a-token-using-azure-powershell"></a>Obter um token usando Azure PowerShell

O exemplo a seguir demonstra como usar as identidades gerenciadas para o ponto de extremidade REST de recursos do Azure de um cliente do PowerShell para:

1. Adquirir um token de acesso.
2. Use o token de acesso para chamar uma API REST do Azure Resource Manager e obter informações sobre a VM. Certifique-se de substituir sua ID de assinatura, nome do grupo de recursos e nome da máquina virtual por `<SUBSCRIPTION-ID>`, `<RESOURCE-GROUP>`e `<VM-NAME>`, respectivamente.

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Exemplo de como analisar o token de acesso da resposta:
```azurepowershell
# Get an access token for managed identities for Azure resources
$response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' `
                              -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The managed identities for Azure resources access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01' -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>Obter um token usando ONDULAção

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Exemplo de como analisar o token de acesso da resposta:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Cache de token

Enquanto as identidades gerenciadas para o subsistema de recursos do Azure sendo usado (identidades IMDS/gerenciadas para extensão de VM de recursos do Azure) armazenam tokens de cache, também recomendamos implementar o cache de token em seu código. Como resultado, você deve se preparar para cenários em que o recurso indica que o token expirou. 

Chamadas durante a transmissão para o resultado do Azure AD somente quando:
- o erro de cache ocorre devido a nenhum token nas identidades gerenciadas do cache do subsistema de recursos do Azure
- o token armazenado em cache expirou

## <a name="error-handling"></a>Processamento de erros

O ponto de extremidade de identidades gerenciadas para recursos do Azure sinaliza erros por meio do campo código de status do cabeçalho da mensagem de resposta HTTP, como erros 4xx ou 5xx:

| Código de status | Motivo do erro | Como tratar |
| ----------- | ------------ | ------------- |
| 404 não encontrado. | O ponto de extremidade IMDS está atualizando. | Tente novamente com a retirada de Expontential. Consulte as diretrizes abaixo. |
| 429 número excessivo de solicitações. |  Limite de limitação de IMDS atingido. | Tente novamente com retirada exponencial. Consulte as diretrizes abaixo. |
| Erro de 4xx na solicitação. | Um ou mais dos parâmetros de solicitação estavam incorretos. | Não tente novamente.  Examine os detalhes do erro para obter mais informações.  erros de 4xx são erros de tempo de design.|
| erro transitório de 5xx do serviço. | As identidades gerenciadas para o subsistema de recursos do Azure ou Azure Active Directory retornaram um erro transitório. | É seguro tentar novamente depois de aguardar pelo menos 1 segundo.  Se você tentar novamente com muita rapidez ou muitas vezes, o IMDS e/ou o Azure AD poderão retornar um erro de limite de taxa (429).|
| tempo limite | O ponto de extremidade IMDS está atualizando. | Tente novamente com a retirada de Expontential. Consulte as diretrizes abaixo. |

Se ocorrer um erro, o corpo de resposta HTTP correspondente conterá JSON com os detalhes do erro:

| Elemento | Descrição |
| ------- | ----------- |
| erro   | Identificador de erro. |
| error_description | Descrição detalhada do erro. **As descrições de erro podem ser alteradas a qualquer momento. Não grave o código que ramifica com base nos valores na descrição do erro.**|

### <a name="http-response-reference"></a>Referência de resposta HTTP

Esta seção documenta as possíveis respostas de erro. Um status "200 OK" é uma resposta bem-sucedida e o token de acesso está contido no JSON do corpo de resposta, no elemento access_token.

| Código de estado | Erro | Descrição do erro | Solução |
| ----------- | ----- | ----------------- | -------- |
| 400 solicitação inadequada | invalid_resource | AADSTS50001: o aplicativo chamado *\<URI\>* não foi encontrado no locatário chamado *\<tenant-ID\>* . Isso pode acontecer se o aplicativo não tiver sido instalado pelo administrador do locatário ou consentido por qualquer usuário no locatário. Você pode ter enviado sua solicitação de autenticação para o locatário errado. \ | (Somente Linux) |
| 400 solicitação inadequada | bad_request_102 | Cabeçalho de metadados necessário não especificado | O campo de cabeçalho de solicitação `Metadata` está ausente da sua solicitação ou está formatado incorretamente. O valor deve ser especificado como `true`, em todas as letras minúsculas. Consulte a "solicitação de exemplo" na seção REST anterior para obter um exemplo.|
| 401 não autorizado | unknown_source | URI de *\<* de origem desconhecido\> | Verifique se o URI de solicitação HTTP GET está formatado corretamente. A parte `scheme:host/resource-path` deve ser especificada como `http://localhost:50342/oauth2/token`. Consulte a "solicitação de exemplo" na seção REST anterior para obter um exemplo.|
|           | invalid_request | A solicitação não tem um parâmetro obrigatório, inclui um valor de parâmetro inválido, inclui um parâmetro mais de uma vez ou está malformado. |  |
|           | unauthorized_client | O cliente não está autorizado a solicitar um token de acesso usando este método. | Causado por uma solicitação que não usou o loopback local para chamar a extensão ou em uma VM que não tem identidades gerenciadas para recursos do Azure configurados corretamente. Consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](qs-configure-portal-windows-vm.md) se você precisar de assistência com a configuração da VM. |
|           | access_denied | O proprietário do recurso ou o servidor de autorização negou a solicitação. |  |
|           | unsupported_response_type | O servidor de autorização não dá suporte à obtenção de um token de acesso usando esse método. |  |
|           | invalid_scope | O escopo solicitado é inválido, desconhecido ou malformado. |  |
| erro interno do servidor 500 | conhecidos | Falha ao recuperar o token do Active Directory. Para obter detalhes, consulte logs em *\<caminho do arquivo\>* | Verifique se as identidades gerenciadas dos recursos do Azure foram habilitadas na VM. Consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](qs-configure-portal-windows-vm.md) se você precisar de assistência com a configuração da VM.<br><br>Verifique também se o URI de solicitação HTTP GET está formatado corretamente, particularmente o URI de recurso especificado na cadeia de caracteres de consulta. Consulte a "solicitação de exemplo" na seção REST anterior para obter um exemplo ou [Serviços do Azure que dão suporte à autenticação do Azure ad](services-support-msi.md) para obter uma lista de serviços e suas respectivas IDs de recurso.

## <a name="retry-guidance"></a>Diretrizes de repetição 

É recomendável tentar novamente se você receber um código de erro 404, 429 ou 5xx (consulte [tratamento de erros](#error-handling) acima).

Limites de limitação se aplicam ao número de chamadas feitas ao ponto de extremidade IMDS. Quando o limite de limitação for excedido, o ponto de extremidade IMDS limitará quaisquer outras solicitações enquanto a limitação estiver em vigor. Durante esse período, o ponto de extremidade IMDS retornará o código de status HTTP 429 ("muitas solicitações") e as solicitações falharão. 

Para tentar novamente, recomendamos a seguinte estratégia: 

| **Estratégia de repetição** | **Definições** | **Valores** | **Como funciona** |
| --- | --- | --- | --- |
|ExponentialBackoff |Contagem de repetições<br />Término mín.<br />Término máx.<br />Término delta<br />Primeira repetição rápida |5<br />0 s<br />60 s<br />2 s<br />false |Tentativa 1 – atraso de 0 s<br />Tentativa 2 – atraso de ~2 s<br />Tentativa 3 – atraso de ~6 s<br />Tentativa 4 – atraso de ~14 s<br />Tentativa 5 – atraso de ~30 s |

## <a name="resource-ids-for-azure-services"></a>IDs de recurso para serviços do Azure

Consulte [Serviços do Azure que dão suporte à autenticação do Azure ad](services-support-msi.md) para obter uma lista de recursos que dão suporte ao Azure AD e foram testados com identidades gerenciadas para recursos do Azure e suas respectivas IDs de recurso.


## <a name="next-steps"></a>Passos seguintes

- Para habilitar identidades gerenciadas para recursos do Azure em uma VM do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](qs-configure-portal-windows-vm.md).








