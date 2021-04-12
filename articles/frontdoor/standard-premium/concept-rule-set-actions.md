---
title: Configurar ações de regra padrão/premium da porta frontal configure
description: Este artigo fornece uma lista das várias ações que pode fazer com o conjunto de regras da Porta frontal Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: yuajia
ms.openlocfilehash: e4698a1c1576d15042dd050e0123b83dba39a3e3
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064796"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-actions"></a>Azure Front Door Standard/Premium (Pré-visualização) Regra Definida ações

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

Um [conjunto](concept-rule-set.md) de regras standard/premium da porta frontal Azure consiste em regras com uma combinação de condições e ações de jogo. Este artigo fornece uma descrição detalhada das ações que pode usar no Conjunto de Regras Standard/Premium da Porta Frontal Azure. A ação define o comportamento que é aplicado a um tipo de pedido que uma(s) condição de correspondência(s) identifica. Num Conjunto de Regras da Porta Frontal Azure (Standard/Premium), uma regra pode conter até cinco ações.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

As seguintes ações estão disponíveis para utilização no conjunto de regras da Porta Frontal Azure.  

## <a name="cache-expiration"></a><a name="CacheExpiration"></a> Expiração da cache

Utilize a ação **de validade** da cache para substituir o valor do ponto final (TTL) do ponto final para pedidos que as regras correspondem às condições.

> [!NOTE]
> As origens podem especificar para não cache respostas específicas utilizando o `Cache-Control` cabeçalho com um valor de `no-cache` , `private` ou `no-store` . Nestas circunstâncias, a Porta Frontal nunca cacheá o conteúdo e esta ação não terá qualquer efeito.

### <a name="properties"></a>Propriedades

| Propriedade | Valores suportados |
|-------|------------------|
| Comportamento da cache | <ul><li>**Cache de bypass:** O conteúdo não deve ser em cache. Nos modelos ARM, desate a `cacheBehavior` propriedade para `BypassCache` .</li><li>**Sobreposição:** O valor TTL devolvido da sua origem é substituído com o valor especificado na ação. Este comportamento só será aplicado se a resposta for cacheable. Nos modelos ARM, desate a `cacheBehavior` propriedade para `Override` .</li><li>**Conjunto se faltar:** Se nenhum valor TTL for devolvido da sua origem, a regra define o TTL ao valor especificado na ação. Este comportamento só será aplicado se a resposta for cacheable. Nos modelos ARM, desate a `cacheBehavior` propriedade para `SetIfMissing` .</li></ul> |
| Duração da cache | Quando _o comportamento da Cache_ estiver definido para `Override` `Set if missing` ou, estes campos devem especificar a duração da cache a utilizar. A duração máxima é de 366 dias.<ul><li>No portal Azure: especifique os dias, horas, minutos e segundos.</li><li>Nos modelos ARM: especifique a duração do formato `d.hh:mm:ss` . |

### <a name="example"></a>Exemplo

Neste exemplo, sobrepõem-se à expiração da cache para 6 horas, para pedidos compatíveis que já não especificam uma duração de cache.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/cache-expiration.png" alt-text="Imagem de portal mostrando ação de expiração da cache.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheExpiration",
  "parameters": {
    "cacheBehavior": "SetIfMissing",
    "cacheType": "All",
    "cacheDuration": "0.06:00:00",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheExpiration'
  parameters: {
    cacheBehavior: 'SetIfMissing'
    cacheType: All
    cacheDuration: '0.06:00:00'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters'
  }
}
```

---

## <a name="cache-key-query-string"></a><a name="CacheKeyQueryString"></a> Cadeia de consulta chave cache

Utilize a ação de **cadeia de perguntas à chave** de cache para modificar a chave cache com base em cadeias de consulta. A chave de cache é a forma como a Porta frontal identifica pedidos únicos de cache.

### <a name="properties"></a>Propriedades

| Propriedade | Valores suportados |
|-------|------------------|
| Comportamento | <ul><li>**Incluir:** As cadeias de consulta especificadas nos parâmetros são incluídas quando a chave de cache é gerada. Nos modelos ARM, desate a `queryStringBehavior` propriedade para `Include` .</li><li>**Cache cada URL único:** Cada URL único tem a sua própria chave cache. Nos modelos ARM, utilize o `queryStringBehavior` de `IncludeAll` .</li><li>**Excluir:** As cadeias de consulta especificadas nos parâmetros são excluídas quando a tecla de cache é gerada. Nos modelos ARM, desate a `queryStringBehavior` propriedade para `Exclude` .</li><li>**Ignore as cordas de consulta:** As cordas de consulta não são consideradas quando a chave de cache é gerada. Nos modelos ARM, desate a `queryStringBehavior` propriedade para `ExcludeAll` .</li></ul>  |
| Parâmetros | A lista de nomes de parâmetros de cadeia de consulta, separados por vírgulas. |

### <a name="example"></a>Exemplo

Neste exemplo, modificamos a chave de cache para incluir um parâmetro de cadeia de consulta chamado `customerId` .

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/cache-key-query-string.png" alt-text="Screenshot do portal mostrando ação de cadeia de consulta de chave de cache.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheKeyQueryString",
  "parameters": {
    "queryStringBehavior": "Include",
    "queryParameters": "customerId",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheKeyQueryString'
  parameters: {
    queryStringBehavior: 'Include'
    queryParameters: 'customerId'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters'
  }
}
```

---

## <a name="modify-request-header"></a><a name="ModifyRequestHeader"></a> Modificar o cabeçalho do pedido

Utilize a ação do **cabeçalho do pedido de modificação** para modificar os cabeçalhos do pedido quando este for enviado para a sua origem.

### <a name="properties"></a>Propriedades

| Propriedade | Valores suportados |
|-------|------------------|
| Operador | <ul><li>**Apêndice:** O cabeçalho especificado é adicionado ao pedido com o valor especificado. Se o cabeçalho já estiver presente, o valor é anexado ao valor do cabeçalho existente utilizando a concatenação das cordas. Não são adicionados delimiters. Nos modelos ARM, utilize o `headerAction` de `Append` .</li><li>**Sobrepor:** O cabeçalho especificado é adicionado ao pedido com o valor especificado. Se o cabeçalho já estiver presente, o valor especificado substitui o valor existente. Nos modelos ARM, utilize o `headerAction` de `Overwrite` .</li><li>**Excluir:** Se o cabeçalho especificado na regra estiver presente, o cabeçalho é apagado do pedido. Nos modelos ARM, utilize o `headerAction` de `Delete` .</li></ul> |
| Nome do cabeçalho | O nome do cabeçalho para modificar. |
| Valor do cabeçalho | O valor para anexar ou substituir. |

### <a name="example"></a>Exemplo

Neste exemplo, anexamos o valor `AdditionalValue` ao cabeçalho do `MyRequestHeader` pedido. Se a origem definir o cabeçalho de resposta para um valor de `ValueSetByClient` , então após esta ação ser aplicada, o cabeçalho de pedido teria um valor de `ValueSetByClientAdditionalValue` .

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/modify-request-header.png" alt-text="Imagem de imagem do portal mostrando modificar a ação do cabeçalho do pedido.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyRequestHeader",
  "parameters": {
    "headerAction": "Append",
    "headerName": "MyRequestHeader",
    "value": "AdditionalValue",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyRequestHeader'
  parameters: {
    headerAction: 'Append'
    headerName: 'MyRequestHeader'
    value: 'AdditionalValue'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="modify-response-header"></a><a name="ModifyResponseHeader"></a> Modificar o cabeçalho de resposta

Utilize a ação do **cabeçalho de resposta** de modificação para modificar os cabeçalhos que estão presentes em respostas antes de serem devolvidos aos seus clientes.

### <a name="properties"></a>Propriedades

| Propriedade | Valores suportados |
|-------|------------------|
| Operador | <ul><li>**Apêndice:** O cabeçalho especificado é adicionado à resposta com o valor especificado. Se o cabeçalho já estiver presente, o valor é anexado ao valor do cabeçalho existente utilizando a concatenação das cordas. Não são adicionados delimiters. Nos modelos ARM, utilize o `headerAction` de `Append` .</li><li>**Sobrepor:** O cabeçalho especificado é adicionado à resposta com o valor especificado. Se o cabeçalho já estiver presente, o valor especificado substitui o valor existente. Nos modelos ARM, utilize o `headerAction` de `Overwrite` .</li><li>**Excluir:** Se o cabeçalho especificado na regra estiver presente, o cabeçalho é apagado da resposta.  Nos modelos ARM, utilize o `headerAction` de `Delete` .</li></ul> |
| Nome do cabeçalho | O nome do cabeçalho para modificar. |
| Valor do cabeçalho | O valor para anexar ou substituir. |

### <a name="example"></a>Exemplo

Neste exemplo, apagamos o cabeçalho com o nome `X-Powered-By` das respostas antes de serem devolvidos ao cliente.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/modify-response-header.png" alt-text="Imagem de portal mostrando modificar a ação do cabeçalho de resposta.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyResponseHeader",
  "parameters": {
    "headerAction": "Delete",
    "headerName": "X-Powered-By",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyResponseHeader'
  parameters: {
    headerAction: 'Delete'
    headerName: 'X-Powered-By'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="url-redirect"></a><a name="UrlRedirect"></a> Redirecionamento de URL

Utilize a ação **de redirecionamento de URL** para redirecionar os clientes para um novo URL. Os clientes são enviados uma resposta de redirecionamento da Porta da Frente.

### <a name="properties"></a>Propriedades

| Propriedade | Valores suportados |
|----------|------------------|
| Tipo de redirecionamento | O tipo de resposta para voltar ao solicitador. <ul><li>No portal Azure: Encontrado (302), Movido (301), Redirecionamento Temporário (307), Redirecionamento Permanente (308).</li><li>Nos modelos ARM: `Found` , `Moved` , `TemporaryRedirect``PermanentRedirect`</li></ul> |
| Protocolo de redirecionamento | <ul><li>No portal Azure: `Match Request` , `HTTP``HTTPS`</li><li>Nos modelos ARM: `MatchRequest` , `Http``Https`</li></ul> |
| Anfitrião do destino | O nome do anfitrião para o seguinte, pretende que o pedido seja redirecionado para. Deixe em branco para preservar o hospedeiro que está a chegar. |
| Caminho de destino | O caminho a utilizar no redirecionamento. Inclua o `/` líder. Deixe em branco para preservar o caminho de entrada. |
| Cadeias de consulta | A cadeia de consulta usada no redirecionamento. Não inclua o `?` líder. Deixe em branco para preservar a cadeia de consulta de entrada. |
| Fragmento de destino | O fragmento para usar no redirecionamento. Deixe em branco para preservar o fragmento de entrada. |

### <a name="example"></a>Exemplo

Neste exemplo, redirecionamos o pedido `https://contoso.com/exampleredirection?clientIp={client_ip}` para, preservando o fragmento. É utilizado um redirecionamento temporário HTTP (307). O endereço IP do cliente é utilizado no lugar do `{client_ip}` símbolo dentro do URL utilizando a `client_ip` [variável](#server-variables)do servidor .

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/url-redirect.png" alt-text="Screenshot do portal mostrando ação de redirecionamento de URL.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRedirect",
  "parameters": {
    "redirectType": "TemporaryRedirect",
    "destinationProtocol": "Https",
    "customHostname": "contoso.com",
    "customPath": "/exampleredirection",
    "customQueryString": "clientIp={client_ip}",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRedirect'
  parameters: {
    redirectType: 'TemporaryRedirect'
    destinationProtocol: 'Https'
    customHostname: 'contoso.com'
    customPath: '/exampleredirection'
    customQueryString: 'clientIp={client_ip}'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters'
  }
}
```

---

## <a name="url-rewrite"></a><a name="UrlRewrite"></a> REescrita de URL

Use a ação **de reescrita de URL** para reescrever o caminho de um pedido que está a caminho da sua origem.

### <a name="properties"></a>Propriedades

| Propriedade | Valores suportados |
|----------|------------------|
| Padrão de origem | Defina o padrão de origem no caminho URL para substituir. Atualmente, o padrão de origem usa uma correspondência baseada em prefixos. Para combinar todos os caminhos de URL, utilize um corte para a frente `/` () como o valor do padrão de origem. |
| Destino | Defina o caminho de destino a utilizar na reescrita. O caminho de destino substitui o padrão de origem. |
| Preservar caminho inigualável | Se definido para _Sim,_ o caminho restante após o padrão de origem é anexado à nova rota de destino. |

### <a name="example"></a>Exemplo

Neste exemplo, reescrevemos todos os pedidos para o `/redirection` caminho, e não preservamos o resto do caminho.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/url-rewrite.png" alt-text="Screenshot do portal mostrando ação de reescrita de URL.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRewrite",
  "parameters": {
    "sourcePattern": "/",
    "destination": "/redirection",
    "preserveUnmatchedPath": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRewrite'
  parameters: {
    sourcePattern: '/'
    destination: '/redirection'
    preserveUnmatchedPath: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters'
  }
}
```

---

## <a name="server-variables"></a>Variáveis de servidor

As variáveis do servidor Rule set fornecem acesso a informações estruturadas sobre o pedido. Pode utilizar variáveis de servidor para alterar dinamicamente os cabeçalhos de pedido/resposta ou os caminhos/cadeias de consulta URL, por exemplo, quando uma nova carga de página ou quando um formulário é publicado.

### <a name="supported-variables"></a>Variáveis suportadas

| Nome da variável    | Description                                                                                                                                                                                                                                                                               |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `socket_ip`      | O endereço IP da ligação direta à borda da porta frontal Azure. Se o cliente usou um representante HTTP ou um equilibrador de carga para enviar o pedido, o valor de `socket_ip` é o endereço IP do proxy ou do equilibrador de carga.                                                                      |
| `client_ip`      | O endereço IP do cliente que fez o pedido original. Se houve um `X-Forwarded-For` cabeçalho no pedido, então o endereço IP do cliente é escolhido a partir do cabeçalho.                                                                                                               |
| `client_port`    | A porta IP do cliente que fez o pedido.                                                                                                                                                                                                                                          |
| `hostname`       | O nome do anfitrião no pedido do cliente.                                                                                                                                                                                                                                             |
| `geo_country`    | Indica o país/região de origem do solicitador através do seu código país/região.                                                                                                                                                                                                       |
| `http_method`    | O método utilizado para fazer o pedido de URL, tais como `GET` `POST` ou .                                                                                                                                                                                                                         |
| `http_version`   | O protocolo de pedido. `HTTP/1.0` `HTTP/1.1` Normalmente, ou . `HTTP/2.0` .                                                                                                                                                                                                                      |
| `query_string`   | A lista de pares variáveis/valor que segue o "?" na URL solicitada.<br />Por exemplo, no `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` pedido, o `query_string` valor será `id=123&title=fabrikam` .                                                      |
| `request_scheme` | O regime de pedidos: `http` ou `https` .                                                                                                                                                                                                                                                    |
| `request_uri`    | O pedido original completo URI (com argumentos).<br />Por exemplo, no `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` pedido, o `request_uri` valor será `/article.aspx?id=123&title=fabrikam` .                                                                     |
| `ssl_protocol`   | O protocolo de uma ligação TLS estabelecida.                                                                                                                                                                                                                                            |
| `server_port`    | A porta do servidor que aceitou um pedido.                                                                                                                                                                                                                                           |
| `url_path`       | Identifica o recurso específico no anfitrião a que o cliente web quer aceder. Esta é a parte do pedido URI sem os argumentos.<br />Por exemplo, no `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` pedido, o `uri_path` valor será `/article.aspx` . |

### <a name="server-variable-format"></a>Formato variável do servidor    

As variáveis do servidor podem ser especificadas utilizando os seguintes formatos:

* `{variable}`: Incluir toda a variável do servidor. Por exemplo, se o endereço IP do cliente `111.222.333.444` for, então o `{client_ip}` token avaliará para `111.222.333.444` .
* `{variable:offset}`: Inclua a variável do servidor após uma compensação específica, até ao final da variável. A compensação é baseada em zero. Por exemplo, se o endereço IP do cliente `111.222.333.444` for, então o `{client_ip:3}` token avaliará para `.222.333.444` .
* `{variable:offset:length}`: Inclua a variável do servidor após uma compensação específica, até ao comprimento especificado. A compensação é baseada em zero. Por exemplo, se o endereço IP do cliente `111.222.333.444` for, então o `{client_ip:4:3}` token avaliará para `222` .

### <a name="supported-actions"></a>Ações suportadas

As variáveis do servidor são suportadas nas seguintes ações:

* Cadeia de consulta chave cache
* Modificar o cabeçalho do pedido
* Modificar o cabeçalho de resposta
* Redirecionamento de URL
* Reescrever URL

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o conjunto de regras Standard/Premium Da porta dianteira do Azure.](concept-rule-set.md)
* Saiba mais sobre [as condições de jogo do Conjunto de Regras](concept-rule-set-match-conditions.md).
