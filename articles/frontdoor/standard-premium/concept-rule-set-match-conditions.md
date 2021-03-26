---
title: Configurar Azure Front Door Standard/Premium regra definida condições de jogo
description: Este artigo fornece uma lista das várias condições de jogo disponíveis com o conjunto de regras Azure Front Door Standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/24/2021
ms.author: yuajia
ms.openlocfilehash: 039effb885463c1c53085535a6980601be890340
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561514"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Azure Front Door Standard/Premium (Pré-visualização) Regra Definida condições de jogo

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

No Conjunto de [Normas/Regras](concept-rule-set.md)Premium da Porta Frontal azul, uma regra consiste em zero ou mais condições de jogo e uma ação. Este artigo fornece descrições detalhadas das condições de jogo que pode utilizar no Conjunto de Regras Standard/Premium da Porta Frontal Azure.

A primeira parte de uma regra é uma condição de jogo ou um conjunto de condições de jogo. Uma regra pode consistir em até 10 condições de jogo. Uma condição de correspondência identifica tipos específicos de pedidos para os quais são feitas ações definidas. Se utilizar várias condições de correspondência, as condições de jogo são agrupadas utilizando a lógica E. Para todas as condições de correspondência que suportam múltiplos valores, a lógica OR é utilizada.

Pode utilizar uma condição de correspondência para:

* Filtrar pedidos com base num endereço IP específico, país ou região.
* Filtrar os pedidos por informação do cabeçalho.
* Filtrar pedidos de dispositivos móveis ou dispositivos de ambiente de trabalho.
* Filtrar pedidos do nome do ficheiro de pedido e extensão do ficheiro.
* Filtrar pedidos de SOLICITAÇÃO, protocolo, caminho, cadeia de consulta, pós-args, etc.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="device-type"></a><a name="IsDevice"></a> Tipo de dispositivo

Utilize a condição de correspondência **do tipo do dispositivo** para identificar os pedidos que tenham sido feitos a partir de um dispositivo móvel ou de um dispositivo de ambiente de trabalho.  

### <a name="properties"></a>Propriedades

| Propriedade | Valores suportados |
|-------|------------------|
| Operador | <ul><li>No portal Azure: `Equal``Not Equal`</li><li>Nos modelos ARM: `Equal` ; use a propriedade para `negateCondition` especificar Não _Igual_ |
| Valor | `Mobile`, `Desktop` |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todos os pedidos que tenham sido detetados como provenientes de um dispositivo móvel.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/device-type.png" alt-text="Imagem de imagem do portal mostrando a condição de correspondência do tipo do dispositivo.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "IsDevice",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "Mobile"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'IsDevice'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'Mobile'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters'
  }
}
```

---

## <a name="post-args"></a><a name="PostArgs"></a> Post args

Utilize a condição de correspondência **pós-args** para identificar pedidos com base nos argumentos apresentados no corpo de um pedido de CORREIO. Uma única condição de jogo corresponde a um único argumento do corpo do pedido do POST. Pode especificar vários valores para combinar, que serão combinados usando a lógica OR.

> [!NOTE]
> A condição de correspondência **pós-args** funciona com o `application/x-www-form-urlencoded` tipo de conteúdo.

### <a name="properties"></a>Propriedades

| Propriedade | Valores suportados |
|-|-|
| Post args | Um valor de corda que representa o nome do argumento DO POST. |
| Operador | Qualquer operador da [lista de operadores padrão.](#operator-list) |
| Valor | Um ou mais valores de corda ou inteiro representando o valor do argumento POST a combinar. Se forem especificados vários valores, são avaliados utilizando a lógica OR. |
| Transformação de caso | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todos os pedidos postais em que `customerName` um argumento é apresentado no órgão de pedido, e onde o valor de começa com a letra ou `customerName` `J` `K` . Usamos uma caixa de transformação para converter os valores de entrada em maiúsculas para que os valores comecem com `J` , e sejam todos `j` `K` `k` compatíveis.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/post-args.png" alt-text="Imagem de portal mostrando a condição de correspondência de args post.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "PostArgs",
  "parameters": {
    "selector": "customerName",
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
        "J",
        "K"
    ],
    "transforms": [
        "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'PostArgs'
  parameters: {
    selector: 'customerName'
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'J'
      'K'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters'
  }
}
```

---

## <a name="query-string"></a><a name="QueryString"></a> Cadeia de consulta

Utilize a condição **de combinação de cordas de consulta** para identificar pedidos que contenham uma cadeia de consulta específica. Pode especificar vários valores para combinar, que serão combinados usando a lógica OR.

> [!NOTE]
> Toda a cadeia de consulta é compatível com uma única corda, sem a liderança `?` .

### <a name="properties"></a>Propriedades

| Propriedade | Valores suportados |
|-|-|
| Operador | Qualquer operador da [lista de operadores padrão.](#operator-list) |
| Cadeias de consulta | Um ou mais valores de corda ou inteiro representando o valor da cadeia de consulta a combinar. Não inclua `?` o no início da cadeia de consultas. Se forem especificados vários valores, são avaliados utilizando a lógica OR. |
| Transformação de caso | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todos os pedidos em que a cadeia de consulta contém a `language=en-US` corda. Queremos que a condição de jogo seja sensível a casos, para não transformarmos o caso.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/query-string.png" alt-text="Screenshot do portal mostrando a condição de jogo de linha de consulta.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "QueryString",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "language=en-US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'QueryString'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'language=en-US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters'
  }
}
```

---

## <a name="remote-address"></a><a name="RemoteAddress"></a> Endereço remoto

A condição de correspondência **de endereço remoto** identifica pedidos com base na localização ou endereço IP do solicitador. Pode especificar vários valores para combinar, que serão combinados usando a lógica OR.

* Utilize a notação CIDR ao especificar os blocos de endereços IP. Isto significa que a sintaxe para um bloco de endereços IP é o endereço IP base seguido de um corte dianteiro e o tamanho do prefixo. Por exemplo:
    * **IPv4 exemplo**: `5.5.5.64/26` corresponde a todos os pedidos que cheguem dos endereços 5.5.5.64 até 5.5.5.127.
    * **IPv6 exemplo**: `1:2:3:/48` corresponde a qualquer pedido que chegue de endereços 1:2:3:0:0:0:0:0:0:0:0:0:0:3: ffff:ff:ff:ff:ff.ff.
* Quando especifica vários endereços IP e blocos de endereços IP, aplica-se a lógica 'OR'.
    * **Exemplo do IPv4**: se adicionar dois endereços IP `1.2.3.4` `10.20.30.40` e, a condição é correspondida para quaisquer pedidos que cheguem de qualquer endereço 1.2.3.4 ou 10.20.30.40.
    * **IPv6 exemplo**: se adicionar dois endereços IP `1:2:3:4:5:6:7:8` e , a condição é `10:20:30:40:50:60:70:80` correspondida para quaisquer pedidos que cheguem de qualquer endereço 1:2:3:4:5:6:7:8 ou 10:20:30:40:50:60:70.80.

### <a name="properties"></a>Propriedades

| Propriedade | Valores suportados |
|-|-|
| Operador | <ul><li>No portal Azure: `Geo Match` `Geo Not Match` , `IP Match` ou `IP Not Match`</li><li>Nos modelos ARM: `GeoMatch` , ; usar a propriedade para `IPMatch` `negateCondition` especificar Geo Not _Match_ ou IP _Not Match_</li></ul> |
| Valor | <ul><li>Para os `IP Match` `IP Not Match` operadores: especifique uma ou mais gamas de endereços IP. Se forem especificados vários intervalos de endereços IP, são avaliados utilizando a lógica OR.</li><li>Para os `Geo Match` `Geo Not Match` operadores: especifique uma ou mais localizações utilizando o seu código de país.</li></ul> |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todos os pedidos em que o pedido não tenha tido origem nos Estados Unidos.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/remote-address.png" alt-text="Imagem de portal mostrando a condição de correspondência de endereço remoto.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RemoteAddress",
  "parameters": {
    "operator": "GeoMatch",
    "negateCondition": true,
    "matchValues": [
      "US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RemoteAddress'
  parameters: {
    operator: 'GeoMatch'
    negateCondition: true
    matchValues: [
      'US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters'
  }
}
```

---

## <a name="request-body"></a><a name="RequestBody"></a> Corpo de pedido

A condição de correspondência **do corpo de pedido** identifica pedidos baseados em texto específico que aparece no corpo do pedido. Pode especificar vários valores para combinar, que serão combinados usando a lógica OR.

> [!NOTE]
> Se um corpo de pedido exceder 64KB de tamanho, apenas o primeiro 64KB será considerado para a condição de correspondência **do corpo pedido.**

### <a name="properties"></a>Propriedades

| Propriedade | Valores suportados |
|-|-|
| Operador | Qualquer operador da [lista de operadores padrão.](#operator-list) |
| Valor | Um ou mais valores de corda ou inteiro representando o valor do texto do corpo do pedido para combinar. Se forem especificados vários valores, são avaliados utilizando a lógica OR. |
| Transformação de caso | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todos os pedidos em que o corpo de pedido contém a `ERROR` corda. Transformamos o corpo de pedido em maiúscula antes de avaliar a partida, por isso `error` e outras variações de caso também desencadearão esta condição de jogo.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-body.png" alt-text="Imagem de portal mostrando condição de correspondência do corpo de pedido.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestBody",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "ERROR"
    ],
    "transforms": [
      "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestBody'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'ERROR'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters'
  }
}
```

---

## <a name="request-file-name"></a><a name="UrlFileName"></a> Solicitar nome de ficheiro

A condição de correspondência **de nome de ficheiro de pedido** identifica pedidos que incluem o nome de ficheiro especificado no URL de pedido. Pode especificar vários valores para combinar, que serão combinados usando a lógica OR.

### <a name="properties"></a>Propriedades

| Propriedade | Valores suportados |
|-|-|
| Operador | Qualquer operador da [lista de operadores padrão.](#operator-list) |
| Valor | Um ou mais valores de cadeia ou inteiro representando o valor do nome do ficheiro pedido para combinar. Se forem especificados vários valores, são avaliados utilizando a lógica OR. |
| Transformação de caso | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todos os pedidos em que o nome do ficheiro de pedido é `media.mp4` . Transformamos o nome do ficheiro em minúsculas antes de avaliar a partida, pelo `MEDIA.MP4` que e outras variações de caso também desencadearão esta condição de correspondência.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-name.png" alt-text="Screenshot do portal mostrando a condição de correspondência do nome do ficheiro do pedido.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileName",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "media.mp4"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileName'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'media.mp4'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters'
  }
}
```

---

## <a name="request-file-extension"></a><a name="UrlFileExtension"></a> Pedido de extensão de ficheiro

A condição **de extensão do ficheiro de pedido** identifica pedidos que incluem a extensão de ficheiro especificada no nome do ficheiro no URL do pedido. Pode especificar vários valores para combinar, que serão combinados usando a lógica OR.

> [!NOTE]
> Não inclua um período de liderança. Por exemplo, utilize `html` em vez de `.html`.

### <a name="properties"></a>Propriedades

| Propriedade | Valores suportados |
|-|-|
| Operador | Qualquer operador da [lista de operadores padrão.](#operator-list) |
| Valor | Um ou mais valores de cadeia ou inteiro representando o valor da extensão do ficheiro pedido para corresponder. Não inclua um período de liderança. Se forem especificados vários valores, são avaliados utilizando a lógica OR. |
| Transformação de caso | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todos os pedidos em que a extensão do ficheiro de pedido é `pdf` ou `docx` . Transformamos a extensão do ficheiro de pedido para minúsculas antes de avaliar a partida, por `PDF` `DocX` isso, e outras variações de caso também desencadearão esta condição de jogo.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-extension.png" alt-text="Screenshot do portal mostrando condição de correspondência de extensão de ficheiro de pedido.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileExtension",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "pdf",
      "docx"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters"
  }
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileExtension'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'pdf'
      'docx'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters'
  }
}
```

---

## <a name="request-header"></a><a name="RequestHeader"></a> Cabeçalho de pedido

A condição de correspondência **do cabeçalho do pedido** identifica pedidos que incluem um cabeçalho específico no pedido. Pode utilizar esta condição de jogo para verificar se existe um cabeçalho qualquer que seja o seu valor, ou para verificar se o cabeçalho corresponde a um valor especificado. Pode especificar vários valores para combinar, que serão combinados usando a lógica OR.

### <a name="properties"></a>Propriedades

| Propriedade | Valores suportados |
|-|-|
| Nome do cabeçalho | Um valor de corda que representa o nome do argumento DO POST. |
| Operador | Qualquer operador da [lista de operadores padrão.](#operator-list) |
| Valor | Um ou mais valores de corda ou inteiro representando o valor do cabeçalho pedido para corresponder. Se forem especificados vários valores, são avaliados utilizando a lógica OR. |
| Transformação de caso | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todos os pedidos em que o pedido contém um cabeçalho `MyCustomHeader` nomeado, independentemente do seu valor.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-header.png" alt-text="Imagem de imagem do portal mostrando condição de correspondência do cabeçalho do pedido.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestHeader",
  "parameters": {
    "selector": "MyCustomHeader",
    "operator": "Any",
    "negateCondition": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestHeader'
  parameters: {
    selector: 'MyCustomHeader',
    operator: 'Any'
    negateCondition: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters'
  }
}
```

---

## <a name="request-method"></a><a name="RequestMethod"></a> Método de pedido

A condição de correspondência **do método de pedido** identifica os pedidos que utilizam o método de pedido HTTP especificado. Pode especificar vários valores para combinar, que serão combinados usando a lógica OR.

### <a name="properties"></a>Propriedades

| Propriedade | Valores suportados |
|-|-|
| Operador | <ul><li>No portal Azure: `Equal``Not Equal`</li><li>Nos modelos ARM: `Equal` ; use a propriedade para `negateCondition` especificar Não _Igual_ |
| Método de pedido | Um ou mais métodos HTTP de: `GET` , , , , , , , . . `POST` `PUT` `DELETE` `HEAD` `OPTIONS` `TRACE` . Se forem especificados vários valores, são avaliados utilizando a lógica OR. |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todos os pedidos em que o pedido utiliza o `DELETE` método.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-method.png" alt-text="Screenshot do portal mostrando condição de correspondência do método de pedido.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestMethod",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "DELETE"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestMethod'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'DELETE
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters'
  }
}
```

---

## <a name="request-path"></a><a name="UrlPath"></a> Caminho de pedido

A condição de correspondência **do percurso de pedido** identifica pedidos que incluem o caminho especificado no URL de pedido. Pode especificar vários valores para combinar, que serão combinados usando a lógica OR.

> [!NOTE]
> O caminho é a parte do URL após o nome de anfitrião e um corte. Por exemplo, no `https://www.contoso.com/files/secure/file1.pdf` URL, o caminho é `files/secure/file1.pdf` .

### <a name="properties"></a>Propriedades

| Propriedade | Valores suportados |
|-|-|
| Operador | Qualquer operador da [lista de operadores padrão.](#operator-list) |
| Valor | Um ou mais valores de corda ou inteiro representando o valor do caminho de pedido para combinar. Não inclua o corte principal. Se forem especificados vários valores, são avaliados utilizando a lógica OR. |
| Transformação de caso | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todos os pedidos onde o caminho do ficheiro de pedido começa com `files/secure/` . Transformamos a extensão do ficheiro de pedido para minúsculas antes de avaliar a correspondência, pelo que os pedidos `files/SECURE/` e outras variações de caso também desencadearão esta condição de correspondência.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-path.png" alt-text="Imagem de portal mostrando condição de correspondência de trajetória de pedido.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlPath",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "files/secure/"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlPath'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'files/secure/'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters'
  }
}
```

---

## <a name="request-protocol"></a><a name="RequestScheme"></a> Protocolo de pedido

A condição de correspondência **do protocolo de pedido** identifica os pedidos que utilizam o protocolo especificado (HTTP ou HTTPS).

> [!NOTE]
> *O protocolo* às vezes também é chamado *de esquema.*

### <a name="properties"></a>Propriedades

| Propriedade | Valores suportados |
|-|-|
| Operador | <ul><li>No portal Azure: `Equal``Not Equal`</li><li>Nos modelos ARM: `Equal` ; use a propriedade para `negateCondition` especificar Não _Igual_ |
| Método de pedido | `HTTP`, `HTTPS` |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todos os pedidos em que o pedido utiliza o `HTTP` protocolo.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-protocol.png" alt-text="Imagem de imagem do portal mostrando condição de correspondência do protocolo de pedido.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestScheme",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "HTTP"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestScheme'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'HTTP
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters'
  }
}
```

---

## <a name="request-url"></a><a name="RequestUrl"></a> URL de pedido

Identifica pedidos que correspondam à URL especificada. Toda a URL é avaliada. Pode especificar vários valores para combinar, que serão combinados usando a lógica OR.

> [!TIP]
> Quando utilizar esta condição de regra, certifique-se de incluir o protocolo. Por exemplo, use `https://www.contoso.com` em vez de `www.contoso.com` apenas.

### <a name="properties"></a>Propriedades

| Propriedade | Valores suportados |
|-|-|
| Operador | Qualquer operador da [lista de operadores padrão.](#operator-list) |
| Valor | Um ou mais valores de corda ou inteiro representando o valor do URL de pedido para combinar. Se forem especificados vários valores, são avaliados utilizando a lógica OR. |
| Transformação de caso | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todos os pedidos onde a URL de pedido começa com `https://api.contoso.com/customers/123` . Transformamos a extensão do ficheiro de pedido para minúsculas antes de avaliar a correspondência, pelo que os pedidos `https://api.contoso.com/Customers/123` e outras variações de caso também desencadearão esta condição de correspondência.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-url.png" alt-text="Screenshot do portal mostrando condição de correspondência DE URL de pedido.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestUri",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "https://api.contoso.com/customers/123"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestUri'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'https://api.contoso.com/customers/123'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters'
  }
}
```

---

## <a name="operator-list"></a><a name = "operator-list"></a> Lista de operadores

Para as regras que aceitam valores da lista de operadores padrão, os seguintes operadores são válidos:

| Operador                   | Descrição                                                                                                                    | Suporte do modelo ARM                                            |
|----------------------------|--------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Qualquer                        | Corresponde quando há algum valor, independentemente do que seja.                                                                     | `operator`: `Any`                                               |
| Igual                      | Corresponde quando o valor corresponde exatamente à cadeia especificada.                                                                   | `operator`: `Equal`                                             |
| Contains                   | Corresponde quando o valor contém a cadeia especificada.                                                                          | `operator`: `Contains`                                          |
| Menos de                  | Corresponde quando o comprimento do valor é inferior ao número inteiro especificado.                                                       | `operator`: `LessThan`                                          |
| Maior Que               | Corresponde quando o comprimento do valor é maior do que o inteiro especificado.                                                    | `operator`: `GreaterThan`                                       |
| Menos ou igual         | Corresponde quando o comprimento do valor é inferior ou igual ao número inteiro especificado.                                           | `operator`: `LessThanOrEqual`                                   |
| Maior do que ou igual      | Corresponde quando o comprimento do valor é maior ou igual ao número inteiro especificado.                                        | `operator`: `GreaterThanOrEqual`                                |
| Começa Por                | Corresponde quando o valor começa com a cadeia especificada.                                                                       | `operator`: `BeginsWith`                                        |
| Termina Com                  | Corresponde quando o valor termina com a cadeia especificada.                                                                         | `operator`: `EndsWith`                                          |
| RegEx                      | Corresponde quando o valor corresponde à expressão regular especificada. [Veja abaixo mais detalhes.](#regular-expressions)        | `operator`: `RegEx`                                             |
| Não Qualquer                    | Corresponde quando não há valor.                                                                                                | `operator`: `Any` `negateCondition` e: `true`                |
| Não igual                  | Corresponde quando o valor não corresponde à cadeia especificada.                                                                    | `operator`: `Equal` `negateCondition` e: `true`              |
| Não contém               | Corresponde quando o valor não contém a cadeia especificada.                                                                  | `operator`: `Contains` `negateCondition` e: `true`           |
| Não menos que              | Corresponde quando o comprimento do valor não é inferior ao número inteiro especificado.                                                   | `operator`: `LessThan` `negateCondition` e: `true`           |
| Não maior do que           | Corresponde quando o comprimento do valor não é maior do que o inteiro especificado.                                                | `operator`: `GreaterThan` `negateCondition` e: `true`        |
| Não menos ou igual     | Corresponde quando o comprimento do valor não é inferior ou igual ao número inteiro especificado.                                       | `operator`: `LessThanOrEqual` `negateCondition` e: `true`    |
| Não maior do que ou iguais | Corresponde quando o comprimento do valor não é maior ou igual ao número inteiro especificado.                                    | `operator`: `GreaterThanOrEqual` `negateCondition` e: `true` |
| Não começa com            | Corresponde quando o valor não começa com a cadeia especificada.                                                               | `operator`: `BeginsWith` `negateCondition` e: `true`         |
| Não termina com              | Corresponde quando o valor não termina com a cadeia especificada.                                                                 | `operator`: `EndsWith` `negateCondition` e: `true`           |
| Não RegEx                  | Corresponde quando o valor não corresponde à expressão regular especificada. [Veja abaixo mais detalhes.](#regular-expressions) | `operator`: `RegEx` `negateCondition` e: `true`              |

> [!TIP]
> Para operadores numéricos como *Menos do que* e Maior do que ou *iguais,* a comparação utilizada baseia-se no comprimento. O valor na condição de jogo deve ser um número inteiro que especifique o comprimento que pretende comparar.

### <a name="regular-expressions"></a><a name="regular-expressions"></a> Expressões regulares

Expressões regulares não suportam as seguintes operações:

* Retroreferências e captura de subexpressões.
* Afirmações arbitrárias de zero largura.
* Referências subroutinas e padrões recursivos.
* Padrões condicionais.
* Verbos de controlo de retrocesso.
* A `\C` diretiva "byte único".
* A `\R` nova diretiva relativa à correspondência.
* O `\K` início da diretiva de reset de partidas.
* Chamadas e código incorporado.
* Agrupamentos atómicos e quantificadores possessivos.

## <a name="arm-template-support"></a>Suporte do modelo ARM

Os conjuntos de regras podem ser configurados usando modelos do Gestor de Recursos Azure. [Consulte um modelo de exemplo.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set) Pode adicionar condições de correspondência utilizando os snippets JSON ou Bicep incluídos nos exemplos acima.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o Conjunto de Regras.](concept-rule-set.md)
* Saiba como [configurar o seu primeiro Conjunto de Regras](how-to-configure-rule-set.md).
* Saiba mais sobre [as ações do Conjunto de Regras](concept-rule-set-actions.md).
