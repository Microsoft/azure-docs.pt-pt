---
title: Como utilizar identidades geridas para recursos do Azure numa máquina virtual para adquirir um token de acesso
description: Instruções passo a passo instruções e exemplos de utilização geridos identidades para recursos do Azure numa máquina virtual para adquirir um token de acesso de OAuth.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09ddce0dc398a078b3a901d224f00ef41550bf89
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437668"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Como utilizar identidades geridas para recursos do Azure numa VM do Azure para adquirir um token de acesso 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Identidades geridas para recursos do Azure fornece serviços do Azure com uma identidade gerida automaticamente no Azure Active Directory. Pode utilizar esta identidade para autenticar a qualquer serviço que suporta a autenticação do Azure AD, sem ter credenciais em seu código. 

Este artigo fornece vários exemplos de código e script para a aquisição do token, bem como orientações sobre tópicos importantes, como a manipulação de erros HTTP e de expiração do token. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Se planeja usar os exemplos do Azure PowerShell neste artigo, certifique-se de que instala a versão mais recente do [do Azure PowerShell](/powershell/azure/install-az-ps).


> [!IMPORTANT]
> - Todos os código/script de exemplo neste artigo supõe que o cliente está em execução numa máquina virtual com identidades geridas para recursos do Azure. Utilize a funcionalidade de "Ligar" da máquina virtual no portal do Azure, para ligar remotamente à VM. Para obter detalhes sobre como ativar identidades geridas para recursos do Azure numa VM, consulte [configurar geridos identidades para recursos do Azure numa VM com o portal do Azure](qs-configure-portal-windows-vm.md), ou um dos artigos variantes (com o PowerShell, CLI, um modelo ou um Azure SDK). 

> [!IMPORTANT]
> - O limite de segurança de identidades geridas para recursos do Azure, é o recurso está a ser utilizado no. Todos os código/scripts em execução numa máquina virtual pode pedir e obtenção de tokens para qualquer identidades geridas disponíveis no mesmo. 

## <a name="overview"></a>Descrição geral

Uma aplicação cliente pode pedir identidades geridas para recursos do Azure [token de acesso só de aplicação](../develop/developer-glossary.md#access-token) para aceder a um determinado recurso. O token é [com base em identidades geridas para o principal de serviço de recursos do Azure](overview.md#how-does-it-work). Como tal, não é necessário para o cliente registar-se para obter um token de acesso em seu próprio principal de serviço. O token é adequado para utilização como um token de portador no [chama o serviço a serviço exigir credenciais de cliente](../develop/v1-oauth2-client-creds-grant-flow.md).

|  |  |
| -------------- | -------------------- |
| [Obter um token através de HTTP](#get-a-token-using-http) | Ponto final de token de detalhes de protocolo de identidades geridas para recursos do Azure |
| [Obter um token com a biblioteca de Microsoft.Azure.Services.AppAuthentication para .NET](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Exemplo de como utilizar a biblioteca de Microsoft.Azure.Services.AppAuthentication de um cliente .NET
| [Obter um token com c#](#get-a-token-using-c) | Exemplo do uso de identidades geridas para o ponto final REST de recursos do Azure de um cliente do c# |
| [Obter um token com Java](#get-a-token-using-java) | Exemplo do uso de identidades geridas para o ponto final REST de recursos do Azure de um cliente de Java |
| [Obter um token com Go](#get-a-token-using-go) | Exemplo do uso de identidades geridas para o ponto final REST de recursos do Azure de um cliente do Go |
| [Obter um token com o Azure PowerShell](#get-a-token-using-azure-powershell) | Exemplo do uso de identidades geridas para o ponto final REST de recursos do Azure de um cliente do PowerShell |
| [Obter um token com o CURL](#get-a-token-using-curl) | Exemplo do uso de identidades geridas para o ponto final REST de recursos do Azure de um cliente de Bash/CURL |
| Manipulação de tokens em cache | Orientações para a manipulação de tokens de acesso expirado |
| [Processamento de erros](#error-handling) | Orientações para a manipulação de erros HTTP devolvidos das identidades geridas para o ponto final de token de recursos do Azure |
| [IDs de recurso para serviços do Azure](#resource-ids-for-azure-services) | Onde obter os IDs de recurso para os serviços do Azure suportados |

## <a name="get-a-token-using-http"></a>Obter um token através de HTTP 

A interface fundamental para obter um token de acesso baseia-se em REST, tornando-o acessível a qualquer aplicação de cliente em execução na VM que pode fazer chamadas REST de HTTP. Isso é semelhante ao modelo de programação do Azure AD, exceto o cliente utiliza um ponto de extremidade na máquina virtual (vs do Azure o ponto final).

Pedido de exemplo com o ponto de extremidade do serviço de metadados de instância do Azure (IMDS) *(recomendado)*:

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Elemento | Descrição |
| ------- | ----------- |
| `GET` | O verbo HTTP, que indica que deseja recuperar dados a partir do ponto final. Neste caso, um OAuth acessar o token. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Identidades geridas para o ponto final de recursos do Azure para o serviço de metadados de instância. |
| `api-version`  | Um parâmetro da cadeia de consulta, que indica a versão de API para o ponto final IMDS. Utilize a API version `2018-02-01` ou superior. |
| `resource` | Um parâmetro da cadeia de consulta, que indica o URI de ID de aplicação do recurso de destino. É também apresentado no `aud` afirmação (público) do token emitido. Neste exemplo pede um token para aceder ao Azure Resource Manager, que tem um URI de ID de aplicação de https://management.azure.com/. |
| `Metadata` | Um HTTP pedido campo de cabeçalho, necessário para identidades geridas para recursos do Azure como uma atenuação contra ataques de falsificação de solicitação de lado do servidor (SSRF). Este valor tem de ser definido como "true", em minúsculas. |
| `object_id` | (Opcional) Um parâmetro da cadeia de consulta, que indica o object_id de a identidade gerida que gostaria de ter o token para. Necessário se a VM tiver várias identidades geridas atribuído ao utilizador.|
| `client_id` | (Opcional) Um parâmetro da cadeia de consulta, que indica o client_id de a identidade gerida que gostaria de ter o token para. Necessário se a VM tiver várias identidades geridas atribuído ao utilizador.|

Pedido de exemplo com as identidades geridas para recursos do Azure ponto final da extensão de VM *(planeada para preterição em Janeiro de 2019)*:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elemento | Descrição |
| ------- | ----------- |
| `GET` | O verbo HTTP, que indica que deseja recuperar dados a partir do ponto final. Neste caso, um OAuth acessar o token. | 
| `http://localhost:50342/oauth2/token` | Identidades geridas para o ponto final de recursos do Azure, onde 50342 é a porta predefinida e é configurável. |
| `resource` | Um parâmetro da cadeia de consulta, que indica o URI de ID de aplicação do recurso de destino. É também apresentado no `aud` afirmação (público) do token emitido. Neste exemplo pede um token para aceder ao Azure Resource Manager, que tem um URI de ID de aplicação de https://management.azure.com/. |
| `Metadata` | Um HTTP pedido campo de cabeçalho, necessário para identidades geridas para recursos do Azure como uma atenuação contra ataques de falsificação de solicitação de lado do servidor (SSRF). Este valor tem de ser definido como "true", em minúsculas.|
| `object_id` | (Opcional) Um parâmetro da cadeia de consulta, que indica o object_id de a identidade gerida que gostaria de ter o token para. Necessário se a VM tiver várias identidades geridas atribuído ao utilizador.|
| `client_id` | (Opcional) Um parâmetro da cadeia de consulta, que indica o client_id de a identidade gerida que gostaria de ter o token para. Necessário se a VM tiver várias identidades geridas atribuído ao utilizador.|


Resposta de exemplo:

```
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
| `access_token` | O token de acesso solicitado. Ao chamar uma API de REST segura, o token está incorporado a `Authorization` campo de cabeçalho de pedido como um token de "bearer", permitindo que a API para autenticar o chamador. | 
| `refresh_token` | Não utilizado por identidades geridas para recursos do Azure. |
| `expires_in` | O número de segundos que o token de acesso continua a ser válida, antes de expirar, a partir da hora de emissão. Hora de emissão pode ser encontrada no token de `iat` de afirmação. |
| `expires_on` | O período de tempo quando o token de acesso expira. A data é representada como o número de segundos a partir de "1970-01-01T0:0:0Z UTC" (corresponde do token `exp` afirmação). |
| `not_before` | O período de tempo quando o token de acesso entra em vigor e pode ser aceites. A data é representada como o número de segundos a partir de "1970-01-01T0:0:0Z UTC" (corresponde do token `nbf` afirmação). |
| `resource` | O recurso o token de acesso foi pedido para que corresponde ao `resource` parâmetro de cadeia de caracteres do pedido de consulta. |
| `token_type` | O tipo de token, que é um token de acesso de "Bearer", o que significa que o recurso pode conceder acesso para o portador do token. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Obter um token com a biblioteca de Microsoft.Azure.Services.AppAuthentication para .NET

Para aplicações de .NET e as funções, a maneira mais simples de trabalhar com identidades geridas para recursos do Azure é de pacote Microsoft.Azure.Services.AppAuthentication. Esta biblioteca também permitirá que teste seu código localmente no computador de desenvolvimento, com a sua conta de utilizador a partir do Visual Studio, o [CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest), ou autenticação integrada do Active Directory. Para obter mais informações sobre as opções de desenvolvimento local com esta biblioteca, consulte a [Microsoft.Azure.Services.AppAuthentication referência](/azure/key-vault/service-to-service-authentication). Esta secção mostra-lhe como começar com a biblioteca no seu código.

1. Adicionar referências para o [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) pacotes de NuGet ao seu aplicativo.

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
    
Para saber mais sobre Microsoft.Azure.Services.AppAuthentication e as operações que expõe, veja a [referência Microsoft.Azure.Services.AppAuthentication](/azure/key-vault/service-to-service-authentication) e o [serviço de aplicações e o Cofre de chaves com geridos identidades de exemplo de .NET de recursos do Azure](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

## <a name="get-a-token-using-c"></a>Obter um token com c#

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

## <a name="get-a-token-using-java"></a>Obter um token com Java

Utilize esta opção [biblioteca JSON](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) para obter um token com Java.

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

## <a name="get-a-token-using-go"></a>Obter um token com Go

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

## <a name="get-a-token-using-azure-powershell"></a>Obter um token com o Azure PowerShell

O exemplo seguinte demonstra como utilizar as identidades geridas para o ponto final REST de recursos do Azure de um cliente de PowerShell para:

1. Adquira um token de acesso.
2. Utilize o token de acesso para chamar uma API de REST do Azure Resource Manager e obter informações sobre a VM. Não se esqueça de substituir o seu ID de subscrição, o nome do grupo de recursos e o nome da máquina virtual para `<SUBSCRIPTION-ID>`, `<RESOURCE-GROUP>`, e `<VM-NAME>`, respectivamente.

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Exemplo sobre como analisar o token de acesso da resposta:
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

## <a name="get-a-token-using-curl"></a>Obter um token com o CURL

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Exemplo sobre como analisar o token de acesso da resposta:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Tokens em cache

Embora as identidades geridas de subsistema de recursos do Azure a ser utilizada (gerida/IMDS identidades para recursos do Azure a extensão de VM) em cache tokens, também é recomendável para implementar a colocação em cache de token em seu código. Como resultado, deve se preparar para cenários em que o recurso indica que o token expirou. 

On-the-wire chamadas para o Azure AD apenas resultam quando:
- Falha de acerto na cache ocorre devido a nenhum token nas identidades geridas para a cache do subsistema de recursos do Azure
- o token em cache está expirado

## <a name="error-handling"></a>Processamento de erros

Identidades geridas para erros de sinais de ponto final do recursos do Azure através do campo de código de status do cabeçalho de mensagem de resposta HTTP, como erros 4xx ou 5xx:

| Código de Estado | Motivo do erro | Como lidar com |
| ----------- | ------------ | ------------- |
| 404 não encontrado. | Ponto final IMDS está a atualizar. | Tente novamente com um término Expontential. Consulte as orientações abaixo. |
| 429 demasiados pedidos. |  Atingido o limite de limitação IMDS. | Tente novamente com um término exponencial. Consulte as orientações abaixo. |
| Erro de 4xx no pedido. | Um ou mais dos parâmetros estava incorreta. | Não repita.  Examine os detalhes do erro para obter mais informações.  erros de 4xx são erros de tempo de design.|
| 5XX erro transitório do serviço. | As identidades geridas para o subsistema de recursos do Azure ou Azure Active Directory devolveu um erro transitório. | É seguro tentar novamente depois de aguardar pelo menos de 1 segundo.  Se repetir de forma demasiado rápida ou com muita frequência, IMDS e/ou do Azure AD pode devolver um erro de limite de taxa (429).|
| tempo limite | Ponto final IMDS está a atualizar. | Tente novamente com um término Expontential. Consulte as orientações abaixo. |

Se ocorrer um erro, o corpo da resposta HTTP correspondente contém JSON com os detalhes do erro:

| Elemento | Descrição |
| ------- | ----------- |
| erro   | Identificador de erro. |
| error_description | Descrição verbosa do erro. **Descrições de erro podem alterar a qualquer momento. Não escreva código que ramos com base nos valores na descrição do erro.**|

### <a name="http-response-reference"></a>Referência de resposta HTTP

Esta secção documenta as respostas de erro possíveis. A "200 OK" estado é uma resposta com êxito e o token de acesso está contido no corpo da resposta JSON, no elemento access_token.

| Código de estado | Erro | Descrição de Erro | Solução |
| ----------- | ----- | ----------------- | -------- |
| 400 pedido inválido | invalid_resource | AADSTS50001: A aplicação com o nome *\<URI\>* não foi encontrado no inquilino com o nome  *\<TENANT-ID\>*. Isto pode acontecer se a aplicação não foi instalada pelo administrador do inquilino ou permitida por qualquer utilizador no inquilino. Poderá ter enviado o pedido de autenticação para o inquilino errado. \ | (Apenas Linux) |
| 400 pedido inválido | bad_request_102 | Cabeçalho de metadados necessários não especificado | Ambos os `Metadata` campo de cabeçalho do pedido está em falta na sua solicitação ou está formatado incorretamente. O valor deve ser especificado como `true`, em minúsculas. Consulte o "pedido de exemplo" na secção anterior do REST para obter um exemplo.|
| 401 não autorizado | unknown_source | Origem desconhecida  *\<URI\>* | Certifique-se de que o URI do pedido HTTP GET está formatado corretamente. O `scheme:host/resource-path` parte deve ser especificada como `http://localhost:50342/oauth2/token`. Consulte o "pedido de exemplo" na secção anterior do REST para obter um exemplo.|
|           | invalid_request | O pedido está em falta um parâmetro necessário, inclui um valor de parâmetro inválido, inclui um parâmetro de mais de uma vez ou caso contrário, tem um formato incorreto. |  |
|           | unauthorized_client | O cliente não está autorizado para pedir um token de acesso através deste método. | Causado por um pedido que não utiliza local loopback para chamar a extensão, ou numa VM que não tem identidades geridas para recursos do Azure configurados corretamente. Ver [configurar geridos identidades para recursos do Azure numa VM com o portal do Azure](qs-configure-portal-windows-vm.md) se precisar de assistência com a configuração da VM. |
|           | access_denied | O proprietário do recurso ou autorização servidor negou o pedido. |  |
|           | unsupported_response_type | O servidor de autorização não suporta a obtenção de um token de acesso através deste método. |  |
|           | invalid_scope | Âmbito do pedido é inválido, desconhecido ou com formato incorreto. |  |
| Erro de servidor interno 500 | desconhecido | Falha ao obter o token do Active directory. Para obter detalhes, consulte os registos no  *\<caminho do ficheiro\>* | Certifique-se de que as identidades geridas para recursos do Azure tenha sido ativada na VM. Ver [configurar geridos identidades para recursos do Azure numa VM com o portal do Azure](qs-configure-portal-windows-vm.md) se precisar de assistência com a configuração da VM.<br><br>Certifique-se também de que o URI do pedido HTTP GET é formatado corretamente, especialmente o recurso que URI especificado na cadeia de consulta. Consulte o "pedido de exemplo" na secção anterior do REST para obter um exemplo, ou [que o suporte do Azure AD a autenticação dos serviços Azure](services-support-msi.md) para obter uma lista de serviços e os respetivos IDs de recurso.

## <a name="retry-guidance"></a>Diretrizes de repetição 

É recomendado para repetir se receber um 404, 429 ou código de erro 5xx (consulte [tratamento de erros](#error-handling) acima).

Limites de limitação aplicam-se ao número de chamadas feitas para o ponto de extremidade IMDS. Quando for excedido o limiar de limitação, o ponto final IMDS limita pedidos adicionais enquanto a limitação está em vigor. Durante este período, o ponto de extremidade IMDS irá devolver o código de estado HTTP 429 ("demasiados pedidos"), e os pedidos falharem. 

Para repetição, recomendamos a seguinte estratégia de: 

| **Estratégia de repetição** | **Definições** | **Valores** | **Como funciona** |
| --- | --- | --- | --- |
|ExponentialBackoff |Contagem de repetições<br />Término mín.<br />Término máx.<br />Término delta<br />Primeira repetição rápida |5<br />0 s<br />60 s<br />2 s<br />false |Tentativa 1 – atraso de 0 s<br />Tentativa 2 – atraso de ~2 s<br />Tentativa 3 – atraso de ~6 s<br />Tentativa 4 – atraso de ~14 s<br />Tentativa 5 – atraso de ~30 s |

## <a name="resource-ids-for-azure-services"></a>IDs de recurso para serviços do Azure

Ver [que o suporte do Azure AD a autenticação dos serviços Azure](services-support-msi.md) para obter uma lista de recursos que oferecem suporte ao AD do Azure e foram testados com identidades geridas para recursos do Azure e os respetivos IDs de recurso.


## <a name="next-steps"></a>Passos Seguintes

- Para ativar identidades geridas para recursos do Azure numa VM do Azure, consulte [configurar geridos identidades para recursos do Azure numa VM com o portal do Azure](qs-configure-portal-windows-vm.md).








