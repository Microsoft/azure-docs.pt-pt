---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: 410b73d3f9011f9384fafa18394d9318e3eafa67
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504496"
---
## <a name="authentication"></a>Autenticação

Cada pedido requer um cabeçalho de autorização. Esta tabela ilustra quais os cabeçalhos suportados por cada serviço:

| Cabeçalhos de autorização apoiados | Conversão de voz em texto | Conversão de texto em voz |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | Sim | Não |
| Autorização: Portador | Sim | Sim |

Ao utilizar o `Ocp-Apim-Subscription-Key` cabeçalho, só é obrigado a fornecer a sua chave de subscrição. Por exemplo:

```http
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Ao utilizar o `Authorization: Bearer` cabeçalho, é obrigado a fazer um pedido ao `issueToken` ponto final. Neste pedido, você troca a sua chave de subscrição por um token de acesso válido por 10 minutos. Nas próximas secções, aprenderás a obter um símbolo e usarás um símbolo.

### <a name="how-to-get-an-access-token"></a>Como obter um token de acesso

Para obter um token de acesso, você precisará fazer um pedido para o `issueToken` ponto final usando a `Ocp-Apim-Subscription-Key` chave de subscrição e a sua.

O `issueToken` ponto final tem este formato:

```http
https://<REGION_IDENTIFIER>.api.cognitive.microsoft.com/sts/v1.0/issueToken
```

`<REGION_IDENTIFIER>`Substitua-a pelo identificador correspondente à região da sua subscrição a partir desta tabela:

[!INCLUDE [](cognitive-services-speech-service-region-identifier.md)]

Use estas amostras para criar o seu pedido de sinal de acesso.

#### <a name="http-sample"></a>Amostra HTTP

Este exemplo é um simples pedido HTTP para obter um token. `YOUR_SUBSCRIPTION_KEY`Substitua-a pela tecla de subscrição do Serviço de Discurso. Se a sua subscrição não estiver na região oeste dos EUA, substitua o `Host` cabeçalho pelo nome de anfitrião da sua região.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

O corpo da resposta contém o token de acesso no formato JSON Web Token (JWT).

#### <a name="powershell-sample"></a>Exemplo do PowerShell

Este exemplo é um simples script PowerShell para obter um token de acesso. `YOUR_SUBSCRIPTION_KEY`Substitua-a pela tecla de subscrição do Serviço de Discurso. Certifique-se de que utiliza o ponto final correto para a região que corresponde à sua subscrição. Este exemplo está atualmente definido para os EUA Ocidentais.

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

cURL é uma ferramenta de linha de comando disponível no Linux (e no Subsistema Windows para Linux). Este comando cURL ilustra como obter um token de acesso. `YOUR_SUBSCRIPTION_KEY`Substitua-a pela tecla de subscrição do Serviço de Discurso. Certifique-se de que utiliza o ponto final correto para a região que corresponde à sua subscrição. Este exemplo está atualmente definido para os EUA Ocidentais.

```console
curl -v -X POST \
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>Exemplo C#

Esta classe C# ilustra como obter um token de acesso. Passe a sua chave de subscrição do Serviço de Discurso quando instantanear a aula. Se a sua subscrição não estiver na região oeste dos EUA, altere o valor de `FetchTokenUri` corresponder à região para a sua subscrição.

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

#### <a name="python-sample"></a>Amostra de Python

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

### <a name="how-to-use-an-access-token"></a>Como usar um símbolo de acesso

O token de acesso deve ser enviado para o serviço como `Authorization: Bearer <TOKEN>` cabeçalho. Cada ficha de acesso é válida por 10 minutos. No entanto, pode obter um novo token a qualquer momento, para minimizar o tráfego da rede e a latência, recomendamos usar o mesmo token durante nove minutos.

Aqui está um pedido HTTP de amostra à API REST discurso-a-texto para áudio curto:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
