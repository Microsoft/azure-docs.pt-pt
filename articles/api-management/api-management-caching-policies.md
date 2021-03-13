---
title: Azure API Management caching políticas | Microsoft Docs
description: Conheça as políticas de caching disponíveis para utilização na Azure API Management. Veja exemplos e veja recursos adicionais disponíveis.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 03/08/2021
ms.author: apimpm
ms.openlocfilehash: 9888627bed0fbf90abc75c81564dacc0d1aac18e
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103233471"
---
# <a name="api-management-caching-policies"></a>Políticas de colocação em cache da Gestão de API
Este tópico fornece uma referência para as seguintes políticas de Gestão da API. Para obter informações sobre políticas de adição e configuração, consulte [Políticas em Gestão de API.](./api-management-policies.md)

> [!IMPORTANT]
> A cache incorporada é volátil e é partilhada por todas as unidades da mesma região no mesmo serviço de Gestão API.

## <a name="caching-policies"></a><a name="CachingPolicies"></a> Política de caching

- Políticas de caching de resposta
    - [Obter de cache](#GetFromCache) - Execute cache olhar para cima e devolva uma resposta em cache válida quando disponível.
    - [Armazenar para cache](#StoreToCache) - Respostas caches de acordo com a configuração de controlo de cache especificada.
- Políticas de caching de valor
    - [Obtenha valor a partir de cache](#GetFromCacheByKey) - Recupere um item em cache por chave.
    - [Valor da loja em cache](#StoreToCacheByKey) - Guarde um item na cache por chave.
    - [Remover o valor da cache](#RemoveCacheByKey) - Remova um item na cache por chave.

## <a name="get-from-cache"></a><a name="GetFromCache"></a> Obter de cache
Utilize a `cache-lookup` política para efetuar cache olhar para cima e devolver uma resposta em cache válida quando disponível. Esta política pode ser aplicada nos casos em que o conteúdo da resposta permanece estático durante um período de tempo. O caching de resposta reduz a largura de banda e os requisitos de processamento impostos no servidor web backend e reduz a latência percebida pelos consumidores da API.

> [!NOTE]
> Esta política deve ter uma loja correspondente à política [de cache.](#StoreToCache)

### <a name="policy-statement"></a>Declaração política

```xml
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" caching-type="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">
  <vary-by-header>Accept</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Accept-Charset</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Authorization</vary-by-header>
  <!-- should be present when allow-private-response-caching is "true"-->
  <vary-by-header>header name</vary-by-header>
  <!-- optional, can repeated several times -->
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>
  <!-- optional, can repeated several times -->
</cache-lookup>
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" caching-type="internal" >
            <vary-by-query-parameter>version</vary-by-query-parameter>
        </cache-lookup>
    </inbound>
    <outbound>
        <cache-store duration="seconds" />
        <base />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Exemplo usando expressões políticas
Este exemplo mostra como configurar a duração da resposta da API Management que corresponda ao caching de resposta do serviço de backend, conforme especificado pela diretiva do serviço `Cache-Control` apoiado. Para uma demonstração de configuração e utilização desta política, consulte [cloud cover episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e fast-forward to 25:25.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Para obter mais informações, consulte [as expressões de política](api-management-policy-expressions.md) e a [variável Context.](api-management-policy-expressions.md#ContextVariables)

### <a name="elements"></a>Elementos

|Nome|Descrição|Obrigatório|
|----------|-----------------|--------------|
|cache-lookup|Elemento de raiz.|Yes|
|variar por cabeçalho|Comece a caching respostas por valor de cabeçalho especificado, tais como Aceitar, Aceitar Charset, Aceitar Codificar, Aceitar Linguagem, Autorização, Esperar, De, Anfitrião, If-Match.|No|
|variar por consulta-parâmetro|Comece a caching respostas por valor de parâmetros de consulta especificados. Introduza um único ou vários parâmetros. Use o ponto e vírgula como separador. Se nenhum for especificado, todos os parâmetros de consulta são utilizados.|No|

### <a name="attributes"></a>Atributos

| Nome                           | Descrição                                                                                                                                                                                                                                                                                                                                                 | Obrigatório | Predefinição           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| permitir-resposta privada-caching | Quando definido `true` para , permite o caching de pedidos que contêm um cabeçalho de autorização.                                                                                                                                                                                                                                                                        | No       | false             |
| tipo caching               | Escolha entre os seguintes valores do atributo:<br />- `internal` para utilizar a cache de gestão da API incorporada,<br />- `external` utilizar a cache externa tal como descrito na [Utilização de uma cache Azure externa para redis na gestão API Azure](api-management-howto-cache-external.md),<br />- `prefer-external` utilizar cache externo se configurar ou cache interna de outra forma. | No       | `prefer-external` |
| a jusante-tipo de caching        | Este atributo deve ser definido para um dos seguintes valores.<br /><br /> - nenhum - não é permitido o caching a jusante.<br />- é permitido um caching privado a jusante.<br />- é permitido um caching público - privado e partilhado a jusante.                                                                                                          | No       | nenhum              |
| deve-revalidar                | Quando o cache a jusante é ativado, este atributo liga ou desliga a diretiva relativa ao controlo de `must-revalidate` caches em respostas de gateway.                                                                                                                                                                                                                      | No       | true              |
| variar por desenvolvedor              | Configurar `true` para cache respostas por conta de desenvolvedor que detém [a chave de subscrição](./api-management-subscriptions.md) incluída no pedido.                                                                                                                                                                                                                                                                                                  | Yes      |         Falso          |
| variar-por-desenvolvedor-grupos       | Configurar `true` para cache respostas por grupo de [utilizador](./api-management-howto-create-groups.md).                                                                                                                                                                                                                                                                                                             | Yes      |       Falso            |

### <a name="usage"></a>Utilização
Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

- **Secções políticas:** entrada
- **Âmbitos de política:** todos os âmbitos

## <a name="store-to-cache"></a><a name="StoreToCache"></a> Armazenar para cache
A `cache-store` política caches respostas de acordo com as definições de cache especificadas. Esta política pode ser aplicada nos casos em que o conteúdo da resposta permanece estático durante um período de tempo. O caching de resposta reduz a largura de banda e os requisitos de processamento impostos no servidor web backend e reduz a latência percebida pelos consumidores da API.

> [!NOTE]
> Esta política deve ter uma política de obter correspondente da política [de cache.](api-management-caching-policies.md#GetFromCache)

### <a name="policy-statement"></a>Declaração política

```xml
<cache-store duration="seconds" cache-response="true | false" />
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">
            <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
        </cache-lookup>
    </inbound>
    <outbound>
        <base />
        <cache-store duration="3600" />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Exemplo usando expressões políticas
Este exemplo mostra como configurar a duração da resposta da API Management que corresponda ao caching de resposta do serviço de backend, conforme especificado pela diretiva do serviço `Cache-Control` apoiado. Para uma demonstração de configuração e utilização desta política, consulte [cloud cover episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e fast-forward to 25:25.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Para obter mais informações, consulte [as expressões de política](api-management-policy-expressions.md) e a [variável Context.](api-management-policy-expressions.md#ContextVariables)

### <a name="elements"></a>Elementos

|Nome|Descrição|Obrigatório|
|----------|-----------------|--------------|
|cache-loja|Elemento de raiz.|Yes|

### <a name="attributes"></a>Atributos

| Nome             | Descrição                                                                                                                                                                                                                                                                                                                                                 | Obrigatório | Predefinição           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| duration         | Tempo de vida das entradas em cache, especificadas em segundos.     | Yes      | N/D               |
| cache-resposta         | Definir para ser verdadeiro para cache a resposta HTTP atual. Se o atributo for omitido ou definido como falso, apenas respostas HTTP com o código de estado `200 OK` são em cache.                           | No      | false               |

### <a name="usage"></a>Utilização
Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

- **Secções políticas:** saída
- **Âmbitos de política:** todos os âmbitos

## <a name="get-value-from-cache"></a><a name="GetFromCacheByKey"></a> Obtenha valor a partir de cache
Utilize a `cache-lookup-value` política para efetuar a procura de cache por chave e devolva um valor em cache. A chave pode ter um valor de cadeia arbitrária e é normalmente fornecida usando uma expressão política.

> [!NOTE]
> Esta política deve ter um valor de Loja correspondente na política [de cache.](#StoreToCacheByKey)

### <a name="policy-statement"></a>Declaração política

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Exemplo
Para obter mais informações e exemplos desta política, consulte [o caching personalizado na Azure API Management.](./api-management-sample-cache-by-key.md)

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Elementos

|Nome|Descrição|Obrigatório|
|----------|-----------------|--------------|
|cache-lookup-value|Elemento de raiz.|Yes|

### <a name="attributes"></a>Atributos

| Nome             | Descrição                                                                                                                                                                                                                                                                                                                                                 | Obrigatório | Predefinição           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| tipo caching | Escolha entre os seguintes valores do atributo:<br />- `internal` para utilizar a cache de gestão da API incorporada,<br />- `external` utilizar a cache externa tal como descrito na [Utilização de uma cache Azure externa para redis na gestão API Azure](api-management-howto-cache-external.md),<br />- `prefer-external` utilizar cache externo se configurar ou cache interna de outra forma. | No       | `prefer-external` |
| valor por defeito    | Um valor que será atribuído à variável se o lookup da chave cache resultar numa falha. Se este atributo não for especificado, `null` é atribuído.                                                                                                                                                                                                           | No       | `null`            |
| key              | Valor chave cache para usar na procura.                                                                                                                                                                                                                                                                                                                       | Yes      | N/D               |
| nome variável    | Nome da [variável](api-management-policy-expressions.md#ContextVariables) de contexto a que o valor de procura será atribuído, se a pesquisa for bem sucedida. Se a procura resultar num erro, a variável será atribuída ao valor do `default-value` atributo `null` ou, se o `default-value` atributo for omitido.                                       | Yes      | N/D               |

### <a name="usage"></a>Utilização
Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

- **Secções políticas:** entrada, saída, backend, erro
- **Âmbitos de política:** todos os âmbitos

## <a name="store-value-in-cache"></a><a name="StoreToCacheByKey"></a> Valor da loja em cache
O `cache-store-value` armazenamento de cache executa por chave. A chave pode ter um valor de cadeia arbitrária e é normalmente fornecida usando uma expressão política.

> [!NOTE]
> O funcionamento do armazenamento do valor em cache realizado por esta política é assíncronos. O valor armazenado pode ser recuperado usando Obter valor da política [cache.](#GetFromCacheByKey) No entanto, o valor armazenado pode não estar imediatamente disponível para recuperação, uma vez que a operação assíncronea que armazena o valor em cache ainda pode estar em curso. 

### <a name="policy-statement"></a>Declaração política

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Exemplo
Para obter mais informações e exemplos desta política, consulte [o caching personalizado na Azure API Management.](./api-management-sample-cache-by-key.md)

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Elementos

|Nome|Descrição|Obrigatório|
|----------|-----------------|--------------|
|valor da loja de cache|Elemento de raiz.|Yes|

### <a name="attributes"></a>Atributos

| Nome             | Descrição                                                                                                                                                                                                                                                                                                                                                 | Obrigatório | Predefinição           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| tipo caching | Escolha entre os seguintes valores do atributo:<br />- `internal` para utilizar a cache de gestão da API incorporada,<br />- `external` utilizar a cache externa tal como descrito na [Utilização de uma cache Azure externa para redis na gestão API Azure](api-management-howto-cache-external.md),<br />- `prefer-external` utilizar cache externo se configurar ou cache interna de outra forma. | No       | `prefer-external` |
| duration         | O valor será em cache para o valor de duração fornecido, especificado em segundos.                                                                                                                                                                                                                                                                                 | Yes      | N/D               |
| key              | Cache chave o valor será armazenado sob.                                                                                                                                                                                                                                                                                                                   | Yes      | N/D               |
| valor            | O valor a ser em cache.                                                                                                                                                                                                                                                                                                                                     | Yes      | N/D               |
### <a name="usage"></a>Utilização
Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

- **Secções políticas:** entrada, saída, backend, erro
- **Âmbitos de política:** todos os âmbitos

## <a name="remove-value-from-cache"></a><a name="RemoveCacheByKey"></a> Remover valor da cache
A `cache-remove-value` elimina um item em cache identificado pela sua chave. A chave pode ter um valor de cadeia arbitrária e é normalmente fornecida usando uma expressão política.

#### <a name="policy-statement"></a>Declaração política

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>Exemplo

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Elementos

|Nome|Descrição|Obrigatório|
|----------|-----------------|--------------|
|cache-remove-valor|Elemento de raiz.|Yes|

#### <a name="attributes"></a>Atributos

| Nome             | Descrição                                                                                                                                                                                                                                                                                                                                                 | Obrigatório | Predefinição           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| tipo caching | Escolha entre os seguintes valores do atributo:<br />- `internal` para utilizar a cache de gestão da API incorporada,<br />- `external` utilizar a cache externa tal como descrito na [Utilização de uma cache Azure externa para redis na gestão API Azure](api-management-howto-cache-external.md),<br />- `prefer-external` utilizar cache externo se configurar ou cache interna de outra forma. | No       | `prefer-external` |
| key              | A chave do valor anteriormente em cache a ser removida da cache.                                                                                                                                                                                                                                                                                        | Yes      | N/D               |

#### <a name="usage"></a>Utilização
Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

- **Secções políticas:** entrada, saída, backend, erro
- **Âmbitos de política:** todos os âmbitos

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as políticas, consulte:

+ [Políticas em Gestão de API](api-management-howto-policies.md)
+ [Transformar APIs](transform-api.md)
+ [Referência política](./api-management-policies.md) para uma lista completa de declarações políticas e suas definições
+ [Exemplos de Políticas](./policy-reference.md)