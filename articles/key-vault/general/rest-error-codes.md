---
title: Códigos de erro da API REST - Azure Key Vault
description: Estes códigos de erro podem ser devolvidos por uma operação num serviço web Azure Key Vault.
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: bbb30c0ad41babca4158391c9e4e5c5d4d25cbf9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81432063"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Códigos de erro do cofre de chaves azure REST API
 
Os seguintes códigos de erro podem ser devolvidos por uma operação num serviço web Azure Key Vault.
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401: Pedido Não Autenticado

401 significa que o pedido não é autenticado para o Cofre chave. 

Um pedido é autenticado se:

- O cofre chave sabe a identidade do chamador; e
- O chamador pode tentar aceder aos recursos do Cofre chave. 

Há várias razões diferentes para um pedido devolver 401.

### <a name="no-authentication-token-attached-to-the-request"></a>Nenhum símbolo de autenticação anexa ao pedido. 

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

O cabeçalho "Autorização" é o sinal de acesso que é necessário a cada chamada para o Cofre chave para operações de avião de dados. Se o cabeceamento está em falta, então a resposta deve ser 401.

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>O símbolo carece do recurso correto associado a ele. 

Ao solicitar um sinal de acesso do ponto final do Azure OAUTH, é obrigatório um parâmetro chamado "recurso". O valor é importante para o fornecedor simbólico porque aplica o símbolo para a sua utilização pretendida. O recurso para **todos os** tokens para aceder a um Cofre chave é *https:\//vault.keyvault.net* (sem corte de rasto).

### <a name="the-token-is-expired"></a>O símbolo expirou.

Os tokens são codificados pela base64 e os valores [http://jwt.calebb.net](http://jwt.calebb.net)podem ser descodificados em websites como . Aqui está o símbolo acima descodificado:

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

- aud (público): O recurso do símbolo. Reparem que <https://vault.azure.net>isto é. Esta ficha NÃO funcionará para qualquer recurso que não corresponda explicitamente a este valor, como o gráfico.
- iat (emitido em): O número de carrapatos desde o início da época quando o símbolo foi emitido.
- nbf (não antes): O número de carrapatos desde o início da época quando este token se torna válido.
- exp (expiração): O número de carrapatos desde o início da época quando este token expirar.
- appid (ID da aplicação): O GUID para o ID da aplicação que faz este pedido.
- tid (ID inquilino): O GUID para a identificação do inquilino do principal fazendo este pedido

É importante que todos os valores sejam devidamente identificados no símbolo para que o pedido funcione. Se tudo estiver correto, então o pedido não resultará em 401.

### <a name="troubleshooting-401"></a>Resolução de problemas 401

401s devem ser investigados a partir do ponto de produção simbólica, antes que o pedido seja feito para o cofre chave. Geralmente, o código está a ser usado para solicitar o símbolo. Uma vez que o símbolo é recebido, é passado para o pedido do Cofre Chave. Se o código estiver em funcionamento localmente, pode `https://login.microsoftonline.com`utilizar o Fiddler para capturar o pedido/resposta a . Um pedido é o seguinte:

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

As seguintes informações fornecidas pelo utilizador estão corretas:

- A identificação do inquilino do cofre chave
- Valor de recurso definido para https%3A%2F%2Fvault.azure.net (URL codificado)
- ID de Cliente
- Segredo do cliente

Certifique-se de que o resto do pedido é quase idêntico.

Se só conseguir obter o sinal de acesso de resposta, pode descodificá-lo (como mostrado acima) para garantir o ID do inquilino, o ID do cliente (ID da aplicação) e o recurso.

## <a name="http-403-insufficient-permissions"></a>HTTP 403: Permissões insuficientes

HTTP 403 significa que o pedido foi autenticado (conhece a identidade solicitada), mas a identidade não tem autorização para aceder ao recurso solicitado. Há duas causas:

- Não há política de acesso à identidade.
- O endereço IP do recurso solicitado não está listado na lista de firewall do cofre chave.

HTTP 403 ocorre frequentemente quando a aplicação do cliente não está a usar o ID do cliente que o cliente pensa que é. Isto significa, normalmente, que as políticas de acesso não estão corretamente definidas para a identidade de chamada real.

### <a name="troubleshooting-403"></a>Resolução de problemas 403

Primeiro, ligue a exploração madeireira. Para obter instruções sobre como fazê-lo, consulte a [exploração do Cofre](logging.md)de Chaves Azure ).

Uma vez que o registo é ligado, pode determinar se o 403 se deve à política de acesso ou à política de firewall.

#### <a name="error-due-to-firewall-policy"></a>Erro devido à política de firewall

"O endereço do cliente (00.00.00.00) não está autorizado e o chamador não é um serviço de confiança"

Há uma lista limitada de "Serviços Fidedignos Azure". Os Web Sites Azure **não** são um serviço Azure fidedigno. Para mais informações, consulte o blog post [Key Vault Firewall acesso por Azure App Services](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services).

Deve adicionar o endereço IP do Web Site Azure ao Cofre chave para que funcione.

Se for devido à política de acesso: encontre o ID do objeto para o pedido e certifique-se de que o ID do objeto corresponde ao objeto ao qual o utilizador está a tentar atribuir a política de acesso. Muitas vezes haverá vários objetos no AAD que têm o mesmo nome, por isso escolher o correto é muito importante. Ao apagar e readicionar a política de acesso, é possível ver se existem múltiplos objetos com o mesmo nome.

Além disso, a maioria das políticas de acesso não requerem a utilização da "aplicação autorizada", como mostra o portal. O pedido autorizado é utilizado para cenários de autenticação "em nome de" em nome de "em nome", que são raros. 


## <a name="http-429-too-many-requests"></a>HTTP 429: Muitos pedidos

O estrangulamento ocorre quando o número de pedidos excede o máximo indicado para o prazo. Se ocorrer estrangulamento, a resposta do Cofre-Chave será HTTP 429. São indicados máximos para tipos de pedidos feitos. Por exemplo: a criação de uma chave HSM 2048-bit é de 5 pedidos por 10 segundos, mas todas as outras transações de HSM têm um limite de 1000 pedidos/10 segundos. Por conseguinte, é importante compreender que tipos de chamadas estão a ser feitas para determinar a causa da aceleração.
Em geral, os pedidos para o Cofre chave estão limitados a 2000 pedidos/10 segundos. Exceções são Operações-Chave, como documentado nos [limites](service-limits.md) de serviço key vault

### <a name="troubleshooting-429"></a>Resolução de problemas 429
O estrangulamento é trabalhado ao redor usando estas técnicas:

- Reduza o número de pedidos feitos ao Cofre chave, determinando se existem padrões para um recurso solicitado e tentando acache-los no pedido de chamada. 

- Quando ocorrer a aceleração do Cofre chave, adapte o código de solicitação para utilizar um backoff exponencial para retentar. O algoritmo é explicado aqui: [Como acelerar a sua app](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- Se o número de pedidos não puder ser reduzido por cache e recuo cronometrado não funcionar, então considere dividir as chaves em vários Cofres-Chave. O limite de serviço para uma única subscrição é 5x o limite individual do Cofre chave. Se utilizar mais de 5 Cofres-Chave, deve ser considerada a utilização de várias subscrições. 

Orientação detalhada, incluindo pedido de aumento de limites, pode ser encontrado aqui: [Orientação](overview-throttling.md) de estrangulamento do cofre chave


