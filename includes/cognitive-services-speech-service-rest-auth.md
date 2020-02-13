---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: 020055c1629a66ec1aa82beb050501803b2a0f18
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168325"
---
## <a name="authentication"></a>Autenticação

Cada pedido requer um cabeçalho de autorização. Esta tabela mostra quais cabeçalhos são suportados para cada serviço:

| Cabeçalhos de autorização suportados | Conversão de voz em texto | Conversão de texto em voz |
|------------------------|----------------|----------------|
| OCP-Apim-Subscription-Key | Sim | Não |
| Autorização: portador | Sim | Sim |

Ao utilizar o cabeçalho `Ocp-Apim-Subscription-Key`, só é necessário fornecer a sua chave de subscrição. Por exemplo:

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Ao utilizar o cabeçalho `Authorization: Bearer`, é-lhe exigido que faça um pedido para o ponto final `issueToken`. Neste pedido, trocar a chave de subscrição para um token de acesso que é válido durante 10 minutos. Nas próximas secções, aprenderás a obter um símbolo e a usar um símbolo.

### <a name="how-to-get-an-access-token"></a>Como obter um token de acesso

Para obter um sinal de acesso, terá de fazer um pedido para o ponto final `issueToken` utilizando o `Ocp-Apim-Subscription-Key` e a sua chave de subscrição.

O ponto final `issueToken` tem este formato:

```
https://<REGION_IDENTIFIER>.api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Substitua `<REGION_IDENTIFIER>` pelo identificador que corresponde à região da sua subscrição a partir desta tabela:

[!INCLUDE [](cognitive-services-speech-service-region-identifier.md)]

Utilize estes exemplos para criar o seu pedido de token de acesso.

#### <a name="http-sample"></a>Exemplo HTTP

Neste exemplo é uma solicitação HTTP simples para obter um token. Substitua `YOUR_SUBSCRIPTION_KEY` com a sua chave de subscrição do Serviço de Fala. Se a sua subscrição não estiver na região oeste dos EUA, substitua o cabeçalho `Host` com o nome de anfitrião da sua região.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

O corpo da resposta contém o símbolo de acesso no formato JSON Web Token (JWT).

#### <a name="powershell-sample"></a>Exemplo do PowerShell

Neste exemplo é um script do PowerShell simple para obter um token de acesso. Substitua `YOUR_SUBSCRIPTION_KEY` com a sua chave de subscrição do Serviço de Fala. Certifique-se utilizar o ponto final correto para a região que corresponde à sua subscrição. Neste exemplo está definido para E.U.A. oeste.

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

cURL é uma ferramenta da linha de comandos disponível no Linux (e no subsistema Windows para Linux). Este comando cURL ilustra como obter um token de acesso. Substitua `YOUR_SUBSCRIPTION_KEY` com a sua chave de subscrição do Serviço de Fala. Certifique-se utilizar o ponto final correto para a região que corresponde à sua subscrição. Neste exemplo está definido para E.U.A. oeste.

```cli
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>Exemplo C#

Isso C# classe ilustra como obter um token de acesso. Passe a chave de subscrição do serviço de voz ao instanciar a classe. Se a sua subscrição não estiver na região dos EUA Ocidentais, altere o valor da `FetchTokenUri` para corresponder à região para a sua subscrição.

```cs
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

#### <a name="python-sample"></a>Amostra de python

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

O sinal de acesso deve ser enviado para o serviço como cabeçalho `Authorization: Bearer <TOKEN>`. Cada token de acesso é válido durante 10 minutos. Pode obter um novo token em qualquer altura, no entanto, para minimizar o tráfego de rede e a latência, recomendamos que utilize o mesmo token para nove minutos.

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
