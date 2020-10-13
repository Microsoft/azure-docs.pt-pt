---
title: Códigos de erro da API REST - Azure Key Vault
description: Estes códigos de erro podem ser devolvidos através de uma operação num serviço web Azure Key Vault.
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: 30b7e34f2a791cfd8dec1a6d8e81d706fa07939f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631227"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Códigos de erro Azure Key Vault REST API
 
Os seguintes códigos de erro podem ser devolvidos através de uma operação num serviço web Azure Key Vault.
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401: Pedido não autenticado

401 significa que o pedido não é autenticado para o Cofre de Chaves. 

Um pedido é autenticado se:

- O cofre-chave sabe a identidade do chamador; e
- O chamador pode tentar aceder aos recursos do Cofre chave. 

Existem várias razões diferentes para um pedido devolver 401.

### <a name="no-authentication-token-attached-to-the-request"></a>Nenhum símbolo de autenticação anexado ao pedido. 

Aqui está um pedido de PUT exemplo, definindo o valor de um segredo:

``` 
PUT https://putreqexample.vault.azure.net//secrets/DatabaseRotatingPassword?api-version=7.0 HTTP/1.1
x-ms-client-request-id: 03d275a2-52a4-4bed-82c8-6fe15165affb
accept-language: en-US
Authorization: Bearer     eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9.eyJhdWQiOiJodHRwczovL3ZhdWx0LmF6dXJlLm5ldCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpYXQiOjE1NDg2OTc1MTMsIm5iZiI6MTU0ODY5NzUxMywiZXhwIjoxNTQ4NzAxNDEzLCJhaW8iOiI0MkpnWUhoODVqaVBnZHF5ZlRGZE5TdHY3bGUvQkFBPSIsImFwcGlkIjoiZmFkN2Q1YjMtNjlkNi00YjQ4LTkyNTktOGQxMjEyNGUxY2YxIiwiYXBwaWRhY3IiOiIxIiwiaWRwIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsIm9pZCI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInN1YiI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjItZ3JoUmtlSWs2QmVZLUxuNDJtQUEiLCJ2ZXIiOiIxLjAifQ.fgubiz1MKqTJTXI8dHIV7t9Fle6FdHrkaGYKcBeVRX1WtLVuk1QVxzIFDlZKLXJ7QPNs0KWpeiWQI9IWIRK-8wO38yCqKTfDlfHOiNWGOpkKddlG729KFqakVf2w0GPyGPFCONRDAR5wjQarN9Bt8I8YbHwZQz_M1hztlnv-Lmsk1jBmech9ujD9-lTMBmSfFFbHcqquev119V7sneI-zxBZLf8C0pIDkaXf1t8y6Xr8CUJDMdlWLslCf3pBCNIOy65_TyGvy4Z4AJryTPBarNBPwOkNAtjCfZ4BDc2KqUZM5QN_VK4foP64sVzUL6mSr0Gh7lQJIL5b1qIpJxjxyQ
User-Agent: FxVersion/4.7.3324.0 OSName/Windows OSVersion/6.2.9200.0 Microsoft.Azure.KeyVault.KeyVaultClient/3.0.3.0
Content-Type: application/json; charset=utf-8
Host: putreqexample.vault.azure.net
Content-Length: 31

{
   "value": "m*gBJ7$Zuoz)"
}
```

O cabeçalho "Autorização" é o símbolo de acesso que é necessário com cada chamada para o Cofre-Chave para operações de data-plane. Se o cabeçalho faltar, então a resposta deve ser 401.

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>O símbolo carece do recurso correto associado. 

Ao solicitar um sinal de acesso a partir do ponto final da Azure OAUTH, é obrigatório um parâmetro chamado "recurso". O valor é importante para o fornecedor de token, uma vez que este considera o símbolo para a sua utilização pretendida. O recurso para **todos os** tokens acederem a um Cofre chave é *https: \/ /vault.keyvault.net* (sem barra de rasto).

### <a name="the-token-is-expired"></a>O símbolo expirou.

Os tokens são codificados base64 e os valores podem ser descodificados em websites como [http://jwt.calebb.net](http://jwt.calebb.net) . Aqui está o símbolo acima descodificado:

```
    {
 typ: "JWT",
 alg: "RS256",
 x5t: "nbCwW11w3XkB-xUaXwKRSLjMHGQ",
 kid: "nbCwW11w3XkB-xUaXwKRSLjMHGQ"
}.
{
 aud: "https://vault.azure.net",
 iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 iat: 1548697513,
 nbf: 1548697513,
 exp: 1548701413,
 aio: "42JgYHh85jiPgdqyfTFdNStv7le/BAA=",
 appid: "fad7d5b3-69d6-4b48-9259-8d12124e1cf1",
 appidacr: "1",
 idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 oid: "3975aeed-7d08-453b-b6f4-445f32698091",
 sub: "3975aeed-7d08-453b-b6f4-445f32698091",
 tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",
 uti: "2-grhRkeIk6BeY-Ln42mAA",
 ver: "1.0"
}.
[signature]
```

Podemos ver muitas partes importantes neste símbolo:

- aud (público): O recurso do símbolo. Note que isto `https://vault.azure.net` é. Este token NÃO funcionará para qualquer recurso que não corresponda explicitamente a este valor, como gráfico.
- iat (emitido em): O número de carraças desde o início da época quando o token foi emitido.
- nbf (não antes): O número de carraças desde o início da época quando este token se torna válido.
- exp (expiração): O número de carrapatos desde o início da época quando este token expirar.
- appid (ID de aplicação): O GUID para o ID da aplicação fazendo este pedido.
- tid (ID inquilino): O GUID para o ID do inquilino do diretor fazendo este pedido

É importante que todos os valores sejam devidamente identificados no token para que o pedido funcione. Se tudo estiver correto, o pedido não resultará em 401.

### <a name="troubleshooting-401"></a>Resolução de problemas 401

401s devem ser investigados a partir do ponto de geração simbólica, antes que o pedido seja feito para o cofre chave. Geralmente, o código está a ser usado para pedir o sinal. Uma vez que o sinal é recebido, é passado para o pedido do Cofre chave. Se o código estiver a funcionar localmente, pode utilizar o Fiddler para capturar o pedido/resposta a `https://login.microsoftonline.com` . Um pedido é o seguinte:

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

As seguintes informações fornecidas pelo utilizador devem estar corretas:

- A chave do cofre do inquilino iD
- Valor de recursos definido para https%3A%2F%2Fvault.azure.net (URL codificado)
- ID de Cliente
- Segredo do cliente

Certifique-se de que o resto do pedido é quase idêntico.

Se você só pode obter o token de acesso de resposta, você pode descodificá-lo (como mostrado acima) para garantir o ID do inquilino, o ID do cliente (ID da aplicação) e o recurso.

## <a name="http-403-insufficient-permissions"></a>HTTP 403: Permissões insuficientes

HTTP 403 significa que o pedido foi autenticado (sabe a identidade do pedido) mas a identidade não tem autorização para aceder ao recurso solicitado. Há duas causas:

- Não há política de acesso à identidade.
- O endereço IP do recurso de pedido não é aprovado nas definições de firewall do cofre de chaves.

HTTP 403 ocorre frequentemente quando a aplicação do cliente não está a usar o ID do cliente que o cliente pensa que é. Isto significa geralmente que as políticas de acesso não estão corretamente criadas para a identidade de chamada real.

### <a name="troubleshooting-403"></a>Resolução de problemas 403

Primeiro, ligue a registar. Para obter instruções sobre como fazê-lo, consulte [o registo do Cofre da Chave Azure](logging.md).

Uma vez ligado o registo, pode determinar se o 403 se deve à política de acesso ou à política de firewall.

#### <a name="error-due-to-firewall-policy"></a>Erro devido à política de firewall

"O endereço do cliente (00.00.00.00) não é autorizado e o chamador não é um serviço de confiança"

Existe uma lista limitada de "Serviços Fidedignos Azure". Os Web Sites Azure **não** são um Serviço Azure Fidedigno. Para obter mais informações, consulte o blog post [Key Vault Firewall acede por Azure App Services](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services).

Tem de adicionar o endereço IP do Web Site Azure ao Cofre de Chaves para que funcione.

Se for devido à política de acesso: encontre o ID do objeto para o pedido e certifique-se de que o ID do objeto corresponde ao objeto ao qual o utilizador está a tentar atribuir a política de acesso. Muitas vezes haverá vários objetos na AAD que têm o mesmo nome, por isso escolher o correto é muito importante. Ao eliminar e reatar a política de acesso, é possível ver se existem vários objetos com o mesmo nome.

Além disso, a maioria das políticas de acesso não requer a utilização da "aplicação autorizada", como mostra o portal. A aplicação autorizada é utilizada para cenários de autenticação "em nome de" que são raros. 


## <a name="http-429-too-many-requests"></a>HTTP 429: Demasiados pedidos

O estrangulamento ocorre quando o número de pedidos excede o máximo indicado para o prazo. Se ocorrer estrangulamento, a resposta do Cofre-Chave será HTTP 429. Existem máximos indicados para os tipos de pedidos feitos. Por exemplo: a criação de uma chave HSM 2048-bit é de 5 pedidos por 10 segundos, mas todas as outras transações de HSM têm um limite de 1000 pedidos/10 segundos. Por conseguinte, é importante compreender que tipos de chamadas estão a ser feitas para determinar a causa do estrangulamento.
Em geral, os pedidos para o Cofre chave estão limitados a 2000 pedidos/10 segundos. Exceções são Operações-Chave, conforme documentado nos [limites de serviço do Cofre-Chave](service-limits.md)

### <a name="troubleshooting-429"></a>Resolução de problemas 429
O estrangulamento é trabalhado utilizando estas técnicas:

- Reduza o número de pedidos feitos ao Cofre-Chave, determinando se existem padrões para um recurso solicitado e tentando cache-los na aplicação de chamada. 

- Quando ocorrer estrangulamento do Cofre de Chaves, adapte o código de pedido para utilizar um recuo exponencial para re-tentar. O algoritmo é explicado aqui: [Como acelerar a sua app](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- Se o número de pedidos não puder ser reduzido através do caching e o backoff cronometrado não funcionar, então considere dividir as chaves em vários Cofres-Chave. O limite de serviço para uma única subscrição é 5x o limite individual do Key Vault. Se utilizar mais de 5 Cofres-chave, deve considerar-se a utilização de várias subscrições. 

Orientações detalhadas, incluindo o pedido de aumento de limites, podem ser encontrados aqui: [Orientação de estrangulamento do Cofre-Chave](overview-throttling.md)
