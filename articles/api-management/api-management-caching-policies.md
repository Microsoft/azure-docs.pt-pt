---
title: Políticas de cache da Azure API Management Microsoft Docs
description: Conheça as políticas de cache disponíveis para utilização na Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 06c4ede12f939e48973d3e0b502d90b848d199bb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280303"
---
# <a name="api-management-caching-policies"></a>Políticas de cache da API Management
Este tópico fornece uma referência para as seguintes políticas de Gestão da API. Para obter informações sobre a adição e configuração de políticas, consulte [Políticas na Gestão da API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="CachingPolicies"></a>Políticas de caching

- Políticas de cache de resposta
    - [Obtenha a partir da cache](api-management-caching-policies.md#GetFromCache) - Execute a cache procure e devolva uma resposta em cache válida quando disponível.
    - [Armazenar em cache](api-management-caching-policies.md#StoreToCache) - Respostas caches de acordo com a configuração especificada de controlo de cache.
- Políticas de cache de valor
    - [Obtenha valor a partir de cache](#GetFromCacheByKey) - Recupere um item em cache por chave.
    - [Valor de loja em cache](#StoreToCacheByKey) - Guarde um item na cache por chave.
    - [Retire o valor da cache](#RemoveCacheByKey) - Retire um item na cache por chave.

## <a name="GetFromCache"></a>Obter da cache
Utilize a política `cache-lookup` para realizar a procura de cache e devolva uma resposta em cache válida quando disponível. Esta política pode ser aplicada nos casos em que o conteúdo da resposta permanece estático durante um período de tempo. O cache de resposta reduz a largura de banda e os requisitos de processamento impostos no servidor web backend e reduz a latência percebida pelos consumidores de API.

> [!NOTE]
> Esta política deve ter uma política de depósito correspondente [à cache.](api-management-caching-policies.md#StoreToCache)

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
Este exemplo mostra como configurar a duração da resposta da API Management que corresponde ao cache de resposta do serviço backend, conforme especificado pela diretiva `Cache-Control` do serviço apoiado. Para uma demonstração de configuração e utilização desta política, consulte [cloud cover Episódio 177: Mais Funcionalidades de Gestão aPI com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avance para as 25:25.

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

Para mais informações, consulte [expressões políticas](api-management-policy-expressions.md) e [variável de contexto.](api-management-policy-expressions.md#ContextVariables)

### <a name="elements"></a>Elementos

|Nome|Descrição|Required|
|----------|-----------------|--------------|
|cache-lookup|Elemento de raiz.|Sim|
|variar por cabeçalho|Inicie respostas de cache por valor de cabeçalho especificado, tais como Aceitar, Aceitar-Charset, Aceitar-Codificar, Aceitar-Linguagem, Autorização, Esperar, From, Anfitrião, If-Match.|Não|
|vary-by-query-parameter|Inicie respostas de cache por valor de parâmetros de consulta especificados. Introduza um único ou múltiplo parâmetros. Use o ponto evíplo como separador. Se nenhum for especificado, todos os parâmetros de consulta são utilizados.|Não|

### <a name="attributes"></a>Atributos

| Nome                           | Descrição                                                                                                                                                                                                                                                                                                                                                 | Required | Predefinição           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| allow-private-response-caching | Quando definido para `true`, permite o cache de pedidos que contenham um cabeçalho de Autorização.                                                                                                                                                                                                                                                                        | Não       | false             |
| tipo de cache               | Escolha entre os seguintes valores do atributo:<br />- `internal` utilizar o cache de Gestão aPI incorporado,<br />- `external` utilizar a cache externa conforme descrito na [Utilização de um Cache Azure externo para Redis em Gestão aPI Azure,](api-management-howto-cache-external.md)<br />- `prefer-external` utilizar cache externo se estiver configurado ou interno em contrário. | Não       | `prefer-external` |
| downstream-caching-type        | Este atributo deve ser fixado num dos seguintes valores.<br /><br /> - nenhum - caching a jusante não é permitido.<br />- privado - caching privado a jusante é permitido.<br />- público - privado e partilhado caching a jusante é permitido.                                                                                                          | Não       | nenhuma              |
| deve revlidar                | Quando o caching a jusante é ativado, este atributo liga ou desliga a diretiva `must-revalidate` controlo de cache em respostas de gateway.                                                                                                                                                                                                                      | Não       | true              |
| variar por desenvolvedor              | Definir para `true` respostas de cache por [chave de subscrição](https://docs.microsoft.com/azure/api-management/api-management-subscriptions).                                                                                                                                                                                                                                                                                                         | Sim      |         Falso          |
| grupos variados por desenvolvedores       | Definir para `true` respostas de cache por [grupo de utilizador](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups).                                                                                                                                                                                                                                                                                                             | Sim      |       Falso            |

### <a name="usage"></a>Utilização
Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

- **Secções políticas:** entrada
- **Âmbitos de política:** todos os âmbitos

## <a name="StoreToCache"></a>Armazenar em cache
A `cache-store` respostas de caches de política de acordo com as definições de cache especificadas. Esta política pode ser aplicada nos casos em que o conteúdo da resposta permanece estático durante um período de tempo. O cache de resposta reduz a largura de banda e os requisitos de processamento impostos no servidor web backend e reduz a latência percebida pelos consumidores de API.

> [!NOTE]
> Esta política deve ter um Get correspondente da política [de cache.](api-management-caching-policies.md#GetFromCache)

### <a name="policy-statement"></a>Declaração política

```xml
<cache-store duration="seconds" />
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
Este exemplo mostra como configurar a duração da resposta da API Management que corresponde ao cache de resposta do serviço backend, conforme especificado pela diretiva `Cache-Control` do serviço apoiado. Para uma demonstração de configuração e utilização desta política, consulte [cloud cover Episódio 177: Mais Funcionalidades de Gestão aPI com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avance para as 25:25.

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

Para mais informações, consulte [expressões políticas](api-management-policy-expressions.md) e [variável de contexto.](api-management-policy-expressions.md#ContextVariables)

### <a name="elements"></a>Elementos

|Nome|Descrição|Required|
|----------|-----------------|--------------|
|cache-loja|Elemento de raiz.|Sim|

### <a name="attributes"></a>Atributos

| Nome             | Descrição                                                                                                                                                                                                                                                                                                                                                 | Required | Predefinição           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| duration         | Tempo de vida das entradas em cache, especificadas em segundos.                                                                                                                                                                                                                                                                                                   | Sim      | N/A               |

### <a name="usage"></a>Utilização
Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

- **Secções políticas:** saída
- **Âmbitos de política:** todos os âmbitos

## <a name="GetFromCacheByKey"></a>Obter valor a partir de cache
Utilize a política `cache-lookup-value` para realizar a procura em cache por chave e devolver um valor em cache. A chave pode ter um valor de cadeia arbitrário e é normalmente fornecida usando uma expressão política.

> [!NOTE]
> Esta política deve ter um valor de loja correspondente na política [de cache.](#StoreToCacheByKey)

### <a name="policy-statement"></a>Declaração política

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Exemplo
Para mais informações e exemplos desta política, consulte O Cache personalizado [na Gestão da API Azure.](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/)

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Elementos

|Nome|Descrição|Required|
|----------|-----------------|--------------|
|cache-lookup-valor|Elemento de raiz.|Sim|

### <a name="attributes"></a>Atributos

| Nome             | Descrição                                                                                                                                                                                                                                                                                                                                                 | Required | Predefinição           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| tipo de cache | Escolha entre os seguintes valores do atributo:<br />- `internal` utilizar o cache de Gestão aPI incorporado,<br />- `external` utilizar a cache externa conforme descrito na [Utilização de um Cache Azure externo para Redis em Gestão aPI Azure,](api-management-howto-cache-external.md)<br />- `prefer-external` utilizar cache externo se estiver configurado ou interno em contrário. | Não       | `prefer-external` |
| valor padrão    | Um valor que será atribuído à variável se a procura da chave cache resultar em falta. Se este atributo não for especificado, `null` é atribuído.                                                                                                                                                                                                           | Não       | `null`            |
| key              | Valor chave cache para usar na procura.                                                                                                                                                                                                                                                                                                                       | Sim      | N/A               |
| nome variável    | Nome da [variável](api-management-policy-expressions.md#ContextVariables) de contexto a que o valor procurado será atribuído, se a procura for bem sucedida. Se a procura resultar em falta, a variável será atribuída ao valor do atributo ou `null``default-value`, se o atributo `default-value` for omitido.                                       | Sim      | N/A               |

### <a name="usage"></a>Utilização
Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

- **Secções políticas:** entrada, saída, backend, erro
- **Âmbitos de política:** todos os âmbitos

## <a name="StoreToCacheByKey"></a>Valor da loja em cache
O `cache-store-value` executa o armazenamento de cache por chave. A chave pode ter um valor de cadeia arbitrário e é normalmente fornecida usando uma expressão política.

> [!NOTE]
> Esta política deve ter um valor de obter correspondente da política [de cache.](#GetFromCacheByKey)

### <a name="policy-statement"></a>Declaração política

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Exemplo
Para mais informações e exemplos desta política, consulte O Cache personalizado [na Gestão da API Azure.](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/)

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Elementos

|Nome|Descrição|Required|
|----------|-----------------|--------------|
|cache-store-valor|Elemento de raiz.|Sim|

### <a name="attributes"></a>Atributos

| Nome             | Descrição                                                                                                                                                                                                                                                                                                                                                 | Required | Predefinição           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| tipo de cache | Escolha entre os seguintes valores do atributo:<br />- `internal` utilizar o cache de Gestão aPI incorporado,<br />- `external` utilizar a cache externa conforme descrito na [Utilização de um Cache Azure externo para Redis em Gestão aPI Azure,](api-management-howto-cache-external.md)<br />- `prefer-external` utilizar cache externo se estiver configurado ou interno em contrário. | Não       | `prefer-external` |
| duration         | O valor será emcache para o valor de duração fornecido, especificado em segundos.                                                                                                                                                                                                                                                                                 | Sim      | N/A               |
| key              | Chave cache o valor será armazenado abaixo.                                                                                                                                                                                                                                                                                                                   | Sim      | N/A               |
| Valor            | O valor a ser emcache.                                                                                                                                                                                                                                                                                                                                     | Sim      | N/A               |
### <a name="usage"></a>Utilização
Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

- **Secções políticas:** entrada, saída, backend, erro
- **Âmbitos de política:** todos os âmbitos

### <a name="RemoveCacheByKey"></a>Remover valor da cache
O `cache-remove-value` elimina um item em cache identificado pela sua chave. A chave pode ter um valor de cadeia arbitrário e é normalmente fornecida usando uma expressão política.

#### <a name="policy-statement"></a>Declaração política

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>Exemplo

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Elementos

|Nome|Descrição|Required|
|----------|-----------------|--------------|
|cache-remove-value|Elemento de raiz.|Sim|

#### <a name="attributes"></a>Atributos

| Nome             | Descrição                                                                                                                                                                                                                                                                                                                                                 | Required | Predefinição           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| tipo de cache | Escolha entre os seguintes valores do atributo:<br />- `internal` utilizar o cache de Gestão aPI incorporado,<br />- `external` utilizar a cache externa conforme descrito na [Utilização de um Cache Azure externo para Redis em Gestão aPI Azure,](api-management-howto-cache-external.md)<br />- `prefer-external` utilizar cache externo se estiver configurado ou interno em contrário. | Não       | `prefer-external` |
| key              | A chave do valor previamente emcache a ser removido da cache.                                                                                                                                                                                                                                                                                        | Sim      | N/A               |

#### <a name="usage"></a>Utilização
Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de política.

- **Secções políticas:** entrada, saída, backend, erro
- **Âmbitos de política:** todos os âmbitos

## <a name="next-steps"></a>Passos Seguintes

Para mais informações que trabalhem com políticas, consulte:

+ [Políticas em Gestão aPi](api-management-howto-policies.md)
+ [Transforme APIs](transform-api.md)
+ [Referência política](api-management-policy-reference.md) para uma lista completa de declarações políticas e suas configurações
+ [Amostras políticas](policy-samples.md)
