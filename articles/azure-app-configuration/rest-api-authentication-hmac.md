---
title: Configuração de aplicativoS Azure REST API - Autenticação HMAC
description: Utilize o HMAC para autenticar a configuração da app Azure utilizando a API REST
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 4b5a073459fad734a11d3a75718240d0ebbb486a
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981396"
---
# <a name="hmac-authentication---rest-api-reference"></a>Autenticação HMAC - Referência REST API

Pode autenticar pedidos HTTP utilizando o sistema de autenticação HMAC-SHA256. (HMAC refere-se ao código de autenticação de mensagens baseado em haxixe.) Estes pedidos devem ser transmitidos através do TLS.

## <a name="prerequisites"></a>Pré-requisitos

- **Credencial** - \<Access Key ID\>
- **Secret** - base64 descodificado Valor chave de acesso. ``base64_decode(<Access Key Value>)``

Os valores para credencial (também `id` chamado) e secreto (também chamado `value` ) devem ser obtidos a partir do caso de Configuração de Aplicação Azure. Pode fazê-lo utilizando o [portal Azure](https://portal.azure.com) ou o [Azure CLI](/cli/azure/).

Forneça a cada pedido todos os cabeçalhos HTTP necessários para a autenticação. O mínimo exigido é:

|  Cabeçalho do pedido | Descrição  |
| --------------- | ------------ |
| **Anfitrião** | Anfitrião de internet e número de porta. Para mais informações, consulte a secção  [3.2.2](https://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.2.2). |
| **Data** | Data e hora em que o pedido foi originado. Não pode estar a mais de 15 minutos do tempo universal coordenado atual (Hora Média de Greenwich). O valor é uma data HTTP, conforme descrito na secção [3.3.1](https://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.3.1).
| **x-ms-data** | O mesmo ```Date``` que acima. Pode usá-lo em vez disso quando o agente não pode aceder diretamente ao cabeçalho do ```Date``` pedido, ou um proxy modifica-o. Se ```x-ms-date``` ```Date``` ambos forem fornecidos, ```x-ms-date``` tem precedência. |
| **x-ms-content-sha256** | base64 codificado SHA256 hash do corpo pedido. Deve ser fornecido mesmo que não haja corpo. ```base64_encode(SHA256(body))```|
| **Autorização** | Informações de autenticação requeridas pelo regime HMAC-SHA256. O formato e os detalhes são explicados mais tarde neste artigo. |

**Exemplo:**

```http
Host: {myconfig}.azconfig.io
Date: Fri, 11 May 2018 18:48:36 GMT
x-ms-content-sha256: {SHA256 hash of the request body}
Authorization: HMAC-SHA256 Credential={Access Key ID}&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature={Signature}
```

## <a name="authorization-header"></a>Cabeçalho de autorização

### <a name="syntax"></a>Syntax

``Authorization``: **HMAC-SHA256**```Credential```=\<value\>&```SignedHeaders```=\<value\>&```Signature```=\<value\>

|  Argumento | Description  |
| ------ | ------ |
| **HMAC-SHA256** | Esquema de autorização. _(obrigatório)_ |
| **Credencial** | A identificação da chave de acesso usada para calcular a assinatura. _(obrigatório)_ |
| **Cabeçalhos Assinados** | HTTP Solicitam cabeçalhos adicionados à assinatura. _(obrigatório)_ |
| **Assinatura** | base64 codificado HMACSHA256 de String-To-Sign. _(obrigatório)_|

### <a name="credential"></a>Credencial

Identificação da chave de acesso usada para calcular a assinatura.

### <a name="signed-headers"></a>Cabeçalhos assinados

HTTP Solicitar nomes de cabeçalho, separados por pontos-e-vírguis, necessários para assinar o pedido. Estes cabeçalhos HTTP devem ser corretamente fornecidos com o pedido também. Não use espaços brancos.

### <a name="required-http-request-headers"></a>Cabeçalhos de pedido HTTP necessários

```x-ms-date```[ou ```Date``` ```host``` ];```x-ms-content-sha256```

Qualquer outro cabeçalho de pedido HTTP também pode ser adicionado à assinatura. Basta adiciá-los ao ```SignedHeaders``` argumento.

**Exemplo:**

x-ms-date;host;x-ms-content-sha256; ```Content-Type``````Accept```

### <a name="signature"></a>Assinatura

Base64 codificava o haxixe HMACSHA256 da Corda-A-Sign. Utiliza a chave de acesso identificada por `Credential` .
```base64_encode(HMACSHA256(String-To-Sign, Secret))```

### <a name="string-to-sign"></a>String-to-Sign

É uma representação canónica do pedido:

_String-to-Sign=_

**HTTP_METHOD** + '\n' + **path_and_query** + '\n' + **signed_headers_values**

|  Argumento | Description  |
| ------ | ------ |
| **HTTP_METHOD** | Nome do método HTTP maiúscula utilizado com o pedido. Para mais informações, consulte [a secção 9.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) |
|**path_and_query** | Concatenação de pedido absoluta caminho URI e cadeia de consulta. Para mais informações, consulte [a secção 3.3](https://tools.ietf.org/html/rfc3986#section-3.3).
| **signed_headers_values** | Valores separados por semicolon de todos os cabeçalhos de pedido HTTP listados em `SignedHeaders` . O formato segue `SignedHeaders` a semântica. |

**Exemplo:**

```js
string-To-Sign=
            "GET" + '\n' +                                                                                  // VERB
            "/kv?fields=*&api-version=1.0" + '\n' +                                                         // path_and_query
            "Fri, 11 May 2018 18:48:36 GMT;{myconfig}.azconfig.io;{value of ms-content-sha256 header}"      // signed_headers_values
```


### <a name="errors"></a>Erros

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Razão:** O pedido de autorização com o regime HMAC-SHA256 não é fornecido.

**Solução:** Forneça um ```Authorization``` cabeçalho de pedido HTTP válido.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="The access token has expired", Bearer
```

**Razão:** ```Date``` ou ```x-ms-date``` o cabeçalho de pedido está a mais de 15 minutos do tempo universal coordenado atual (Tempo Médio de Greenwich).

**Solução:** Forneça a data e a hora corretas.


```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid access token date", Bearer
```

**Razão:** Desaparecido ou inválido ```Date``` ou ```x-ms-date``` cabeçalho de pedido.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="[Credential][SignedHeaders][Signature] is required", Bearer
```

**Razão:** Faltando um parâmetro necessário do cabeçalho do ```Authorization``` pedido.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Credential", Bearer
```

**Razão:** O ID fornecido ```Host``` [[Chave de Acesso] não é encontrado.

**Solução:** Verifique o ```Credential``` parâmetro do cabeçalho do ```Authorization``` pedido. Certifique-se de que é um ID de chave de acesso válido e certifique-se de que o ```Host``` cabeçalho aponta para a conta registada.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature", Bearer
```

**Razão:** O ```Signature``` fornecido não corresponde ao que o servidor espera.

**Solução:** Certifique-se de que ```String-To-Sign``` está correto. Certifique-se de que o ```Secret``` está correto e corretamente utilizado (base64 descodificado antes da utilização).

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Signed request header 'xxx' is not provided", Bearer
```

**Razão:** Cabeçalho de pedido em falta exigido por ```SignedHeaders``` parâmetro no  ```Authorization``` cabeçalho.

**Solução:** Forneça o cabeçalho necessário, com o valor correto.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="XXX is required as a signed header", Bearer
```

**Razão:** Parâmetro em falta em ```SignedHeaders``` .

**Solução:** Verifique os requisitos mínimos dos cabeçalhos assinados.

## <a name="code-snippets"></a>Fragmentos de código

### <a name="javascript"></a>JavaScript

*Pré-requisitos*: [Crypto-JS](https://code.google.com/archive/p/crypto-js/)

```js
function signRequest(host, 
                     method,      // GET, PUT, POST, DELETE
                     url,         // path+query
                     body,        // request body (undefined of none)
                     credential,  // access key id
                     secret)      // access key value (base64 encoded)
{
        var verb = method.toUpperCase();
        var utcNow = new Date().toUTCString();
        var contentHash = CryptoJS.SHA256(body).toString(CryptoJS.enc.Base64);

        //
        // SignedHeaders
        var signedHeaders = "x-ms-date;host;x-ms-content-sha256"; // Semicolon separated header names

        //
        // String-To-Sign
        var stringToSign = 
            verb + '\n' +                              // VERB
            url + '\n' +                               // path_and_query
            utcNow + ';' + host + ';' + contentHash;   // Semicolon separated SignedHeaders values

        //
        // Signature
        var signature = CryptoJS.HmacSHA256(stringToSign, CryptoJS.enc.Base64.parse(secret)).toString(CryptoJS.enc.Base64);

        //
        // Result request headers
        return [
            { name: "x-ms-date", value: utcNow },
            { name: "x-ms-content-sha256", value: contentHash },
            { name: "Authorization", value: "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=" + signedHeaders + "&Signature=" + signature }
        ];
}
```

### <a name="c"></a>C#

```csharp
using (var client = new HttpClient())
{
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://{config store name}.azconfig.io/kv?api-version=1.0"),
        Method = HttpMethod.Get
    };

    //
    // Sign the request
    request.Sign(<Credential>, <Secret>);

    await client.SendAsync(request);
}

static class HttpRequestMessageExtensions
{
    public static HttpRequestMessage Sign(this HttpRequestMessage request, string credential, byte[] secret)
    {
        string host = request.RequestUri.Authority;
        string verb = request.Method.ToString().ToUpper();
        DateTimeOffset utcNow = DateTimeOffset.UtcNow;
        string contentHash = Convert.ToBase64String(request.Content.ComputeSha256Hash());

        //
        // SignedHeaders
        string signedHeaders = "date;host;x-ms-content-sha256"; // Semicolon separated header names

        //
        // String-To-Sign
        var stringToSign = $"{verb}\n{request.RequestUri.PathAndQuery}\n{utcNow.ToString("r")};{host};{contentHash}";

        //
        // Signature
        string signature;

        using (var hmac = new HMACSHA256(secret))
        {
            signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.ASCII.GetBytes(stringToSign)));
        }

        //
        // Add headers
        request.Headers.Date = utcNow;
        request.Headers.Add("x-ms-content-sha256", contentHash);
        request.Headers.Authorization = new AuthenticationHeaderValue("HMAC-SHA256", $"Credential={credential}&SignedHeaders={signedHeaders}&Signature={signature}");

        return request;
    }
}

static class HttpContentExtensions
{
    public static byte[] ComputeSha256Hash(this HttpContent content)
    {
        using (var stream = new MemoryStream())
        {
            if (content != null)
            {
                content.CopyToAsync(stream).Wait();
                stream.Seek(0, SeekOrigin.Begin);
            }

            using (var alg = SHA256.Create())
            {
                return alg.ComputeHash(stream.ToArray());
            }
        }
    }
}
```

### <a name="java"></a>Java

```java
public CloseableHttpResponse signRequest(HttpUriRequest request, String credential, String secret)
        throws IOException, URISyntaxException {
    Map<String, String> authHeaders = generateHeader(request, credential, secret);
    authHeaders.forEach(request::setHeader);

    return httpClient.execute(request);
}

private static Map<String, String> generateHeader(HttpUriRequest request, String credential, String secret) 
        throws URISyntaxException, IOException {
    String requestTime = GMT_DATE_FORMAT.format(new Date());

    String contentHash = buildContentHash(request);
    // SignedHeaders
    String signedHeaders = "x-ms-date;host;x-ms-content-sha256";

    // Signature
    String methodName = request.getRequestLine().getMethod().toUpperCase();
    URIBuilder uri = new URIBuilder(request.getRequestLine().getUri());
    String scheme = uri.getScheme() + "://";
    String requestPath = uri.toString().substring(scheme.length()).substring(uri.getHost().length());
    String host = new URIBuilder(request.getRequestLine().getUri()).getHost();
    String toSign = String.format("%s\n%s\n%s;%s;%s", methodName, requestPath, requestTime, host, contentHash);

    byte[] decodedKey = Base64.getDecoder().decode(secret);
    String signature = Base64.getEncoder().encodeToString(new HmacUtils(HMAC_SHA_256, decodedKey).hmac(toSign));

    // Compose headers
    Map<String, String> headers = new HashMap<>();
    headers.put("x-ms-date", requestTime);
    headers.put("x-ms-content-sha256", contentHash);

    String authorization = String.format("HMAC-SHA256 Credential=%s, SignedHeaders=%s, Signature=%s",
            credential, signedHeaders, signature);
    headers.put("Authorization", authorization);

    return headers;
}

private static String buildContentHash(HttpUriRequest request) throws IOException {
    String content = "";
    if (request instanceof HttpEntityEnclosingRequest) {
        try {
            StringWriter writer = new StringWriter();
            IOUtils.copy(((HttpEntityEnclosingRequest) request).getEntity().getContent(), writer,
                    StandardCharsets.UTF_8);

            content = writer.toString();
        }
        finally {
            ((HttpEntityEnclosingRequest) request).getEntity().getContent().close();
        }
    }

    byte[] digest = new DigestUtils(SHA_256).digest(content);
    return Base64.getEncoder().encodeToString(digest);
}
```

### <a name="golang"></a>Golang

```golang
import (
    "bytes"
    "crypto/hmac"
    "crypto/sha256"
    "encoding/base64"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

//SignRequest Setup the auth header for accessing Azure App Configuration service
func SignRequest(id string, secret string, req *http.Request) error {
    method := req.Method
    host := req.URL.Host
    pathAndQuery := req.URL.Path
    if req.URL.RawQuery != "" {
        pathAndQuery = pathAndQuery + "?" + req.URL.RawQuery
    }

    content, err := ioutil.ReadAll(req.Body)
    if err != nil {
        return err
    }
    req.Body = ioutil.NopCloser(bytes.NewBuffer(content))

    key, err := base64.StdEncoding.DecodeString(secret)
    if err != nil {
        return err
    }

    timestamp := time.Now().UTC().Format(http.TimeFormat)
    contentHash := getContentHashBase64(content)
    stringToSign := fmt.Sprintf("%s\n%s\n%s;%s;%s", strings.ToUpper(method), pathAndQuery, timestamp, host, contentHash)
    signature := getHmac(stringToSign, key)

    req.Header.Set("x-ms-content-sha256", contentHash)
    req.Header.Set("x-ms-date", timestamp)
    req.Header.Set("Authorization", "HMAC-SHA256 Credential="+id+", SignedHeaders=x-ms-date;host;x-ms-content-sha256, Signature="+signature)

    return nil
}

func getContentHashBase64(content []byte) string {
    hasher := sha256.New()
    hasher.Write(content)
    return base64.StdEncoding.EncodeToString(hasher.Sum(nil))
}

func getHmac(content string, key []byte) string {
    hmac := hmac.New(sha256.New, key)
    hmac.Write([]byte(content))
    return base64.StdEncoding.EncodeToString(hmac.Sum(nil))
}
```

### <a name="python"></a>Python

```python

import base64
import hashlib
import hmac
from datetime import datetime
import six

def sign_request(host,
                method,     # GET, PUT, POST, DELETE
                url,        # Path + Query
                body,       # Request body 
                credential, # Access Key ID
                secret):    # Access Key Value
    verb = method.upper()

    utc_now = str(datetime.utcnow().strftime("%b, %d %Y %H:%M:%S ")) + "GMT"

    if six.PY2:
        content_digest = hashlib.sha256(bytes(body)).digest()
    else:
        content_digest = hashlib.sha256(bytes(body, 'utf-8')).digest()

    content_hash = base64.b64encode(content_digest).decode('utf-8')

    # Signed Headers
    signed_headers = "x-ms-date;host;x-ms-content-sha256"  # Semicolon separated header names

    # String-To-Sign
    string_to_sign = verb + '\n' + \
                    url + '\n' + \
                    utc_now + ';' + host + ';' + content_hash  # Semicolon separated SignedHeaders values

    # Decode secret
    if six.PY2:
        decoded_secret = base64.b64decode(secret)
        digest = hmac.new(decoded_secret, bytes(
            string_to_sign), hashlib.sha256).digest()
    else:
        decoded_secret = base64.b64decode(secret, validate=True)
        digest = hmac.new(decoded_secret, bytes(
            string_to_sign, 'utf-8'), hashlib.sha256).digest()

    # Signature
    signature = base64.b64encode(digest).decode('utf-8')

    # Result request headers
    return {
        "x-ms-date": utc_now,
        "x-ms-content-sha256": content_hash,
        "Authorization": "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=" + signed_headers + "&Signature=" + signature
    }
```

### <a name="powershell"></a>PowerShell

```PowerShell
function Sign-Request(
    [string] $hostname,
    [string] $method,      # GET, PUT, POST, DELETE
    [string] $url,         # path+query
    [string] $body,        # request body
    [string] $credential,  # access key id
    [string] $secret       # access key value (base64 encoded)
)
{  
    $verb = $method.ToUpperInvariant()
    $utcNow = (Get-Date).ToUniversalTime().ToString("R", [Globalization.DateTimeFormatInfo]::InvariantInfo)
    $contentHash = Compute-SHA256Hash $body

    $signedHeaders = "x-ms-date;host;x-ms-content-sha256";  # Semicolon separated header names

    $stringToSign = $verb + "`n" +
                    $url + "`n" +
                    $utcNow + ";" + $hostname + ";" + $contentHash  # Semicolon separated signedHeaders values

    $signature = Compute-HMACSHA256Hash $secret $stringToSign

    # Return request headers
    return @{
        "x-ms-date" = $utcNow;
        "x-ms-content-sha256" = $contentHash;
        "Authorization" = "HMAC-SHA256 Credential=" + $credential + "&SignedHeaders=" + $signedHeaders + "&Signature=" + $signature
    }
}

function Compute-SHA256Hash(
    [string] $content
)
{
    $sha256 = [System.Security.Cryptography.SHA256]::Create()
    try {
        return [Convert]::ToBase64String($sha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($content)))
    }
    finally {
        $sha256.Dispose()
    }
}

function Compute-HMACSHA256Hash(
    [string] $secret,      # base64 encoded
    [string] $content
)
{
    $hmac = [System.Security.Cryptography.HMACSHA256]::new([Convert]::FromBase64String($secret))
    try {
        return [Convert]::ToBase64String($hmac.ComputeHash([Text.Encoding]::ASCII.GetBytes($content)))
    }
    finally {
        $hmac.Dispose()
    }
}

# Stop if any error occurs
$ErrorActionPreference = "Stop"

$uri = [System.Uri]::new("https://{myconfig}.azconfig.io/kv?api-version=1.0")
$method = "GET"
$body = $null
$credential = "<Credential>"
$secret = "<Secret>"

$headers = Sign-Request $uri.Authority $method $uri.PathAndQuery $body $credential $secret
Invoke-RestMethod -Uri $uri -Method $method -Headers $headers -Body $body
```

### <a name="bash"></a>Bash

*Pré-requisitos:*

| Pré-requisito | Comando | Versões testadas |
| ------------ | ------- | --------------- |
| [Bash](https://www.gnu.org/software/bash/) | bash | 3.5.27, 4.4.23 |
| [coreutils](https://www.gnu.org/software/coreutils/) | tr | 8.28 |
| [curl](https://curl.haxx.se/) | curl | 7.55.1, 7.58.0 |
| [Abre-se](https://www.openssl.org/) | openssl | 1.1.0g, 1.1.1a |
| [util-linux](https://github.com/karelzak/util-linux/) | hexdump | 2.14.1, 2.31.1 |

```bash
#!/bin/bash

sign_request () {
    local host="$1"
    local method="$2"      # GET, PUT, POST, DELETE
    local url="$3"         # path+query
    local body="$4"        # request body
    local credential="$5"  # access key id
    local secret="$6"      # access key value (base64 encoded)

    local verb=$(printf "$method" | tr '[:lower:]' '[:upper:]')
    local utc_now="$(date -u '+%a, %d %b %Y %H:%M:%S GMT')"
    local content_hash="$(printf "$body" | openssl sha256 -binary | base64)"

    local signed_headers="x-ms-date;host;x-ms-content-sha256"  # Semicolon separated header names
    local string_to_sign="$verb\n$url\n$utc_now;$host;$content_hash"  # Semicolon separated signed_headers values

    local decoded_secret="$(printf "$secret" | base64 -d | hexdump -v -e '/1 "%02x"')"
    local signature="$(printf "$string_to_sign" | openssl sha256 -mac HMAC -macopt hexkey:"$decoded_secret" -binary | base64)"

    # Output request headers
    printf '%s\n' \
           "x-ms-date: $utc_now" \
           "x-ms-content-sha256: $content_hash" \
           "Authorization: HMAC-SHA256 Credential=$credential&SignedHeaders=$signed_headers&Signature=$signature"
}

host="{config store name}.azconfig.io"
method="GET"
url="/kv?api-version=1.0"
body=""
credential="<Credential>"
secret="<Secret>"

headers=$(sign_request "$host" "$method" "$url" "$body" "$credential" "$secret")

while IFS= read -r line; do
    header_args+=("-H$line")
done <<< "$headers"
curl -X "$method" -d "$body" "${header_args[@]}" "https://$host$url"
```
