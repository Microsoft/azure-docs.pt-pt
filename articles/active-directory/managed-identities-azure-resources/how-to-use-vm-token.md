---
title: Utilize identidades geridas numa máquina virtual para adquirir acesso a ficha - Azure AD
description: Instruções passo a passo e exemplos para usar identidades geridas para os recursos Do Azure numa máquina virtual para adquirir um símbolo de acesso OAuth.
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
ms.openlocfilehash: a58103bad3914bd0c0c6e70f8e3d2882271e1070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049200"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Como utilizar identidades geridas para recursos Azure num VM Azure para adquirir um sinal de acesso 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Identidades geridas para recursos Azure fornecem serviços Azure com uma identidade gerida automaticamente no Diretório Ativo Azure. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação DaD Azure, sem ter credenciais no seu código. 

Este artigo fornece vários exemplos de código e script para aquisição simbólica, bem como orientação sobre temas importantes como manipulação de expiração de token e erros HTTP. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Se planeia utilizar os exemplos do Azure PowerShell neste artigo, certifique-se de instalar a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps).


> [!IMPORTANT]
> - Todo o código/script da amostra neste artigo pressupõe que o cliente está a funcionar numa máquina virtual com identidades geridas para os recursos do Azure. Utilize a função de máquina virtual "Connect" no portal Azure, para ligar remotamente ao seu VM. Para mais detalhes sobre a possibilidade de permitir identidades geridas para os recursos Azure num VM, consulte [as identidades geridas pela Configure para os recursos Azure num VM utilizando o portal Azure](qs-configure-portal-windows-vm.md), ou um dos artigos variantes (utilizando PowerShell, CLI, um modelo ou um Azure SDK). 

> [!IMPORTANT]
> - O limite de segurança das identidades geridas para os recursos do Azure é o recurso em que está a ser usado. Todos os códigos/scripts que estão a funcionar numa máquina virtual podem solicitar e recuperar fichas para quaisquer identidades geridas disponíveis nele. 

## <a name="overview"></a>Descrição geral

Uma aplicação de cliente pode solicitar identidades geridas para o [acesso apenas](../develop/developer-glossary.md#access-token) a aplicações de recursos Azure para aceder a um determinado recurso. O símbolo [baseia-se nas identidades geridas para](overview.md#how-does-the-managed-identities-for-azure-resources-work)o principal de serviço de recursos azure. Como tal, não há necessidade de o cliente se registar para obter um sinal de acesso ao abrigo do seu próprio serviço principal. O símbolo é adequado para ser usado como um símbolo portador em [chamadas de serviço a serviço que requerem credenciais](../develop/v2-oauth2-client-creds-grant-flow.md)de cliente .

|  |  |
| -------------- | -------------------- |
| [Obtenha um símbolo usando HTTP](#get-a-token-using-http) | Detalhes do protocolo para identidades geridas para o ponto final simbólico dos recursos do Azure |
| [Obtenha um símbolo utilizando a biblioteca Microsoft.Azure.Services.AppAuthentication para .NET](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Exemplo de utilização da biblioteca Microsoft.Azure.Services.AppAuthentication de um cliente .NET
| [Obter um símbolo usando C #](#get-a-token-using-c) | Exemplo de utilização de identidades geridas para os recursos Azure REST point de um cliente C# |
| [Obter um símbolo usando Java](#get-a-token-using-java) | Exemplo de utilização de identidades geridas para os recursos Azure REST point de um cliente Java |
| [Obter um símbolo usando Go](#get-a-token-using-go) | Exemplo de utilização de identidades geridas para os recursos Azure REST point de um cliente Go |
| [Obtenha um símbolo usando azure PowerShell](#get-a-token-using-azure-powershell) | Exemplo de utilização de identidades geridas para os recursos Azure REST point de um cliente PowerShell |
| [Obtenha um símbolo usando curl](#get-a-token-using-curl) | Exemplo de utilização de identidades geridas para os recursos Azure REST point de um cliente Bash/CURL |
| Manuseamento de caching de token | Orientação para manusear fichas de acesso expiradas |
| [Processamento de erros](#error-handling) | Orientação para lidar com erros HTTP devolvidos das identidades geridas para o ponto final simbólico dos recursos do Azure |
| [IDs de recursos para serviços Azure](#resource-ids-for-azure-services) | Onde obter iDs de recursos para serviços Azure suportados |

## <a name="get-a-token-using-http"></a>Obtenha um símbolo usando HTTP 

A interface fundamental para a aquisição de um token de acesso baseia-se no REST, tornando-o acessível a qualquer aplicação do cliente em execução no VM que possa fazer chamadas HTTP REST. Isto é semelhante ao modelo de programação da AD Azure, exceto que o cliente utiliza um ponto final na máquina virtual (vs um ponto final da AD Azure).

Pedido de amostra geminação utilizando o ponto final do Serviço de Metadados de Instância Seletiva (IMDS) *(recomendado):*

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Elemento | Descrição |
| ------- | ----------- |
| `GET` | O verbo HTTP, indicando que pretende recuperar dados do ponto final. Neste caso, um sinal de acesso da OAuth. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | As identidades geridas para o ponto final dos recursos do Azure para o Serviço de Metadados de Exemplo. |
| `api-version`  | Um parâmetro de corda de consulta, indicando a versão API para o ponto final do IMDS. Por favor, `2018-02-01` utilize a versão API ou maior. |
| `resource` | Um parâmetro de corda de consulta, indicando o ID da aplicação URI do recurso alvo. Também aparece na `aud` reivindicação (audiência) do símbolo emitido. Este exemplo solicita um sinal de acesso ao Azure Resource `https://management.azure.com/`Manager, que tem um ID app URI de . |
| `Metadata` | Um campo de cabeçalho de pedido HTTP, exigido por identidades geridas para os recursos Do Azure como uma mitigação contra o ataque de Forgery de Pedido lateral do Servidor (SSRF). Este valor deve ser definido como "verdadeiro", em todos os casos inferiores. |
| `object_id` | (Opcional) Um parâmetro de corda de consulta, indicando o object_id da identidade gerida para a que gostaria do símbolo. Necessário, se o seu VM tiver várias identidades geridas atribuídas ao utilizador.|
| `client_id` | (Opcional) Um parâmetro de corda de consulta, indicando o client_id da identidade gerida para a que gostaria do símbolo. Necessário, se o seu VM tiver várias identidades geridas atribuídas ao utilizador.|
| `mi_res_id` | (Opcional) Um parâmetro de corda de consulta, indicando o mi_res_id (Azure Resource ID) da identidade gerida para a que gostaria do símbolo. Necessário, se o seu VM tiver várias identidades geridas atribuídas ao utilizador. |

Pedido de amostra utilizando as identidades geridas para os recursos Azure VM Extension Endpoint *(previsto para depreciação em janeiro de 2019)*

```http
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elemento | Descrição |
| ------- | ----------- |
| `GET` | O verbo HTTP, indicando que pretende recuperar dados do ponto final. Neste caso, um sinal de acesso da OAuth. | 
| `http://localhost:50342/oauth2/token` | As identidades geridas para o ponto final dos recursos Azure, onde 50342 é a porta padrão e é configurável. |
| `resource` | Um parâmetro de corda de consulta, indicando o ID da aplicação URI do recurso alvo. Também aparece na `aud` reivindicação (audiência) do símbolo emitido. Este exemplo solicita um sinal de acesso ao Azure Resource `https://management.azure.com/`Manager, que tem um ID app URI de . |
| `Metadata` | Um campo de cabeçalho de pedido HTTP, exigido por identidades geridas para os recursos Do Azure como uma mitigação contra o ataque de Forgery de Pedido lateral do Servidor (SSRF). Este valor deve ser definido como "verdadeiro", em todos os casos inferiores.|
| `object_id` | (Opcional) Um parâmetro de corda de consulta, indicando o object_id da identidade gerida para a que gostaria do símbolo. Necessário, se o seu VM tiver várias identidades geridas atribuídas ao utilizador.|
| `client_id` | (Opcional) Um parâmetro de corda de consulta, indicando o client_id da identidade gerida para a que gostaria do símbolo. Necessário, se o seu VM tiver várias identidades geridas atribuídas ao utilizador.|

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
| `access_token` | O sinal de acesso solicitado. Ao chamar uma API DE REPOUSO segura, `Authorization` o símbolo está incorporado no campo de cabeçalho de pedido como um símbolo "portador", permitindo que a API autenticar o chamador. | 
| `refresh_token` | Não usado por identidades geridas para recursos Azure. |
| `expires_in` | O número de segundos que o token de acesso continua a ser válido, antes de expirar, a partir do momento da emissão. O tempo de emissão pode ser encontrado `iat` na reivindicação do símbolo. |
| `expires_on` | O tempo de espera quando o sinal de acesso expira. A data é representada como o número de segundos de "1970-01-01T0:0:0Z UTC" (corresponde à reivindicação do `exp` token). |
| `not_before` | O tempo de espera quando o token de acesso entra em vigor, e pode ser aceite. A data é representada como o número de segundos de "1970-01-01T0:0:0Z UTC" (corresponde à reivindicação do `nbf` token). |
| `resource` | O recurso para o sinal de acesso `resource` foi solicitado, que corresponde ao parâmetro de corda de consulta do pedido. |
| `token_type` | O tipo de ficha, que é um símbolo de acesso "Portador", o que significa que o recurso pode dar acesso ao portador deste símbolo. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Obtenha um símbolo utilizando a biblioteca Microsoft.Azure.Services.AppAuthentication para .NET

Para aplicações e funções .NET, a forma mais simples de trabalhar com identidades geridas para os recursos Azure é através do pacote Microsoft.Azure.Services.Services.AppAuthentication. Esta biblioteca também lhe permitirá testar o seu código localmente na sua máquina de desenvolvimento, utilizando a sua conta de utilizador do Visual Studio, do [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)ou da Autenticação Integrada do Diretório Ativo. Para mais informações sobre as opções de desenvolvimento local com esta biblioteca, consulte a [referência Microsoft.Azure.Services.AppAuthentication](/azure/key-vault/service-to-service-authentication). Esta secção mostra-lhe como começar com a biblioteca no seu código.

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
    
Para saber mais sobre microsoft.Azure.Services.AppAuthentication e as operações que expõe, consulte a [referência Microsoft.Azure.Services.AppAuthentication](/azure/key-vault/service-to-service-authentication) e o [App Service e KeyVault com identidades geridas para os recursos Azure .NET sample](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

## <a name="get-a-token-using-c"></a>Obter um símbolo usando C #

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

Use esta [biblioteca JSON](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) para recuperar um símbolo usando Java.

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

## <a name="get-a-token-using-azure-powershell"></a>Obtenha um símbolo usando azure PowerShell

O exemplo que se segue demonstra como usar as identidades geridas para os recursos Azure REST point de um cliente PowerShell para:

1. Adquira um sinal de acesso.
2. Utilize o sinal de acesso para ligar para um Gestor de Recursos Azure REST API e obter informações sobre o VM. Certifique-se de substituir o seu ID de subscrição, nome de grupo de recursos e nome de máquina virtual por, `<SUBSCRIPTION-ID>` `<RESOURCE-GROUP>`e `<VM-NAME>`, respectivamente.

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Exemplo sobre como analisar o símbolo de acesso da resposta:
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

## <a name="get-a-token-using-curl"></a>Obtenha um símbolo usando curl

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Exemplo sobre como analisar o símbolo de acesso da resposta:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Caching de token

Embora as identidades geridas para o subsistema de recursos Azure que estão a ser utilizadas (identidades IMDS/geridas para extensão VM de recursos Azure) faça tokens cache, também recomendamos implementar cacheching no seu código. Como resultado, deve preparar-se para cenários em que o recurso indique que o token está expirado. 

As chamadas no cabo para a AD Azure só resultam quando:
- cache miss ocorre devido a nenhum símbolo nas identidades geridas para o cache do subsistema de recursos Azure
- o símbolo cached é expirado

## <a name="error-handling"></a>Processamento de erros

As identidades geridas para os recursos do Azure assinalam erros através do campo de código de estado do cabeçalho de mensagem de resposta HTTP, como erros 4xx ou 5xx:

| Código de Estado | Razão do erro | Como lidar |
| ----------- | ------------ | ------------- |
| 404 não encontrados. | O ponto final do IMDS está a atualizar-se. | Retry com Backoff Expontenteial. Consulte a orientação abaixo. |
| 429 pedidos a mais. |  Limite de aceleração IMDS atingido. | Retry com Backoff Exponencial. Consulte a orientação abaixo. |
| Erro 4xx a pedido. | Um ou mais dos parâmetros de pedido estavam incorretos. | Não tente de novo.  Examine os detalhes do erro para mais informações.  Erros 4xx são erros de tempo de design.|
| Erro transitório 5xx do serviço. | As identidades geridas para o subsistema de recursos Azure ou Azure Ative Directory devolveu um erro transitório. | É seguro voltar a tentar depois de esperar pelo menos 1 segundo.  Se voltar a tentar com demasiada rapidez ou demasiadas vezes, o IMDS e/ou o Azure AD podem devolver um erro de limite de taxa (429).|
| tempo limite | O ponto final do IMDS está a atualizar-se. | Retry com Backoff Expontenteial. Consulte a orientação abaixo. |

Se ocorrer um erro, o corpo de resposta http correspondente contém JSON com os detalhes do erro:

| Elemento | Descrição |
| ------- | ----------- |
| erro   | Identificador de erros. |
| error_description | Verbose descrição de erro. **As descrições de erros podem mudar a qualquer momento. Não escreva código que se responsacom base em valores na descrição do erro.**|

### <a name="http-response-reference"></a>Referência de resposta HTTP

Esta secção documenta as possíveis respostas de erro. Um estado de "200 OK" é uma resposta bem sucedida, e o sinal de acesso está contido no corpo de resposta JSON, no elemento access_token.

| Código de estado | Erro | Descrição do Erro | Solução |
| ----------- | ----- | ----------------- | -------- |
| 400 Mau Pedido | invalid_resource | AADSTS50001: O * \<pedido\> * de nome URI não foi encontrado no inquilino denominado * \<TENANT-ID\>*. Isto pode acontecer se o pedido não tiver sido instalado pelo administrador do inquilino ou consentir com qualquer utilizador do arrendatário. Pode ter enviado o seu pedido de autenticação ao inquilino errado. | (apenas Linux) |
| 400 Mau Pedido | bad_request_102 | Cabeçalho de metadados exigido não especificado | Ou `Metadata` o campo de cabeçalho do pedido está ausente do seu pedido, ou está formatado incorretamente. O valor deve ser `true`especificado como, em todos os casos inferiores. Consulte o "Pedido de amostra" na secção REST anterior, por exemplo.|
| 401 Não autorizado | unknown_source | Fonte Desconhecida * \<URI\>* | Verifique se o seu pedido HTTP GET URI está formatado corretamente. A `scheme:host/resource-path` parte deve ser `http://localhost:50342/oauth2/token`especificada como . Consulte o "Pedido de amostra" na secção REST anterior, por exemplo.|
|           | invalid_request | Falta um parâmetro necessário, inclui um valor de parâmetro inválido, inclui um parâmetro superior a uma vez, ou está mal formado. |  |
|           | unauthorized_client | O cliente não está autorizado a solicitar um sinal de acesso utilizando este método. | Causado por um pedido que não usou o loopback local para chamar a extensão, ou num VM que não tenha gerido identidades para recursos Azure configurados corretamente. Consulte [as identidades geridas pela Configure para os recursos Azure num VM utilizando o portal Azure](qs-configure-portal-windows-vm.md) se precisar de assistência com a configuração VM. |
|           | access_denied | O proprietário do recurso ou servidor de autorização negou o pedido. |  |
|           | unsupported_response_type | O servidor de autorização não suporta obter um sinal de acesso utilizando este método. |  |
|           | invalid_scope | O âmbito solicitado é inválido, desconhecido ou mal formado. |  |
| Erro do servidor interno 500 | desconhecido | Não conseguiu recuperar o símbolo do diretório Ative. Para mais detalhes consulte registos no * \<caminho do ficheiro\>* | Verifique se as identidades geridas para os recursos Azure foram ativadas no VM. Consulte [as identidades geridas pela Configure para os recursos Azure num VM utilizando o portal Azure](qs-configure-portal-windows-vm.md) se precisar de assistência com a configuração VM.<br><br>Verifique também se o seu pedido HTTP GET URI está formatado corretamente, em particular o recurso URI especificado na cadeia de consulta. Consulte o "pedido de amostra" na secção REST anterior, por exemplo, ou [os serviços Azure que suportam a autenticação Azure AD](services-support-msi.md) para uma lista de serviços e respetivos IDs de recursos.

## <a name="retry-guidance"></a>Orientação de retry 

Recomenda-se que se retente se receber um código de erro 404, 429 ou 5xx (ver [error handling](#error-handling) acima).

Os limites de estrangulamento aplicam-se ao número de chamadas efetuadas ao ponto final do IMDS. Quando o limiar de estrangulamento é ultrapassado, o ponto final do IMDS limita quaisquer pedidos adicionais enquanto o acelerador estiver em vigor. Durante este período, o ponto final do IMDS devolverá o código de estado HTTP 429 ("Pedidos a mais"), e os pedidos falham. 

Para voltar a tentar, recomendamos a seguinte estratégia: 

| **Estratégia de repetição** | **Definições** | **Valores** | **Como funciona** |
| --- | --- | --- | --- |
|ExponentialBackoff |Contagem de repetições<br />Término mín.<br />Término máx.<br />Término delta<br />Primeira repetição rápida |5<br />0 s<br />60 s<br />2 s<br />false |Tentativa 1 – atraso de 0 s<br />Tentativa 2 – atraso de ~2 s<br />Tentativa 3 – atraso de ~6 s<br />Tentativa 4 – atraso de ~14 s<br />Tentativa 5 – atraso de ~30 s |

## <a name="resource-ids-for-azure-services"></a>IDs de recursos para serviços Azure

Consulte [os serviços Azure que suportam a autenticação Azure AD](services-support-msi.md) para uma lista de recursos que suportam a Azure AD e foram testados com identidades geridas para recursos Azure, e seus respetivos IDs de recursos.


## <a name="next-steps"></a>Passos seguintes

- Para permitir identidades geridas para os recursos Azure num Azure VM, consulte [as identidades geridas pela Configure para os recursos Azure num VM utilizando o portal Azure.](qs-configure-portal-windows-vm.md)








