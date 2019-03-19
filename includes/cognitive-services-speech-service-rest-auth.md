---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: 8e8f7c18c50085d6baf17cc0c9103b2c4bcacbaf
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2019
ms.locfileid: "58051881"
---
## <a name="authentication"></a>Authentication

Cada pedido requer um cabeçalho de autorização. Esta tabela mostra quais cabeçalhos são suportados para cada serviço:

| Cabeçalhos de autorização suportados | Conversão de voz em texto | Conversão de texto em voz |
|------------------------|----------------|----------------|
| OCP-Apim-Subscription-Key | Sim | Não |
| Autorização: Portador | Sim | Sim |

Ao utilizar o `Ocp-Apim-Subscription-Key` cabeçalho, só é necessário para fornecer a chave de subscrição. Por exemplo:

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Ao utilizar o `Authorization: Bearer` cabeçalho, for necessário para fazer um pedido para o `issueToken` ponto final. Neste pedido, trocar a chave de subscrição para um token de acesso que é válido durante 10 minutos. As próximas seções, aprenderá como obter um token, utilizam um token e um token de atualização.


### <a name="how-to-get-an-access-token"></a>Como obter um token de acesso

Para obter um token de acesso, terá de fazer um pedido para o `issueToken` ponto final a utilizar o `Ocp-Apim-Subscription-Key` e a chave de subscrição.

Essas regiões e pontos de extremidade são suportados:

[!INCLUDE [](./cognitive-services-speech-service-endpoints-token-service.md)]

Utilize estes exemplos para criar o seu pedido de token de acesso.

#### <a name="http-sample"></a>Exemplo HTTP

Neste exemplo é uma solicitação HTTP simples para obter um token. Substitua `YOUR_SUBSCRIPTION_KEY` com a sua chave de subscrição do serviço de voz. Se a sua subscrição não está na região E.U.A. oeste, substitua o `Host` cabeçalho com o nome de anfitrião de sua região.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

O corpo da resposta contém o token de acesso no formato JSON Web Token (JWT).

#### <a name="powershell-sample"></a>Exemplo do PowerShell

Neste exemplo é um script do PowerShell simple para obter um token de acesso. Substitua `YOUR_SUBSCRIPTION_KEY` com a sua chave de subscrição do serviço de voz. Certifique-se utilizar o ponto final correto para a região que corresponde à sua subscrição. Neste exemplo está definido para E.U.A. oeste.

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>exemplo de cURL

cURL é uma ferramenta da linha de comandos disponível no Linux (e no subsistema Windows para Linux). Este comando cURL ilustra como obter um token de acesso. Substitua `YOUR_SUBSCRIPTION_KEY` com a sua chave de subscrição do serviço de voz. Certifique-se utilizar o ponto final correto para a região que corresponde à sua subscrição. Neste exemplo está definido para E.U.A. oeste.

```cli
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>Exemplo C#

Isso C# classe ilustra como obter um token de acesso. Passe a chave de subscrição do serviço de voz ao instanciar a classe. Se a sua subscrição não está na região E.U.A. oeste, altere o valor de `FetchTokenUri` de acordo com a região para a sua subscrição.

```cs
/*
    * This class demonstrates how to get a valid access token.
    */
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

#### <a name="python-sample"></a>Exemplo de Python

```python
# Request module must be installed.
# Run pip install requests if necessary.
import requests

subscription_key = 'REPLACE_WITH_YOUR_KEY'

def get_token(subscription_key):
    fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
    headers = {
        'Ocp-Apim-Subscription-Key': subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    access_token = str(response.text)
    print(access_token)
```

### <a name="how-to-use-an-access-token"></a>Como utilizar um token de acesso

O token de acesso deve ser enviado para o serviço como o `Authorization: Bearer <TOKEN>` cabeçalho. Cada token de acesso é válido durante 10 minutos. Pode obter um novo token em qualquer altura, no entanto, para minimizar o tráfego de rede e a latência, recomendamos que utilize o mesmo token para nove minutos.

Este é um pedido HTTP de exemplo para a API REST do texto para voz:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
