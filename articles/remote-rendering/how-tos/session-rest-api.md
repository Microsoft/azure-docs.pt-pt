---
title: A gestão da sessão REST API
description: Descreve como gerir sessões
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: d957c5d6521010c7393e2297be16cd7bef41c35f
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724073"
---
# <a name="use-the-session-management-rest-api"></a>Utilizar a API REST de gestão de sessões

Para utilizar a funcionalidade de renderização remota Azure, precisa de criar uma *sessão*. Cada sessão corresponde a uma máquina virtual (VM) a ser alocado em Azure e à espera que um dispositivo cliente se conecte. Quando um dispositivo se liga, o VM presta os dados solicitados e serve o resultado como um fluxo de vídeo. Durante a criação da sessão, escolheu o tipo de servidor que pretende executar, o que determina o preço. Uma vez que a sessão não seja mais necessária, deve ser interrompida. Se não parar manualmente, será desligado automaticamente quando o tempo de *locação* da sessão expirar.

Fornecemos um script PowerShell no [repositório de amostras ARR](https://github.com/Azure/azure-remote-rendering) na pasta *Scripts,* chamada *RenderingSession.ps1*, o que demonstra o uso do nosso serviço. O script e a sua configuração são descritos aqui: [Exemplo PowerShell scripts](../samples/powershell-example-scripts.md)

> [!TIP]
> Os comandos PowerShell listados nesta página destinam-se a complementar-se mutuamente. Se executar todos os scripts em sequência dentro da mesma decisão de comando PowerShell, eles construirão em cima uns dos outros.

## <a name="regions"></a>Regiões

Consulte a [lista das regiões disponíveis](../reference/regions.md) para os URLs de base para enviar os pedidos para.

Para os scripts da amostra abaixo escolhemos a região *westus2.*

### <a name="example-script-choose-an-endpoint"></a>Script de exemplo: Escolha um ponto final

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>Contas

Se não tiver uma conta de renderização remota, [crie uma](create-an-account.md). Cada recurso é identificado por um *accountId*, que é usado ao longo da sessão APIs.

### <a name="example-script-set-accountid-accountkey-and-account-domain"></a>Script de exemplo: Definir contaD, contaKey e domínio de conta

O domínio da conta é a localização da conta de renderização remota. Neste exemplo, a localização da conta é região *leste.*

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
$accountDomain = "eastus.mixedreality.azure.com"
```

## <a name="common-request-headers"></a>Cabeçalhos de pedido comuns

* O cabeçalho *de autorização* deve ter o valor de " " onde " " é o símbolo `Bearer TOKEN` de `TOKEN` autenticação devolvido pelo Serviço [DeKen Seguro](tokens.md).

### <a name="example-script-request-a-token"></a>Roteiro de exemplo: Pedir um token

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.$accountDomain/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>Cabeçalhos de resposta comuns

* O *cabeçalho MS-CV* pode ser utilizado pela equipa do produto para rastrear a chamada dentro do serviço.

## <a name="create-a-session"></a>Criar uma sessão

Este comando cria uma sessão. Devolve a identificação da nova sessão. Precisa da identificação da sessão para todos os outros comandos.

| URI | Método |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/create | POST |

**Entidade de pedido:**

* maxLeaseTime (timepan): um valor de tempo limite quando a sessão será desativada automaticamente
* modelos (matriz): URLs de contentores de ativos para pré-carregar
* tamanho (cadeia): o tamanho do servidor para configurar [**("standard"**](../reference/vm-sizes.md) ou [**"premium").**](../reference/vm-sizes.md) Consulte [limitações de tamanho específicas.](../reference/limits.md#overall-number-of-polygons)

**Respostas:**

| Código de estado | Carga JSON | Comentários |
|-----------|:-----------|:-----------|
| 202 | - sessionId: GUID | Success |

### <a name="example-script-create-a-session"></a>Script de exemplo: Criar uma sessão

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

### <a name="example-script-store-sessionid"></a>Script de exemplo: Store sessionId

A resposta do pedido acima inclui uma **sessãoId,** que você precisa para todos os pedidos de acompanhamento.

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="modify-and-query-session-properties"></a>Modificar e consultar propriedades da sessão

Existem alguns comandos para consultar ou modificar os parâmetros das sessões existentes.

> [!CAUTION]
> No que diz respeito a todas as chamadas REST, o envio demasiado frequente destes comandos fará com que o servidor se ausse e retornará eventualmente. O código de estado neste caso é 429 ("muitos pedidos"). Em regra, deve haver um atraso de **5-10 segundos entre chamadas subsequentes**.

### <a name="update-session-parameters"></a>Atualizar parâmetros de sessão

Este comando atualiza os parâmetros de uma sessão. Atualmente só pode estender o tempo de arrendamento de uma sessão.

> [!IMPORTANT]
> O tempo de arrendamento é sempre dado como um tempo total desde o início da sessão. Isto significa que se criou uma sessão com um tempo de arrendamento de uma hora, e quer prolongar o seu tempo de arrendamento por mais uma hora, tem de atualizar o seu maxLeaseTime para duas horas.

| URI | Método |
|-----------|:-----------|
| /v1/accounts/*accountID*/sessions/sessionId | PATCH |

**Entidade de pedido:**

* maxLeaseTime (timepan): um valor de tempo limite quando a sessão será desativada automaticamente

**Respostas:**

| Código de estado | Carga JSON | Comentários |
|-----------|:-----------|:-----------|
| 200 | | Success |

#### <a name="example-script-update-a-session"></a>Script de exemplo: Atualizar uma sessão

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

### <a name="get-active-sessions"></a>Obtenha sessões ativas

Este comando devolve uma lista de sessões ativas.

| URI | Método |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions | GET |

**Respostas:**

| Código de estado | Carga JSON | Comentários |
|-----------|:-----------|:-----------|
| 200 | - sessões: variedade de propriedades de sessão | consulte a secção "Obter propriedades de sessão" para uma descrição das propriedades da sessão |

#### <a name="example-script-query-active-sessions"></a>Script de exemplo: Sessões ativas de consulta

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

### <a name="get-sessions-properties"></a>Obtenha propriedades de sessões

Este comando retorna informações sobre uma sessão, como o seu nome de anfitrião VM.

| URI | Método |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/*sessionId*/properties | GET |

**Respostas:**

| Código de estado | Carga JSON | Comentários |
|-----------|:-----------|:-----------|
| 200 | - mensagem: corda<br/>- sessãoElapsedTime: timepan<br/>- sessãoOstname: corda<br/>- sessionId: string<br/>- sessãoMaxLeaseTime: timepan<br/>- sessãoSize: enum<br/>- sessionStatus: enum | enum sessionStatus { começando, pronto, parando, parado, expirado, erro}<br/>Se o estado for 'erro' ou 'expirado', a mensagem conterá mais informações |

#### <a name="example-script-get-session-properties"></a>Script de exemplo: Obtenha propriedades de sessão

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

## <a name="stop-a-session"></a>Parar uma sessão

Este comando para uma sessão. O VM atribuído será recuperado pouco depois.

| URI | Método |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/sessionId | DELETE |

**Respostas:**

| Código de estado | Carga JSON | Comentários |
|-----------|:-----------|:-----------|
| 204 | | Success |

### <a name="example-script-stop-a-session"></a>Roteiro de exemplo: Parar uma sessão

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

* [Scripts do PowerShell de exemplo](../samples/powershell-example-scripts.md)
