---
title: Utilize identidades geridas numa máquina virtual para adquirir o token de acesso - Azure AD
description: Instruções passo a passo e exemplos para a utilização de identidades geridas para recursos Azure em máquinas virtuais para adquirir um token de acesso OAuth.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4683a77b9467775fbe368e2017416e0fbff9718c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89266294"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Como utilizar identidades geridas para recursos Azure num VM Azure para adquirir um token de acesso 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação AZure AD, sem ter credenciais no seu código. 

Este artigo fornece vários exemplos de código e script para aquisição de token, bem como orientações sobre tópicos importantes como o manuseamento de validade de fichas e erros HTTP. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Se pretender utilizar os exemplos Azure PowerShell neste artigo, certifique-se de instalar a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps).


> [!IMPORTANT]
> - Todo o código/script de amostra neste artigo pressupõe que o cliente está a funcionar numa máquina virtual com identidades geridas para recursos Azure. Utilize a função de máquina virtual "Connect" no portal Azure, para ligar remotamente ao seu VM. Para obter detalhes sobre como permitir identidades geridas para recursos Azure num VM, consulte [identidades geridas configure para recursos Azure num VM utilizando o portal Azure](qs-configure-portal-windows-vm.md), ou um dos artigos variantes (utilizando PowerShell, CLI, um modelo ou um Azure SDK). 

> [!IMPORTANT]
> - A fronteira de segurança das identidades geridas para os recursos do Azure é o recurso em que está a ser usado. Todos os códigos/scripts em execução numa máquina virtual podem solicitar e recuperar fichas para quaisquer identidades geridas disponíveis no mesmo. 

## <a name="overview"></a>Descrição geral

Uma aplicação do cliente pode solicitar identidades geridas para [o token de acesso apenas a recursos](../develop/developer-glossary.md#access-token) Azure para aceder a um determinado recurso. O símbolo [baseia-se nas identidades geridas para o diretor do serviço de recursos Azure.](overview.md#managed-identity-types) Como tal, não é necessário que o cliente se registe para obter um token de acesso ao abrigo do seu próprio principal serviço. O token é adequado para ser utilizado como símbolo portador em [chamadas de serviço-a-serviço que requerem credenciais de cliente](../develop/v2-oauth2-client-creds-grant-flow.md).

| Ligação | Descrição |
| -------------- | -------------------- |
| [Obtenha um token usando HTTP](#get-a-token-using-http) | Detalhes do protocolo para identidades geridas para recursos Azure token ponto final |
| [Obtenha um token usando a biblioteca Microsoft.Azure.Services.AppAuthentication para .NET](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Exemplo da utilização da biblioteca Microsoft.Azure.Services.AppAuthentication a partir de um cliente .NET
| [Obtenha um token usando C #](#get-a-token-using-c) | Exemplo de utilização de identidades geridas para recursos Azure REST ponto final de um cliente C# |
| [Obter um símbolo usando Java](#get-a-token-using-java) | Exemplo de utilização de identidades geridas para recursos Azure REST ponto final de um cliente Java |
| [Obter um símbolo usando Go](#get-a-token-using-go) | Exemplo de utilização de identidades geridas para recursos Azure REST ponto final de um cliente Go |
| [Obtenha um símbolo usando Azure PowerShell](#get-a-token-using-azure-powershell) | Exemplo de utilização de identidades geridas para recursos Azure REST ponto final de um cliente PowerShell |
| [Obtenha um token usando CURL](#get-a-token-using-curl) | Exemplo de utilização de identidades geridas para recursos Azure REST ponto final de um cliente Bash/CURL |
| Manipulação de caching simbólico | Orientação para o manuseamento de fichas de acesso expiradas |
| [Processamento de erros](#error-handling) | Orientação para o manuseamento de erros HTTP devolvidos das identidades geridas para o ponto final simbólico dos recursos Azure |
| [IDs de recursos para serviços Azure](#resource-ids-for-azure-services) | Onde obter IDs de recursos para serviços Azure suportados |

## <a name="get-a-token-using-http"></a>Obtenha um token usando HTTP 

A interface fundamental para adquirir um token de acesso baseia-se no REST, tornando-o acessível a qualquer aplicação do cliente em execução no VM que possa fazer chamadas HTTP REST. Isto é semelhante ao modelo de programação AZure AD, exceto que o cliente usa um ponto final na máquina virtual (vs um ponto final AZure AD).

Pedido de amostra utilizando o ponto final do Serviço de Metadados de Instância Azure (IMDS) *(recomendado)*:

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Elemento | Descrição |
| ------- | ----------- |
| `GET` | O verbo HTTP, indicando que pretende obter dados do ponto final. Neste caso, um token de acesso OAuth. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | As identidades geridas para o ponto final dos recursos Azure para o Serviço de Metadados de Exemplo. |
| `api-version`  | Um parâmetro de cadeia de consulta, indicando a versão API para o ponto final do IMDS. Por favor, use a versão API `2018-02-01` ou maior. |
| `resource` | Um parâmetro de cadeia de consulta, indicando o ID URI da aplicação do recurso-alvo. Também aparece na `aud` reivindicação (audiência) do token emitido. Este exemplo solicita um símbolo para aceder ao Azure Resource Manager, que tem uma App ID URI de `https://management.azure.com/` . |
| `Metadata` | Um campo de cabeçalho de pedido HTTP, exigido por identidades geridas para recursos Azure como uma mitigação contra o ataque de Falsificação de Pedidos de Pedidos do Servidor (SSRF). Este valor deve ser definido como "verdadeiro", em todos os casos inferiores. |
| `object_id` | (Opcional) Um parâmetro de cadeia de consulta, indicando a object_id da identidade gerida para a quais gostaria de ser o símbolo. Necessário, se o seu VM tiver várias identidades geridas atribuídas pelo utilizador.|
| `client_id` | (Opcional) Um parâmetro de cadeia de consulta, indicando a client_id da identidade gerida para a quais gostaria de ser o símbolo. Necessário, se o seu VM tiver várias identidades geridas atribuídas pelo utilizador.|
| `mi_res_id` | (Opcional) Um parâmetro de cadeia de consulta, indicando o mi_res_id (ID de recurso Azure) da identidade gerida para a quais gostaria de ser o símbolo. Necessário, se o seu VM tiver várias identidades geridas atribuídas pelo utilizador. |

Pedido de amostra utilizando as identidades geridas para o ponto final de extensão VM dos recursos Azure *(previsto para a depreciação em janeiro de 2019)*:

```http
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elemento | Descrição |
| ------- | ----------- |
| `GET` | O verbo HTTP, indicando que pretende obter dados do ponto final. Neste caso, um token de acesso OAuth. | 
| `http://localhost:50342/oauth2/token` | As identidades geridas para o ponto final dos recursos Azure, onde 50342 é a porta padrão e é configurável. |
| `resource` | Um parâmetro de cadeia de consulta, indicando o ID URI da aplicação do recurso-alvo. Também aparece na `aud` reivindicação (audiência) do token emitido. Este exemplo solicita um símbolo para aceder ao Azure Resource Manager, que tem uma App ID URI de `https://management.azure.com/` . |
| `Metadata` | Um campo de cabeçalho de pedido HTTP, exigido por identidades geridas para recursos Azure como uma mitigação contra o ataque de Falsificação de Pedidos de Pedidos do Servidor (SSRF). Este valor deve ser definido como "verdadeiro", em todos os casos inferiores.|
| `object_id` | (Opcional) Um parâmetro de cadeia de consulta, indicando a object_id da identidade gerida para a quais gostaria de ser o símbolo. Necessário, se o seu VM tiver várias identidades geridas atribuídas pelo utilizador.|
| `client_id` | (Opcional) Um parâmetro de cadeia de consulta, indicando a client_id da identidade gerida para a quais gostaria de ser o símbolo. Necessário, se o seu VM tiver várias identidades geridas atribuídas pelo utilizador.|

Resposta da amostra:

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
| `access_token` | O sinal de acesso solicitado. Ao chamar uma API REST segura, o token está incorporado no campo do cabeçalho de `Authorization` pedido como um símbolo "portador", permitindo à API autenticar o chamador. | 
| `refresh_token` | Não usado por identidades geridas para recursos Azure. |
| `expires_in` | O número de segundos em que o token de acesso continua a ser válido, antes de expirar, a partir do momento da emissão. O tempo de emissão pode ser encontrado na reivindicação do `iat` símbolo. |
| `expires_on` | O tempo de tempo quando a ficha de acesso expira. A data é representada como o número de segundos de "1970-01-01T0:0Z UTC" (corresponde à reivindicação do `exp` token). |
| `not_before` | O período de tempo quando o token de acesso entra em vigor, e pode ser aceite. A data é representada como o número de segundos de "1970-01-01T0:0Z UTC" (corresponde à reivindicação do `nbf` token). |
| `resource` | O recurso para o token de acesso foi solicitado, que corresponde ao `resource` parâmetro de cadeia de consulta do pedido. |
| `token_type` | O tipo de token, que é um token de acesso "Portador", o que significa que o recurso pode dar acesso ao portador deste token. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Obtenha um token usando a biblioteca Microsoft.Azure.Services.AppAuthentication para .NET

Para aplicações e funções .NET, a forma mais simples de trabalhar com identidades geridas para recursos Azure é através do pacote Microsoft.Azure.Services.AppAuthentication. Esta biblioteca também lhe permitirá testar o seu código localmente na sua máquina de desenvolvimento, utilizando a sua conta de utilizador a partir do Visual Studio, do [Azure CLI](/cli/azure?view=azure-cli-latest)ou da Autenticação Integrada do Diretório Ativo. Para obter mais informações sobre as opções de desenvolvimento local com esta biblioteca, consulte a [referência Microsoft.Azure.Services.AppAuthentication](../../key-vault/general/service-to-service-authentication.md). Esta secção mostra-lhe como começar com a biblioteca no seu código.

1. Adicione referências aos pacotes [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet à sua aplicação.

2.  Adicione o seguinte código à sua aplicação:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```
    
Para saber mais sobre o Microsoft.Azure.Services.AppAuthentication e as operações que expõe, consulte a [referência Microsoft.Azure.Services.AppAuthentication](../../key-vault/general/service-to-service-authentication.md) e o [Serviço de Aplicações e KeyVault com identidades geridas para recursos Azure .AMOSTRA NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

## <a name="get-a-token-using-c"></a>Obtenha um token usando C #

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

## <a name="get-a-token-using-java"></a>Obter um símbolo usando Java

Utilize esta [biblioteca JSON](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) para recuperar um símbolo usando Java.

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

## <a name="get-a-token-using-go"></a>Obter um símbolo usando Go

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

## <a name="get-a-token-using-azure-powershell"></a>Obtenha um símbolo usando Azure PowerShell

O exemplo a seguir demonstra como utilizar as identidades geridas para os recursos Azure REST ponto final de um cliente PowerShell para:

1. Adquira um símbolo de acesso.
2. Utilize o token de acesso para ligar para a Azure Resource Manager REST API e obter informações sobre o VM. Não se esqueça de substituir o seu ID de subscrição, nome de grupo de recursos e nome de máquina virtual por `<SUBSCRIPTION-ID>` `<RESOURCE-GROUP>` , `<VM-NAME>` e, respectivamente.

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

## <a name="get-a-token-using-curl"></a>Obtenha um token usando CURL

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Exemplo sobre como analisar o token de acesso da resposta:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Caching token

Embora as identidades geridas para o subsistema de recursos Azure que estão a ser utilizados (IMDS/identidades geridas para extensão VM recursos Azure) faça fichas de cache, recomendamos também implementar o cache simbólico no seu código. Como resultado, deve preparar-se para cenários em que o recurso indica que o token está expirado. 

Chamadas no fio para o resultado da AD Azure apenas quando:
- cache miss ocorre devido a nenhum símbolo nas identidades geridas para cache subsistema de recursos Azure
- o símbolo em cache é expirado

## <a name="error-handling"></a>Processamento de erros

As identidades geridas para o ponto final dos recursos Azure assinalam erros através do campo de código de estado do cabeçalho da mensagem de resposta HTTP, como erros de 4xx ou 5xx:

| Código de Estado | Razão de erro | Como lidar |
| ----------- | ------------ | ------------- |
| 404 Não encontrado. | O ponto final do IMDS está a atualizar-se. | Re-tentar com o Recuo Exponencial. Consulte a orientação abaixo. |
| 429 Pedidos a mais. |  Limite de aceleração IMDS atingido. | Re-tentar com o Recuo Exponencial. Consulte a orientação abaixo. |
| Erro 4xx a pedido. | Um ou mais dos parâmetros do pedido estavam incorretos. | Não redaça.  Examine os detalhes do erro para obter mais informações.  Erros 4xx são erros de tempo de conceção.|
| Erro transitório 5xx do serviço. | As identidades geridas para o sub-sistema de recursos Azure ou para o Azure Ative Directory retornaram um erro transitório. | É seguro voltar a tentar depois de esperar pelo menos 1 segundo.  Se voltar a tentar com demasiada rapidez ou demasiadas vezes, o IMDS e/ou a AD Azure podem devolver um erro de limite de taxa (429).|
| tempo limite | O ponto final do IMDS está a atualizar-se. | Re-tentar com o Recuo Exponencial. Consulte a orientação abaixo. |

Se ocorrer um erro, o respetivo organismo de resposta HTTP contém JSON com os detalhes de erro:

| Elemento | Descrição |
| ------- | ----------- |
| erro   | Identificador de erro. |
| error_description | Descrição verbosa do erro. **As descrições de erros podem ser alteradas a qualquer momento. Não escreva código que se baseie em valores na descrição do erro.**|

### <a name="http-response-reference"></a>Referência de resposta HTTP

Esta secção documenta as possíveis respostas de erro. Um estado de "200 OK" é uma resposta bem sucedida, e o token de acesso está contido no corpo de resposta JSON, no elemento access_token.

| Código de estado | Erro | Descrição do Erro | Solução |
| ----------- | ----- | ----------------- | -------- |
| 400 Pedido Incorreto | invalid_resource | AADSTS50001: O pedido nomeado *\<URI\>* não foi encontrado no arrendatário denominado *\<TENANT-ID\>* . Este erro poderá ocorrer se a aplicação não tiver sido instalada pelo administrador do inquilino ou não tiver sido permitida por qualquer utilizador do inquilino. Pode ter enviado o seu pedido de autenticação ao inquilino errado. | (apenas Linux) |
| 400 Pedido Incorreto | bad_request_102 | Cabeçalho de metadados necessário não especificado | Ou o `Metadata` campo de cabeçalho de pedido está ausente do seu pedido, ou é formatado incorretamente. O valor deve ser especificado como `true` , em todos os casos inferiores. Consulte o "pedido de amostra" na secção REST anterior, por exemplo.|
| 401 Não Autorizado | unknown_source | Fonte Desconhecida *\<URI\>* | Verifique se o seu pedido HTTP GET URI está formatado corretamente. A `scheme:host/resource-path` parte deve ser especificada como `http://localhost:50342/oauth2/token` . Consulte o "pedido de amostra" na secção REST anterior, por exemplo.|
|           | invalid_request | O pedido está em falta de um parâmetro necessário, inclui um valor de parâmetro inválido, inclui um parâmetro mais de uma vez, ou está mal formado. |  |
|           | unauthorized_client | O cliente não está autorizado a solicitar um token de acesso usando este método. | Causado por um pedido que não usou o loopback local para ligar para a extensão, ou em um VM que não tem identidades geridas para recursos Azure configurados corretamente. Consulte [identidades geridas para os recursos Azure num VM utilizando o portal Azure](qs-configure-portal-windows-vm.md) se precisar de assistência com a configuração VM. |
|           | access_denied | O proprietário de recursos ou servidor de autorização negou o pedido. |  |
|           | unsupported_response_type | O servidor de autorização não suporta obter um token de acesso utilizando este método. |  |
|           | invalid_scope | O âmbito solicitado é inválido, desconhecido ou mal formado. |  |
| Erro interno do servidor 500 | desconhecido | Falhou em recuperar o símbolo do diretório Ativo. Para mais detalhes, consulte os registos *\<file path\>* | Verifique se as identidades geridas para os recursos Azure foram ativadas no VM. Consulte [identidades geridas para os recursos Azure num VM utilizando o portal Azure](qs-configure-portal-windows-vm.md) se precisar de assistência com a configuração VM.<br><br>Verifique também se o seu pedido HTTP GET URI está formatado corretamente, particularmente o recurso URI especificado na cadeia de consulta. Consulte o "pedido de amostra" na secção REST anterior, por exemplo, ou [os serviços Azure que suportam a autenticação AZure AD](./services-support-managed-identities.md) para uma lista de serviços e respetivos IDs de recursos.

## <a name="retry-guidance"></a>Orientação de retíria 

Recomenda-se voltar a tentar se receber um código de erro 404, 429 ou 5xx (ver [tratamento de erro](#error-handling) acima).

Os limites de aceleração aplicam-se ao número de chamadas efetuadas para o ponto final do IMDS. Quando o limiar de estrangulamento é ultrapassado, o ponto final do IMDS limita quaisquer outros pedidos enquanto o acelerador está em vigor. Durante este período, o ponto final do IMDS devolverá o código de estado HTTP 429 ("Demasiados pedidos"), e os pedidos falham. 

Para reefaça, recomendamos a seguinte estratégia: 

| **Estratégia de repetição** | **Definições** | **Valores** | **Como funciona** |
| --- | --- | --- | --- |
|ExponentialBackoff |Contagem de repetições<br />Término mín.<br />Término máx.<br />Término delta<br />Primeira repetição rápida |5<br />0 s<br />60 s<br />2 s<br />false |Tentativa 1 – atraso de 0 s<br />Tentativa 2 – atraso de ~2 s<br />Tentativa 3 – atraso de ~6 s<br />Tentativa 4 – atraso de ~14 s<br />Tentativa 5 – atraso de ~30 s |

## <a name="resource-ids-for-azure-services"></a>IDs de recursos para serviços Azure

Consulte [os serviços Azure que suportam a autenticação Azure AD](./services-support-managed-identities.md) para uma lista de recursos que suportam a Azure AD e foram testados com identidades geridas para recursos Azure, e respetivas identidades de recursos.


## <a name="next-steps"></a>Passos seguintes

- Para permitir identidades geridas para recursos Azure num Azure VM, consulte [identidades geridas configure para recursos Azure num VM utilizando o portal Azure](qs-configure-portal-windows-vm.md).