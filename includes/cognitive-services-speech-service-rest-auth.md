---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: dc5e251fee00ee22edb2261c1abd8404714834ba
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78669243"
---
## <a name="authentication"></a>Autenticação

Cada pedido requer um cabeçalho de autorização. Esta tabela ilustra quais os cabeçalhos suportados para cada serviço:

| Cabeçalhos de autorização suportados | Conversão de voz em texto | Conversão de texto em voz |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | Sim | Não |
| Autorização: Portador | Sim | Sim |

Ao utilizar `Ocp-Apim-Subscription-Key` o cabeçalho, só é necessário fornecer a sua chave de subscrição. Por exemplo:

```http
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Ao utilizar `Authorization: Bearer` o cabeçalho, é-lhe exigido `issueToken` que faça um pedido até ao ponto final. Neste pedido, você troca a sua chave de subscrição por um token de acesso válido por 10 minutos. Nas próximas secções, aprenderás a obter um símbolo e a usar um símbolo.

### <a name="how-to-get-an-access-token"></a>Como obter um sinal de acesso

Para obter um sinal de acesso, terá de `issueToken` fazer um `Ocp-Apim-Subscription-Key` pedido para o ponto final utilizando a chave de subscrição e a sua chave de subscrição.

O `issueToken` ponto final tem este formato:

```http
https://<REGION_IDENTIFIER>.api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Substitua-o `<REGION_IDENTIFIER>` pelo identificador que corresponde à região da sua subscrição a partir desta tabela:

[!INCLUDE [](cognitive-services-speech-service-region-identifier.md)]

Use estas amostras para criar o seu pedido de acesso.

#### <a name="http-sample"></a>Amostra http

Este exemplo é um simples pedido http para obter um símbolo. Substitua-a `YOUR_SUBSCRIPTION_KEY` com a chave de subscrição do Serviço de Fala. Se a sua subscrição não estiver na `Host` região oeste dos EUA, substitua o cabeçalho com o nome de anfitrião da sua região.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

O corpo da resposta contém o símbolo de acesso no formato JSON Web Token (JWT).

#### <a name="powershell-sample"></a>Exemplo do PowerShell

Este exemplo é um simples script PowerShell para obter um sinal de acesso. Substitua-a `YOUR_SUBSCRIPTION_KEY` com a chave de subscrição do Serviço de Fala. Certifique-se de que utiliza o ponto final correto para a região que corresponde à sua subscrição. Este exemplo está atualmente definido para os EUA Ocidentais.

```powershell
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

#### <a name="curl-sample"></a>amostra cURL

cURL é uma ferramenta de linha de comando disponível em Linux (e no Subsistema Windows para Linux). Este comando cURL ilustra como obter um sinal de acesso. Substitua-a `YOUR_SUBSCRIPTION_KEY` com a chave de subscrição do Serviço de Fala. Certifique-se de que utiliza o ponto final correto para a região que corresponde à sua subscrição. Este exemplo está atualmente definido para os EUA Ocidentais.

```console
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>Exemplo C#

Esta aula de C# ilustra como obter um sinal de acesso. Passe a sua chave de assinatura do Speech Service quando instantaneamente a aula. Se a sua subscrição não estiver na região dos EUA Ocidentais, altere o valor de `FetchTokenUri` corresponder à região para a sua subscrição.

```csharp
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

### <a name="how-to-use-an-access-token"></a>Como usar um sinal de acesso

O sinal de acesso deve ser `Authorization: Bearer <TOKEN>` enviado para o serviço como cabeçalho. Cada ficha de acesso é válida por 10 minutos. No entanto, pode obter um novo símbolo a qualquer momento, para minimizar o tráfego de rede e a latência, recomendamos a utilização do mesmo símbolo durante nove minutos.

Aqui está uma amostra http pedido para a API REST text-to-speech:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
