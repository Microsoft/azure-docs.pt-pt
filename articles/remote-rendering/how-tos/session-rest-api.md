---
title: A gestão da sessão REST API
description: Descreve como gerir sessões
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 46560f067e020236031487677ad4f48a9560d4e1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681249"
---
# <a name="use-the-session-management-rest-api"></a>Utilize a gestão da sessão REST API

Para utilizar a funcionalidade de renderização remota Azure, é necessário criar uma *sessão*. Cada sessão corresponde a uma máquina virtual (VM) a ser atribuída no Azure e à espera que um dispositivo cliente se conecte. Quando um dispositivo se liga, o VM torna os dados solicitados e serve o resultado como um fluxo de vídeo. Durante a criação da sessão, escolheu o tipo de servidor que pretende executar, o que determina os preços. Uma vez que a sessão já não seja necessária, deve ser interrompida. Se não parar manualmente, será desligado automaticamente quando o tempo de *aluguer* da sessão expirar.

Fornecemos um script PowerShell no [repositório](https://github.com/Azure/azure-remote-rendering) de amostras DE ARR na pasta *Scripts,* chamada *RenderingSession.ps1,* que demonstra o uso do nosso serviço. O script e a sua configuração são descritos aqui: [Exemplo de scripts PowerShell](../samples/powershell-example-scripts.md)

> [!TIP]
> Os comandos PowerShell listados nesta página destinam-se a complementar-se mutuamente. Se executar todos os scripts em sequência dentro do mesmo pedido de comando PowerShell, eles vão construir em cima uns dos outros.

## <a name="regions"></a>Regiões

Consulte a [lista das regiões disponíveis](../reference/regions.md) para que os URLs base enviem os pedidos.

Para as amostras abaixo escolhemos a região *westus2*.

### <a name="example-script-choose-an-endpoint"></a>Exemplo script: Escolha um ponto final

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>Contas

Se não tiver uma conta de renderização remota, [crie uma](create-an-account.md). Cada recurso é identificado por uma *contaId*, que é usada ao longo da sessão APIs.

### <a name="example-script-set-accountid-and-accountkey"></a>Script de exemplo: Definir contaId e contaChave

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
```

## <a name="common-request-headers"></a>Cabeçalhos de pedido comum

* O cabeçalho *de Autorização* `Bearer TOKEN`deve ter`TOKEN`o valor de " ", onde " é o símbolo de autenticação [devolvido pelo Serviço De Token Seguro](tokens.md).

### <a name="example-script-request-a-token"></a>Exemplo script: Solicite um símbolo

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>Cabeçalhos de resposta comum

* O cabeçalho *MS-CV* pode ser usado pela equipa de produtos para rastrear a chamada dentro do serviço.

## <a name="create-a-session"></a>Criar uma sessão

Este comando cria uma sessão. Devolve a identificação da nova sessão. Precisa da identificação da sessão para todos os outros comandos.

| URI | Método |
|-----------|:-----------|
| /v1/contas/*contaId*/sessões/criar | POST |

**Órgão de pedido:**

* maxLeaseTime (timepan): um valor de tempo limite quando o VM será desativado automaticamente
* modelos (matriz): URLs de contentores de ativos para pré-carga
* tamanho (cadeia): o tamanho VM **("standard"** ou **"premium").** Consulte [limitações específicas do tamanho](../reference/limits.md#overall-number-of-polygons)do VM .

**Respostas:**

| Código de estado | Carga útil JSON | Comentários |
|-----------|:-----------|:-----------|
| 202 | - sessãoId: GUID | Êxito |

### <a name="example-script-create-a-session"></a>Exemplo script: Criar uma sessão

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/create" -Method Post -ContentType "application/json" -Body "{ 'maxLeaseTime': '4:0:0', 'models': [], 'size': 'standard' }" -Headers @{ Authorization = "Bearer $token" }
```

Exemplo de saída:

```PowerShell
StatusCode        : 202
StatusDescription : Accepted
Content           : {"sessionId":"d31bddca-dab7-498e-9bc9-7594bc12862f"}
RawContent        : HTTP/1.1 202 Accepted
                    MS-CV: 5EqPJ1VdTUakDJZc6/ZhTg.0
                    Content-Length: 52
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:17:50 GMT
                    Location: accounts/11111111-1111-1111-11...
Forms             : {}
Headers           : {[MS-CV, 5EqPJ1VdTUakDJZc6/ZhTg.0], [Content-Length, 52], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:17:50 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 52
```

### <a name="example-script-store-sessionid"></a>Script exemplo: Store sessionId

A resposta do pedido acima inclui uma **sessãoId,** que você precisa para todos os pedidos de acompanhamento.

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="update-a-session"></a>Atualizar uma sessão

Este comando atualiza os parâmetros de uma sessão. Atualmente só pode prolongar o tempo de arrendamento de uma sessão.

> [!IMPORTANT]
> O tempo de arrendamento é sempre dado como um tempo total desde o início da sessão. Isto significa que se criou uma sessão com um tempo de arrendamento de uma hora, e quiser prolongar o seu tempo de arrendamento por mais uma hora, tem de atualizar o seu maxLeaseTime para duas horas.

| URI | Método |
|-----------|:-----------|
| /v1/contas/*contaID*/sessões/*sessionId* | PATCH |

**Órgão de pedido:**

* maxLeaseTime (timepan): um valor de tempo limite quando o VM será desativado automaticamente

**Respostas:**

| Código de estado | Carga útil JSON | Comentários |
|-----------|:-----------|:-----------|
| 200 | | Êxito |

### <a name="example-script-update-a-session"></a>Script exemplo: Atualizar uma sessão

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Patch -ContentType "application/json" -Body "{ 'maxLeaseTime': '5:0:0' }" -Headers @{ Authorization = "Bearer $token" }
```

Exemplo de saída:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: Fe+yXCJumky82wuoedzDTA.0
                    Content-Length: 0
                    Date: Thu, 09 May 2019 16:27:31 GMT


Headers           : {[MS-CV, Fe+yXCJumky82wuoedzDTA.0], [Content-Length, 0], [Date, Thu, 09 May 2019 16:27:31 GMT]}
RawContentLength  : 0
```

## <a name="get-active-sessions"></a>Obtenha sessões ativas

Este comando devolve uma lista de sessões ativas.

| URI | Método |
|-----------|:-----------|
| /v1/contas/*contaId*/sessões | GET |

**Respostas:**

| Código de estado | Carga útil JSON | Comentários |
|-----------|:-----------|:-----------|
| 200 | - sessões: conjunto de propriedades da sessão | ver secção "Obter propriedades da sessão" para uma descrição das propriedades da sessão |

### <a name="example-script-query-active-sessions"></a>Script de exemplo: Consultas de sessões ativas

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Exemplo de saída:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : []
RawContent        : HTTP/1.1 200 OK
                    MS-CV: WfB9Cs5YeE6S28qYkp7Bhw.1
                    Content-Length: 15
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 25 Jul 2019 16:23:50 GMT

                    {"sessions":[]}
Forms             : {}
Headers           : {[MS-CV, WfB9Cs5YeE6S28qYkp7Bhw.1], [Content-Length, 2], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 25 Jul 2019 16:23:50 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 2
```

## <a name="get-sessions-properties"></a>Obtenha propriedades de sessões

Este comando devolve informações sobre uma sessão, como o seu nome de anfitrião VM.

| URI | Método |
|-----------|:-----------|
| /v1/contas/*contaId*/sessões/*sessionId*/propriedades | GET |

**Respostas:**

| Código de estado | Carga útil JSON | Comentários |
|-----------|:-----------|:-----------|
| 200 | - mensagem: corda<br/>- sessãoTimelapsedTime: timepan<br/>- sessãoNome anfitrião: corda<br/>- sessionId: string<br/>- sessãoMaxLeaseTime: timepan<br/>- sessãoTamanho: enum<br/>- sessãoEstatuto: enum | enum sessionStatus { início, pronto, paragem, parado, expirado, erro}<br/>Se o estado for 'error' ou 'expirado', a mensagem conterá mais informações |

### <a name="example-script-get-session-properties"></a>Script de exemplo: Obtenha propriedades da sessão

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId/properties" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Exemplo de saída:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {"message":null,"sessionElapsedTime":"00:00:01","sessionHostname":"5018fee8-817e-4366-9179-556af79a4240.remoterenderingvm.westeurope.mixedreality.azure.com","sessionId":"e13d2c44-63e0-4591-991e-f9e05e599a93","sessionMaxLeaseTime":"04:00:00","sessionStatus":"Ready"}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: CMXegpZRMECH4pbOW2j5GA.0
                    Content-Length: 60
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:30:38 GMT

                    {"message":null,...
Forms             : {}
Headers           : {[MS-CV, CMXegpZRMECH4pbOW2j5GA.0], [Content-Length, 60], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:30:38 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 60
```

## <a name="stop-a-session"></a>Pare uma sessão

Este comando para uma sessão. O VM atribuído será recuperado pouco depois.

| URI | Método |
|-----------|:-----------|
| /v1/contas/*contaId*/sessões/*sessionId* | DELETE |

**Respostas:**

| Código de estado | Carga útil JSON | Comentários |
|-----------|:-----------|:-----------|
| 204 | | Êxito |

### <a name="example-script-stop-a-session"></a>Exemplo script: Parar uma sessão

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Delete -Headers @{ Authorization = "Bearer $token" }
```

Exemplo de saída:

```PowerShell
StatusCode        : 204
StatusDescription : No Content
Content           : {}
RawContent        : HTTP/1.1 204 No Content
                    MS-CV: YDxR5/7+K0KstH54WG443w.0
                    Date: Thu, 09 May 2019 16:45:41 GMT


Headers           : {[MS-CV, YDxR5/7+K0KstH54WG443w.0], [Date, Thu, 09 May 2019 16:45:41 GMT]}
RawContentLength  : 0
```

## <a name="next-steps"></a>Passos seguintes

* [Exemplo de scripts PowerShell](../samples/powershell-example-scripts.md)
